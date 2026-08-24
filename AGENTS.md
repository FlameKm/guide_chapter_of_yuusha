# 勇者之章 Ⅲ · 流程攻略可视化

本仓库将 MC百科 上的无赎/勇者装备流程攻略整理为**静态单页可视化**：按 Boss 章节展示武器/聚晶、护甲、词条、宝石与饰品配图，支持搜索、龙前/龙后筛选、详情弹窗与图片放大。

---

## 目录结构

```
/
├── index.html              # 导航页（三条路线入口）
├── dragon/                 # 无赎弓线
│   ├── index.html          # 页面 + 龙前数据（内联）
│   └── post-dragon-data.js # 龙后数据 → window.MC_POST_DRAGON
├── warrior/                # 勇者战线
│   ├── index.html
│   ├── pre-dragon-data.js  # → window.MC_WARRIOR_PRE
│   └── post-dragon-data.js # → window.MC_WARRIOR_POST
└── mage/                   # 无赎聚晶法
    ├── index.html
    ├── pre-dragon-data.js  # → window.MC_MAGE_PRE
    └── post-dragon-data.js # → window.MC_MAGE_POST
```

**技术栈**：纯 HTML + CSS + 原生 JS，无构建步骤。配图托管于 MC百科 CDN，本地仅存相对路径。

---

## 数据来源（勿混用）

| 路线 | 龙前 | 龙后 | 原作者 |
|------|------|------|--------|
| 无赎弓 `dragon/` | [post/5756](https://www.mcmod.cn/post/5756.html)（3.11.2） | [post/5757](https://www.mcmod.cn/post/5757.html)（3.9.9） | M1tsukasa_Ayase |
| 勇者战 `warrior/` | [post/6413](https://www.mcmod.cn/post/6413.html)（3.13.8） | [post/6423](https://www.mcmod.cn/post/6423.html) | Emra |
| 聚晶法 `mage/` | [post/5742](https://www.mcmod.cn/post/5742.html)（3.9.9） | [post/5745](https://www.mcmod.cn/post/5745.html) | M1tsukasa_Ayase |

整理原则：

- **以原文为准**：Boss 顺序、装备名、打法要点均来自对应帖子；本页是摘要与索引，不能替代原文。
- **路线隔离**：弓/战/法三套数据独立，同一 Boss 的配图路径、武器推荐可能不同，禁止跨路线复制 stage 或 images。
- **版本标注**：页面 header/footer 已链到具体帖子；整合包版本更新时，先核对原文是否改版再改数据。
- **译名差异**：如「凋零/凋灵」等，与原文保持一致即可，不必强行统一。

配图 URL 规则：

```
https://i.mcmod.cn/editor/upload/{相对路径}
```

相对路径从原文 `<img>` 的 `src` 中提取（形如 `20251113/1763026080_1252418_HeGM.webp`）。页面内 `<img>` 使用 `referrerpolicy="no-referrer"`。

---

## 数据写法

### 全局变量

| 文件 | 挂载变量 |
|------|----------|
| `dragon/post-dragon-data.js` | `window.MC_POST_DRAGON` |
| `warrior/pre-dragon-data.js` | `window.MC_WARRIOR_PRE` |
| `warrior/post-dragon-data.js` | `window.MC_WARRIOR_POST` |
| `mage/pre-dragon-data.js` | `window.MC_MAGE_PRE` |
| `mage/post-dragon-data.js` | `window.MC_MAGE_POST` |

龙前弓线数据目前仍**内联**在 `dragon/index.html` 的 `DATA` 与 `STAGE_IMAGES` 中；龙后通过 `<script src="post-dragon-data.js">` 合并。战/法龙前龙后均为外部 JS。

### Pack 结构

每个 `*-data.js`（或弓线龙后 pack）包含：

```js
{
  extraColors: ["#..."],      // 章节配色扩展
  progression: [              // 顶部演进条节点
    { name: "装备名", chapter: 0, type?: "demon" }  // type 仅弓线演进条可选
  ],
  images: {                   // key = stage.id
    "tongbi": ["20251113/....webp"],
    "final_trial": ["...", "..."]
  },
  imageCaptions: {            // 可选，多图时的说明
    "final_trial": ["增伤饰品", "武器配置 1"]
  },
  chapters: [ /* 见下 */ ]
}
```

### 章节与阶段

- **龙前**：第 0–9 章，`phase: "pre"`（可省略，chapter < 10 即视为龙前）
- **龙后**：第 10–20 章，`phase: "post"`
- 每章含 `chapter`、`title`、`subtitle`、`stages[]`
- 每个 stage 必须有唯一 **`id`**（英文 snake_case，与 `images` 的 key 对应）

### Stage 字段

| 字段 | 弓线 | 战线 | 法线 | 说明 |
|------|------|------|------|------|
| `name` | ✓ | ✓ | ✓ | Boss 或 prep 名称 |
| `bow` | ✓ | — | — | 弓与『封弓』『印弓』『妖弓』『魔弓』标签 |
| `arrows` | ✓ | — | — | 箭矢 |
| `weapon` | — | ✓ | 兼容 | 主武器；法线可用 `crystal` + `wand` |
| `crystal` | — | — | ✓ | 聚晶 |
| `wand` | — | — | ✓ | 魔杖 |
| `armor` | ✓ | ✓ | ✓ | 护甲 |
| `affixes` | ✓ | ✓ | ✓ | 词条 |
| `gems` | ✓ | ✓ | ✓ | 宝石 |
| `accessories` | ✓ | ✓ | ✓ | 饰品文字说明 |
| `tips` | ✓ | ✓ | ✓ | 打法要点（字符串数组） |
| `farm` / `craft` / `warn` | ✓ | ✓ | ✓ | 刷材料 / 合成 / 警告 |
| `prep` | ✓ | ✓ | ✓ | 开局准备关，卡片样式不同 |
| `optional` | ✓ | ✓ | ✓ | 可跳过（如聚晶法「滑行魔石」） |
| `dpsCheck` | ✓ | ✓ | ✓ | DPS 检测点标记 |
| `finale` | ✓ | ✓ | ✓ | 终局关高亮 |

`mergePack()` 会把 pack 合并进页面：`images` 自动拼 CDN 前缀；`chapters` 追加到 `DATA`。

### 新增 Boss 步骤（战/法/弓龙后）

1. 在原文找到该 Boss 段落与配图，确认 **stage.id**（沿用已有 id 或新建，与 `images` key 一致）。
2. 在对应章节的 `stages` 数组中按流程顺序插入对象。
3. 在 pack 的 `images[id]` 填入配图路径（可多图）。
4. 若改动了关键装备节点，同步更新 `progression`。
5. 校验：`node --check path/to/data.js`
6. 浏览器打开对应路线页，搜索 Boss 名、点卡片与图片确认渲染。

### 新增整条路线

1. 复制 `warrior/` 或 `mage/` 为模板（推荐法线作聚晶类模板，战线作近战模板）。
2. 改 `--accent` 主题色、header 标题、legend、演进条文案、`mergePack` 变量名。
3. 新建 `pre-dragon-data.js` / `post-dragon-data.js`，在 `index.html` 中 `<script src>` 引用。
4. 在根 `index.html` 增加导航卡片。

---

## 给使用者（玩家）

- **用途**：快速查「这一章该用什么装备、饰品怎么配」，配合原文链接使用。
- **龙前 / 龙后**：工具栏 Tab 可筛选；第 10 章起为龙后内容。
- **搜索**：支持 Boss 名、装备名、饰品关键词。
- **配图**：卡片缩略图与详情内均可点击放大；部分 Boss 与原文共用同一张配置图，属正常现象。
- **免责**：整合包版本、数值、Mod 变动可能导致与原文不一致；以游戏内实测与 MC百科最新帖为准。

---

## 给整理者与 Agent 的注意事项

### 内容

- 复杂流程（如科妮莉亚船长 + 女仆刷终焉之尘、神化附魔顺序）应保留在 `tips` / `warn` 中，不要过度压缩到一行。
- `accessories` 中避免保留「见原文第 N 图」类占位（页面已有配图）；`cleanAccessoryText()` 会尝试剥掉此类文字。
- 同一章节多个 Boss 复用配图时，可以指向相同路径，但 **id 必须各自独立**。
- 弓线『封弓/印弓/妖弓/魔弓』须使用全角书名号，页面才会渲染彩色标签。

### 技术

- **不要用构建工具**：保持可直接 `python3 -m http.server` 或通过任意静态托管打开。
- **修改 index.html 时**：战/法/弓三页逻辑高度相似；改交互或样式时考虑是否需同步三处（或日后抽公共片段——当前未抽离，勿擅自大重构除非用户要求）。
- **dragon 龙前**：约 1700 行内联数据，编辑时优先小范围 diff；长期可考虑外置为 `pre-dragon-data.js` 与战/法对齐。
- **JSON 合法性**：`*-data.js` 使用 `window.XXX = { ... }` 对象字面量；注意尾逗号、引号转义。提交前运行 `node --check`。
- **版权**：攻略文字与配图版权归 MC百科作者与上传者；本仓库为整理展示，外链原文，勿将配图批量镜像到其他 CDN。

### 本地预览

```bash
cd /workspace/mc
python3 -m http.server 8766
# 打开 http://127.0.0.1:8766/
```

### 常见坑

| 问题 | 原因 / 处理 |
|------|-------------|
| 图片不显示 | 路径错误或 CDN 限 referer；检查 `images` 路径与 `referrerpolicy` |
| 卡片无配图 | `stage.id` 与 `images` 的 key 不一致 |
| 龙后内容缺失 | 未加载 `post-dragon-data.js` 或全局变量名不匹配 |
| 搜索不到 | 字段为数组时仅部分文本参与搜索；检查 `tips` 等是否写入 |
| 混线数据 | 从其他路线复制 stage 未改装备字段 |

---

## 相关链接

- [MC百科 · 勇者之章 Ⅲ 整合包](https://www.mcmod.cn/modpack/125.html)（如包页有更新，从这里找最新攻略帖）
