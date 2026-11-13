# 产品场景图生成器

**Product Scenario Illustration Kit** — 一套用 HTML/CSS 复刻的「扁平矢量 UI 示意图」视觉系统。
给一个标题，产出一张 1760×1120 的场景图；同一个标题反复生成，结果完全一致。

![canvas](https://img.shields.io/badge/canvas-1760%C3%971120-4A9DFC?style=flat-square)
![license](https://img.shields.io/badge/license-MIT-7C6BF5?style=flat-square)
![deps](https://img.shields.io/badge/dependencies-none-4A9DFC?style=flat-square)
![agent](https://img.shields.io/badge/skill-ready-7C6BF5?style=flat-square)

<img src="examples/03-diagram-canvas.png" alt="示例：智能图表绘制" width="100%">

---

## 这是什么

一组风格统一的产品功能配图，加上**把这组图复刻出来所需的全部东西**：色值与几何的 token 表、三种版式骨架、可直接改的 HTML 模板、一个零依赖的渲染脚本，以及一个给 AI Agent 用的 skill。

它解决的是很具体的问题：一个产品有二十个功能点，要做二十张风格一致的配图放进 PPT、官网和产品文档。找设计师太慢，用 AI 生图每次风格都不一样、中文还会糊。于是把风格**固化成代码**——模板只有一套壳，内容随便换，出来的图永远属于同一个家族。

| 项 | 值 |
|---|---|
| 输出尺寸 | 1760 × 1120（11:7） |
| 技术栈 | HTML + CSS + 内联 SVG |
| 渲染器 | Edge / Chrome / Chromium headless |
| 依赖 | **无**（`render.py` 只用 Python 标准库） |
| 单张耗时 | 约 2 秒 |

## 效果

<table>
<tr>
<td width="33%"><img src="examples/01-document-diagram-generation.png" alt="文档配图生成"><br><sub><b>左→右流程 + 四联输出</b><br>一句话生成时序图/ER 图/状态机/拓扑图</sub></td>
<td width="33%"><img src="examples/02-architecture-evolution.png" alt="架构局部迭代"><br><sub><b>界面隐喻 · 选中态</b><br>只改一块，其余布局与样式不动</sub></td>
<td width="33%"><img src="examples/03-diagram-canvas.png" alt="智能图表绘制"><br><sub><b>界面隐喻 · 画布编辑</b><br>自然语言生成 + 局部修改</sub></td>
</tr>
</table>

三张图不是手绘的，也不是 AI 生图，是 `examples/src/` 里那三个 HTML 文件被浏览器渲染出来的。改一行文字、重新渲染，就得到新的一张。

## 风格特征

一句话定性：**扁平矢量 UI 示意图**。用卡片层叠造深度、用渐变图标瓦片承载主体、用虚线表达流程、用骨架条代替真实内容。**无人物、无透视、无照片质感。**

| 维度 | 特征 |
|---|---|
| **构图** | 三层嵌套卡片逐层向右下偏移 8–14px（唯一的深度来源）+ 1–2 个破框浮标 |
| **配色** | 色相锁死 200°–250°，主蓝 `#4A9DFC`、紫端 `#7C6BF5`；唯一暖色 `#F58A8A` 只表告警 |
| **光影** | 无方向性光源、无高光、无 AO；只有三级层高投影，色是**深蓝** `rgba(31,71,141,…)` 而非黑 |
| **材质** | 全哑光平涂；唯一"实体感"是渐变图标瓦片 |
| **视角** | 严格正交正视 0° 透视，所有矩形轴对齐 |
| **氛围** | 洁净、克制、可信 —— 企业级 SaaS 技术说明书气质，讲"怎么运作"而非"多酷" |
| **密度** | 中低，一图一件事 |

完整的分析见 [`docs/style-guide.html`](docs/style-guide.html)（可视化版，10 节：结构剖面、版式骨架、色卡面积占比、9 个零件实物复刻、标题→版式映射、提示词模板、自检清单），可执行版见 [`docs/style-spec.md`](docs/style-spec.md)。

## 为什么用 HTML 渲染，而不是 AI 生图

这类图有三个硬指标，扩散模型目前都过不了：

1. **文字必须清晰**。「接入层」「一句话生成」这类标签是信息主体，生图会画成伪文字；
2. **1px 描边必须直**。模型画出来的卡片边缘会抖、圆角会不圆；
3. **必须可复现**。同一标题今天生成和下周生成要一模一样，才好批量维护。

HTML 渲染把这三件事都变成确定性的：文字是真文字、描边是真 1px、结果是代码的函数。代价是需要一次性把版式搭出来——而这一步已经做完了。

> AI 生图仍然有用：做无文字的氛围底图、做封面插画。但主体示意图建议走本项目的渲染管线。

## 项目结构

```
.
├── README.md
├── LICENSE
├── .gitignore
├── docs/
│   ├── style-spec.md            # 可执行规范：token 表 + 标题→版式映射 + 自检
│   └── style-guide.html         # 可视化风格指南（浏览器打开）
├── examples/
│   ├── 01-document-diagram-generation.png
│   ├── 02-architecture-evolution.png
│   ├── 03-diagram-canvas.png
│   └── src/
│       ├── 01-document-diagram-generation.html
│       ├── 02-architecture-evolution.html
│       └── 03-diagram-canvas.html
├── skill/
│   └── hwcloud-scene-image/     # 给 AI Agent 用的 skill（见下）
│       ├── SKILL.md
│       ├── references/
│       │   ├── style-tokens.md  # 色值/字号/圆角/投影完整表
│       │   ├── layouts.md       # 三种版式骨架 + 元素配方
│       │   └── checklist.md     # 交付前 12 条自检
│       ├── assets/
│       │   ├── _blank-shell.html      # 只有壳层，从零搭版式用
│       │   ├── template-b-flow.html   # B 左→右流程
│       │   ├── template-c-ui.html     # C 界面隐喻·画布编辑
│       │   └── template-c-arch.html   # C 界面隐喻·选中态
│       └── scripts/
│           └── render.py        # HTML → 1760×1120 PNG
└── samples/                     # 原始参考样本（未随仓库分发，原因见文末）
```

## 快速开始

**环境**：Python 3.7+，本机装有 Edge / Chrome / Chromium 任意一个。不需要 `pip install`。

```bash
git clone <your-repo-url>
cd <repo>
```

### 1. 复制一份模板

```bash
cp skill/hwcloud-scene-image/assets/template-c-ui.html my-scene.html
```

### 2. 改文字

打开 `my-scene.html`，只需要改这几处：

```html
<div class="title">智能图表绘制</div>
                          <!-- ↑ 4–6 个汉字，别照搬长标题 -->
```

```html
<div class="txt">画一张订单支付的架构图，包含网关、服务与数据库</div>
                          <!-- ↑ 换成真实例句，不要写"示例文本" -->
```

```html
<div class="badge" style="top:526px;">
  ...
  <div class="txt">一句话生成</div>
                          <!-- ↑ 4–6 字，动词开头或承诺句 -->
</div>
```

**壳层 CSS 一行都不要动**——`.shell` / `.panel` / `.content` 的坐标就是这套风格的骨架。

### 3. 渲染

```bash
python skill/hwcloud-scene-image/scripts/render.py my-scene.html out.png
```

```
[1/4] 浏览器: C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe
[2/4] 临时目录: C:\_scene_zt4t6dsw
[3/4] 渲染中 …
[4/4] 输出: out.png  1760x1120  90 KB
```

脚本会先把 HTML 复制到**纯 ASCII 临时目录**再截图——浏览器 headless 在中文路径下会静默失败、输出空白或旧图，这是踩过的坑。渲染完还会校验像素尺寸，不对直接报错。

浏览器不在默认位置时：

```bash
SCENE_BROWSER=/path/to/chrome python scripts/render.py in.html out.png
```

## 用 AI Agent 直接出图

`skill/hwcloud-scene-image/` 是一个标准 Agent Skill。装好之后，你只需要说一句「帮我画：XXX」，Agent 会自己选版式、套模板、渲染、自检。

**安装**（WorkBuddy / Claude Code 等支持 Skill 的环境）：

```bash
cp -r skill/hwcloud-scene-image ~/.workbuddy/skills/
```

放好后重启会话，skill 会被自动索引。

**触发词**：场景图、产品场景图、功能场景图、示意图、配图、卡片式示意图、扁平矢量插画。

**Agent 拿到的内容**：

- **SKILL.md** — 铁律 8 条、固定几何坐标表、5 步工作流、8 个元素零件、9 条常见错误
- **style-tokens.md** — 所有色值、字号、圆角、投影的精确值
- **layouts.md** — 三种版式的 ASCII 骨架图 + 元素配方 + 怎么选
- **checklist.md** — 交付前 12 条核对（含"缩小到 50% 还看得懂吗"这类经验判断）
- **3 套可直接运行的模板** + 1 个空白壳层
- **render.py** — 渲染并校验尺寸

关键是**固定几何写死在文档里**：`shell 110,66,1520×968`、`panel 140,96,1460×908`、`content 180,240,1310×644`、`badge left 1356 宽 284 右缘 1640 破框`。不同 Agent、不同会话、不同时间，出来的图坐标完全一致。

## 风格 Token 速查

### 几何

| 层 | 元素 | left | top | w × h | 圆角 |
|---|---|---|---|---|---|
| L1 | 外壳白卡 | 110 | 66 | 1520 × 968 | 62 |
| L2 | 淡蓝面板 | 140 | 96 | 1460 × 908 | 50 |
| L3 | 内容白卡 | 180 | 240 | 1310 × 620–660 | 34 |
| L4 | 破框浮标 | 1356 | 自定 | 284 × 94 | 24 |
| – | 标题 | 190 | 148 | – | 60px / 600 |

### 主色

| 角色 | 值 |
|---|---|
| 主蓝 / 渐变起点 | `#4A9DFC` |
| 渐变紫端 | `#7C6BF5` |
| 面板渐变 | `#E4F1FF → #E9F4FF → #F2F9FF → #F5FAFF` |
| 骨架条 | `#BCD3EF`（选中 `#A9C7EC`） |
| 连线 / 箭头 | `#BFD6F1` |
| 节点描边 | `#C6D9F1` |
| 文字主 / 次 / 弱 | `#191919` / `#5A616B` / `#71808F` |
| 告警（唯一暖色） | `#F58A8A` |

### 投影

```css
/* 一级：小控件 */ box-shadow:0 4px 12px rgba(31,71,141,.045);
/* 二级：内容卡 */ box-shadow:0 8px 24px rgba(31,71,141,.05);
/* 三级：外壳   */ box-shadow:0 22px 60px rgba(31,71,141,.085), 0 4px 14px rgba(31,71,141,.04);
/* 浮标（最重）*/ box-shadow:0 18px 44px rgba(31,71,141,.15), 0 3px 8px rgba(31,71,141,.05);
/* 渐变瓦片   */ box-shadow:0 16px 34px rgba(74,157,252,.40), 0 0 34px 6px rgba(124,107,245,.16);
```

投影色永远是深蓝，不是黑——这是整张图"干净而不脏"的原因。

## 三种版式怎么选

| 标题特征 | 版式 | 模板 |
|---|---|---|
| 输入→生成→产出、转换、整理、多路输出 | **B 左→右流程** | `template-b-flow.html` |
| 既讲"一句话生成"又讲"生成后能改" | **C 界面隐喻 · 画布编辑** | `template-c-ui.html` |
| 只改一处、其余不动、增量演进 | **C 界面隐喻 · 选中态** | `template-c-arch.html` |
| 一个中心服务 N 个场景 | **A 中心辐射** | 用 `_blank-shell.html` 自搭 |

拿不准就选 C 画布版——界面隐喻最容易读懂，也最不容易画歪。

## 元素零件

| # | 零件 | 关键参数 |
|---|---|---|
| 1 | 渐变图标瓦片 | 104–132px 见方，135° `#4A9DFC→#7C6BF5` + 白色线性图标 + 下缘柔光；**全图恰好 1 个** |
| 2 | 破框浮标 | 白底 + 重投影 + 34px 蓝图标 + 26px 文字，越过卡片右缘 |
| 3 | 骨架条 | `h12 / r6 / #BCD3EF`，每个节点 2–3 条，**长度必须不等且有节奏** |
| 4 | 虚线连接 | `2px / #BFD6F1 / dash 5 5`，箭头用**等腰三角**，不能用直角三角形 |
| 5 | 圆角节点 | `268×88 / r20`，内部 40×40 图标块 + 2 条骨架条 |
| 6 | 选中态 | `2.5px #4A9DFC` + 10px 光晕 + 四角手柄；**不要画虚线选择框** |
| 7 | 指令气泡 | 与目标节点用「圆点锚点 + 40px 虚线」相连，**不要加箭头** |
| 8 | 标签页 / 缩放控件 | `− 100% +`、`架构图/流程图/结构图`，既交代"这是工具"又填补空白 |

## 常见坑

| 现象 | 原因 | 修法 |
|---|---|---|
| 底部一条白带，面板像只有边框 | 渐变淡到 `#FBFCFE` | 止于 `#F5FAFF` |
| 蓝色带太窄，整图发闷 | 内容卡太高 | h 收到 620–660，底部留 100–140px |
| 缩略后糊成一片灰 | 元素太小太淡 | 节点 ≥268×88；描边 `#C6D9F1`；骨架条 `#BCD3EF` |
| 上排 3 块中排 2 块，中间一条横线像分割线 | 汇聚总线横穿全宽 | 上下严格垂直对齐（2-2-1），只汇一次 |
| 虚线选择框被连线穿过 | 框跨越了层间连线 | 去掉虚线框，只留四角手柄 |
| 图层标签和节点没对齐 | 手写了 `top` | `label top = 节点 top + 节点高/2 − 13` |
| 箭头歪、像直角三角形 | 路径写错 | 向下 `M265 170 h14 l-7 12 z`，向右 `M456 219 l12 7 l-12 7 z` |
| 渲染出来空白或旧图 | HTML 在中文路径下 | 用 `render.py`，它复制到 ASCII 目录 |
| 浮标压住内容 | 内容卡过宽 | 内容卡宽 1310，右侧留 146–270px 给浮标 |

## 关于原始样本

本项目的风格来自对 **4 个产品线共 14 张原始场景图**的逆向分析（比例、取色、层叠偏移、元素频次都做了实测）。

这些原始图**版权归原权利方，未随仓库分发**，因此 `samples/` 已加入 `.gitignore`。风格规范、token 表、模板与示例图都是基于这些分析独立重写的，可以自由使用。

如果你拥有这些素材的授权、想一并发不出来：

```bash
git add -f samples/
```

## 二次开发

- **换配色**：只改 `:root` 之外的三个值——主蓝 `#4A9DFC`、紫端 `#7C6BF5`、面板渐变首尾。保持 135° 渐变方向和"深蓝投影"两条不变，家族感就不会丢。
- **加版式**：用 `_blank-shell.html` 起手，壳层不动，在 `.content` 区域内用绝对坐标摆零件。内容区原点 = `(180, 240)`。
- **批量出图**：写个脚本遍历标题列表 → 替换模板文本 → 调 `render.py`。模板是纯文本，用字符串替换或 Jinja2 都行。
- **改尺寸**：不建议。11:7 + 1760×1120 是这套图的比例基因，所有坐标都按它算好了。

## Roadmap

- [x] 14 张样本的风格逆向分析与 token 提取
- [x] 三种版式骨架 + 8 个元素零件
- [x] 零依赖渲染脚本（自动探测浏览器 / ASCII 路径 / 尺寸校验）
- [x] Agent Skill 封装
- [ ] 版式 A（中心辐射）完整模板
- [ ] 一键批量：`titles.txt` → 一批 PNG
- [ ] SVG 输出（当前只有 PNG，矢量版可无损缩放）
- [ ] 深色模式 token

## 贡献

欢迎提 Issue / PR。提交新模板时请附上渲染结果，并确保通过 `skill/hwcloud-scene-image/references/checklist.md` 的 12 条自检。改动 token 的 PR 需要说明为什么原值不够用——这套系统的价值就在"不变"。

## License

[MIT](LICENSE) © 2026

本项目为独立实现的视觉风格系统，与华为技术有限公司无关，未使用其任何官方设计资源。文中提及的产品名称仅用于说明风格来源。

<details>
<summary><b>English</b></summary>

A flat-vector, card-layered product-illustration system rendered with plain HTML/CSS/SVG. Give it a title, get a 1760×1120 PNG — deterministic, pixel-stable, Chinese text always crisp.

- **Stack**: HTML + CSS + inline SVG, rendered by headless Edge/Chrome. `render.py` uses only the Python standard library.
- **Style**: 3 nested rounded cards offset down-right, one 135° blue→violet gradient tile as the hero, light-blue dashed connectors, skeleton bars instead of real copy, 1–2 badges breaking the card edge, strict 0° perspective, no people, no photos.
- **Why not a diffusion model**: precise labels, 1px strokes, and reproducibility are hard requirements here.
- **Agent skill**: `skill/hwcloud-scene-image/` — copy it into `~/.workbuddy/skills/` and ask "draw me: …".

```bash
cp skill/hwcloud-scene-image/assets/template-c-ui.html my-scene.html
# edit .title, .prompt .txt, .badge .txt
python skill/hwcloud-scene-image/scripts/render.py my-scene.html out.png
```

</details>
