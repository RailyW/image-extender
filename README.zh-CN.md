# Image Extender

> 使用 AI 无缝地向任意方向扩展图片，并在同一个工作室中构建完整的 2D 游戏美术资产集：视差背景、自动瓦片、精灵动画和装饰道具。

本文是 [README.md](README.md) 的中文翻译件，不替代默认 README。

这是一个小型开源 Web 应用，用于 AI 扩图和 2D 游戏美术生成。它默认通过 [OpenRouter](https://openrouter.ai) 使用 Google Gemini 图像模型，但也可以在 Settings 中把 Image / Text / Vision 工作负载分别路由到不同的自定义 provider。Poisson 融合流水线会隐藏原图像素与 AI 生成像素之间的接缝，同时还提供面向瓦片、精灵和道具的专用生成流水线。

你可以自带 provider API key。密钥只会保存在你的浏览器中，不会保存在服务器上。

![一张 1024² 肖像照片被扩展成无可见接缝的电影感宽画幅场景](docs/screenshots/after.png)

## 扩展前 / 扩展后

一张 1024 × 1024 的手机风格人像，只需几次点击，就能变成 16:9 的电影感宽画幅。颜色、光照、湿润路面的反射都保持一致，只是画面内容大幅扩展了。

| 扩展前 · 1024 × 1024 | 扩展后 · 向左 + 向右扩展成电影感宽画幅 |
| --- | --- |
| ![一张在霓虹灯照亮的布鲁克林街角、穿黄色夹克的人物方形肖像](docs/screenshots/before.png) | ![同一场景被横向扩展成宽幅电影画面](docs/screenshots/after.png) |

## 五种模式：Extender + Parallax + Tiles + Sprites + Props

应用本身是一个小型工作室，顶部栏的胶囊按钮可以在五个工作区之间切换：

- **Extender**（默认）- 向四个方向中的任意一个方向对图片进行扩图，并提供 best-of-3 接缝质量候选选择器。
- **Parallax Studio** - 从零构建真正的多层横版卷轴背景：独立的 **Sky / Far / Mid / Near** 图层、感知图层角色的 AI 提示词、基于色键的透明图层、实时多层滚动预览、自动扩展到目标宽度、可平铺循环修复，以及一键导出带 JSON 清单的 ZIP。
- **Tile Studio** - 为 2D 平台游戏生成 13 块自动瓦片集（主体 + 4 条边 + 4 个外角 + 4 个内角），通过 **一次** AI 调用生成 4×4 精灵表，并带有确定性的角块调和与 AI “美术总监” QA / 重绘循环。整套瓦片的调色板和纹理细节会保持锁定。
- **Sprite Studio** - 通过单次 AI 调用生成角色和生物动画表。选择一种 **体型方案**（人形、四足、蛇形 / 鱼类、飞行 / 鸟类或团块），选择动画，描述生物，然后得到一张关键帧表，附带实时动画播放器和面向引擎的导出。每种体型方案都有自己的解剖结构专用姿态引导骨架和动画集。
- **Props Studio** - 一个开放式、可持续增长的独立透明 **装饰精灵** 库，也就是游戏中叠放在瓦片地图之上的装饰物。每次生成 8 个，由两次调用的“美术总监 → 画师”流水线驱动，使资产集保持变化，避免重复。

### Parallax Studio

![Parallax Studio：Sky / Far / Mid / Near 深度图层，带实时多层滚动预览和 ZIP 导出](docs/screenshots/mode-parallax.png)

### Tile Studio

![Tile Studio：一次调用生成的 13 块自动瓦片集，带有实时“瓦片如何拼接”平台预览](docs/screenshots/mode-tiles.png)

### Props Studio

![Props Studio：一个开放式透明散点装饰精灵库，每次新增 8 个资产](docs/screenshots/mode-props.png)

### Sprite Studio

![Sprite Studio：角色行走循环关键帧表，带实时循环动画播放器](docs/screenshots/mode-sprite.png)

## 功能

- **点击边缘 → 向对应方向扩展。** 直接在图片上操作的空间控件，不需要层层对话框。
- **Best-of-3 候选选择器。** 每次扩展最多生成 3 个候选结果，并按接缝质量排序。可以用 `← →` 循环查看，在接受前选择你喜欢的版本。
- **Poisson 融合接缝。** 使用梯度域图像编辑（Pérez 等，2003），结合 mask-grow 与 replicate-padded Gauss-Seidel 迭代，让 AI 区域与原图边界在数学意义上尽量不可见。
- **低频颜色漂移预校正。** 在融合前，把 AI 输出在接缝处的颜色整体向原图接缝颜色靠拢，用于修正扩图中常见的“天空稍微变蓝了一点”这类失败模式。
- **可选提示词 + 美术风格。** 留空提示词即可做纯场景延续，也可以加入具体指令，例如 *“在地平线上加入一轮外星月亮”*。
- **自定义美术风格。** 内置 40 多种风格，从电影感、油画，到 Studio Ghibli、赛博朋克、蒸汽波等。
- **BYOK（Bring Your Own Key，自带密钥）。** Provider 密钥只存储在浏览器的 `localStorage` 中。服务器会代理请求到配置的 provider，但不会记录或持久化密钥。
- **模型 + provider 选择器。** 可以在 Settings 中切换默认 OpenRouter 模型，也可以为 Image / Text / Vision 分别配置独立的 provider、base URL、API key、协议和模型。
- **键盘优先。** 方向键用于扩展，`←` / `→` 用于切换候选，`Enter` 接受，`R` 重新生成，`Esc` 丢弃。
- **从零生成。** 没有基础图片也可以先用文本提示词生成一张，再进行扩展。
- **共享场景简报。** 系统会从你的提示词中自动提炼一个“场景简报”（地点、时间、调色板、氛围），并在 Parallax、Tiles、Sprites 和 Props 中复用，使同一项目里的所有资产都像来自同一个世界。

### Parallax Studio（面向游戏设计师）

- **4 个真实深度图层。** Sky（后景，不透明）、Far（远景剪影）、Mid（中景）、Near（前景道具）。在左侧选择一张卡片，只编辑画布中的对应图层。它使用与 Extender 相同的扩展流水线，但工作室知道每个图层的用途。
- **感知角色的 AI 提示词。** 当你生成或扩展某个图层时，模型会被告知该图层的角色，并只生成对应深度带的内容。Far / Mid / Near 会在纯洋红色键色（`#FF00FF`）背景上渲染，客户端再把它替换成真正的 alpha，因此每个图层都可以干净地叠加在天空上。
- **实时多层预览。** 每个已填充图层都会使用 GPU 加速的 `repeat-x` 横向滚动叠加，并拥有自己的可调滚动速度（Sky 缓慢漂移，Near 快速掠过）。在导出前调节每层速度滑块，即时感受纵深。
- **锁定横向扩展。** 只暴露 `←` 和 `→`，因为纵向扩展会扭曲游戏高度，所以被隐藏。
- **自动扩展到目标宽度。** 选择一个目标宽度（例如 7680px = 4 × 1080p 屏幕），然后让工作室自动工作。它会重复向右扩展当前图层，自动接受最佳候选，重新应用色键处理，并在达到目标宽度时停止。点击 `Stop` 可以中断。
- **可平铺循环点。** 游戏引擎通常用 `repeat-x` 平铺视差背景。`Tileable` 按钮会执行标准的“偏移半宽 / 修复新的中间接缝 / 偏移回去”流程，让纹理可以无缝循环。它也会在自动扩展结束后 **自动运行**，因此默认导出的结果可以直接使用。
- **Harmonize 接缝均衡。** 另一个可选处理用于解决更常见的问题：每次 AI 扩展都会引入轻微的颜色 / 亮度偏移，多次扩展后这些偏移会累积成竖向“面板条纹”。`Harmonize` 按钮会运行列均值平滑，去除条纹，同时保留细节。
- **宽度预设。** 快速选择常见目标宽度（3840 / 5120 / 7680 / 10240 / 15360 px），对应 720p / 1080p 屏幕的若干倍。
- **一键 ZIP 导出。** 将所有已填充图层打包为 PNG（保留 alpha），并附带 `parallax.json` 清单，记录深度顺序、滚动速度和尺寸。把 ZIP 放进 Unity / Godot / Phaser 后即可直接接入你的视差控制器。

### Tile Studio（2D 平台游戏自动瓦片）

- **一次 AI 调用生成完整 13 块瓦片集。** 主体、4 条直边、4 个外凸角和 4 个内凹角会一起生成在一张 4×4 图表里，因此调色板、纹理尺度和光照在整套瓦片中保持 **锁定**，不会像 13 次独立调用那样漂移。
- **模板引导的图生图。** 它不是要求模型凭文本凭空发明图集，而是让模型重绘一个结构参考：平坦洋红色背景上的圆角矩形，并带一个矩形洞。13 个角色都位于已知单元格，因此客户端可以在重绘后确定性地切出它们。
- **自动对齐 + 色键。** 重绘输出会被重新贴合到模板轮廓上（修复“AI 把它画小并居中”的失败模式），洋红色会用瓦片调校过的色键转成 alpha，边缘瓦片也会沿循环轴做可平铺处理。
- **确定性的角块调和。** 角块是最难的瓦片，因此应用会拼接角块，而不是完全信任 AI 原始单元格：外角保留绘制出的端帽，并通过羽化的 **边缘嫁接** 让纹理颗粒在两个方向上都匹配直边邻居；内角保留 AI 美术，只嫁接与直边接触的接缝。结果是每次运行都能得到无缝角块。
- **AI “美术总监” QA / 重绘循环。** 生成后，整套瓦片会被组合进一个平台预览样机，并交给一个 *vision* 批评模型，以资深瓦片美术的标准评审：检查色键是否真正扣掉背景（没有不透明背景块）、边缘端帽一致性、调色板 / 光照协调性、主体无缝性、毛边和模糊。如果失败，它会返回一份简洁修复报告，用于驱动重绘；如果通过则直接交付。批评模型会刻意限制在 **画师可修复** 的缺陷范围内（不会挑剔应用组合出来的角块几何）。
- **保留最佳选择。** 每一轮都会打分，循环会提交它见过的 **最佳** 候选，而不是简单采用最后一轮结果。因此即使批评模型不稳定，完整重绘也只会改善结果，不会让干净的初始生成退化。
- **可平铺主体，没有可见网格。** 重复的主体填充会经过更强的 2D 无缝处理（完整色调均衡 + 宽范围接缝融合），提示词也会禁止单元格大小的面板、长条纹和显眼主特征，因此内部重复时会像一整块连续表面。
- **实时“瓦片如何拼接”预览** 和单块瓦片重抽。可以只重新生成一个瓦片单元，不影响其他单元；之后整套瓦片会重新调和角块。
- **面向引擎的图集导出。** 导出带 padding 的图集（每个瓦片周围有 2px 复制 **extrude** 边框，用于防止 Unity / Godot / Phaser / Tiled 中的过滤和子像素渗色），以及单块 PNG 和清单。
- **14 个材质预设**：葱郁草地、苔藓石、红砖、雪峰、橡木板、沙漠砂岩、火山岩、发光洞穴、水晶冰、丛林地表、秋天地面、大理石与黄金、黑曜石、珊瑚礁。每个都是丰富的调色板 / 表面描述，且可以继续编辑。

### Sprite Studio（角色动画）

- **两阶段 anchor → sheet 工作流。** 朴素的单次多面板生成会闪烁：角色在单元格之间漂移、帧尺寸不一致、调色板偏移。这里采用 AI 精灵社区的共识做法：
  - **第 1 阶段：锁定角色。** 在纯洋红色键色上生成一张中性站立参考图。
  - **第 2 阶段：绘制动画表。** 把 anchor 作为参考图附加进去，再生成关键帧表：*“所附图片是标准角色，每个单元格都必须与它完全一致。”* 这是保持跨帧身份一致性最重要的杠杆。
  - Anchor 会 **在切换动画时保持存在**，因此同一个角色可以从 idle 复用到 walk、run、jump、attack，而不会重新抽身份。`Re-roll character` 按钮会丢弃 anchor 并重新生成一个角色。
- **五种体型方案。** 人形骨架不能驱动奔跑的四足动物、蜿蜒的鱼、扇动翅膀的飞行生物或弹跳的团块，因此 Sprite 模式会按解剖结构分支。选择一种 **体型方案** 后，工作室会切换到对应的姿态骨架、动画集、初始生物和 QA 预期，同时复用同一套 anchor → sheet → align → export 流水线：
  - **Humanoid**（双足）- 骑士、法师、哥布林、Boss。
  - **Quadruped** - 狼、大型猫科、马、猎犬，以及常见动物（狗、猫、牛、鹿、熊、狐狸、猪、山羊）。
  - **Serpent / Fish** - 蛇、鳗鱼和海洋生物（鲨鱼、小丑鱼、河豚、琵琶鱼、剑鱼、海豚、海蛇、食人鱼）。
  - **Flyer / Bird** - 鸟、蝙蝠、飞龙、精灵、凤凰。
  - **Blob** - 史莱姆、软泥怪、元素体、幽灵（纯 squash & stretch）。
- **解剖结构专用姿态引导骨架。** 每种体型方案都带有一张确定性的、由代码生成的姿态引导表（类似“ControlNet 风格”的人体 / 生物模型），会针对所选动作即时绘制并作为结构参考输入。骨架使用远近明度分离和深色轮廓，使重叠肢体保持可读，并会绘制真实解剖结构：四足动物的脊柱 + 4 条腿与头部起伏步态，蛇形的脊柱波动与张口攻击，鸟类填充的 **翼膜** 与正确的拍翼 / 俯冲动作，以及团块的 squash-and-stretch 运动弧线。
- **确定性的双体检测器。** 一个像素分析流程会扫描每个单元格的 alpha，检测一帧里是否出现两个生物（多面板生成常见失败模式），其中包含形态学 opening 步骤，可分离 **粘连** 的重复个体，并在发现重复时强制重绘。精灵生成更多依赖这些确定性检查，而不是等待 vision 批评模型，因此不会被 QA 模型阻塞。
- **尺度归一化。** 模型常常会在每个单元格中以略微不同的尺寸重画生物，导致回放时轮廓“呼吸”。处理流程会测量每帧紧致轮廓（bbox 对角线），取中位数作为目标尺度，然后在容差与 clamp 范围内把每帧缩放到目标附近；真实的姿态延伸（奔跑伸展、攻击突进）仍会保留形状。
- **基线与水平对齐。** 帧会按脚底基线对齐到单元格内同一条地面线，并水平居中，使回放时不会跳动或滑移。着地步态（idle / walk / **run**）会让 *每一帧* 都踩在地面线上；只有真正的弹道动作（jump、pounce）会通过刚性位移保留空中抬升，因此快速奔跑不会分裂成一排高帧和一排低帧。
- **按体型方案区分动画集。** Humanoid：idle / walk / run / jump / attack / hurt / death。Quadruped：idle / walk / run / jump / pounce / hurt / death / sleep。Serpent：idle / slither / strike / coil / hurt / death。Flyer：idle / flap / glide / dive / hurt / death。Blob：idle / hop / bounce / lunge / hurt / death。每个动画都有调校过的动作设计和合理的默认 FPS。
- **实时动画播放器。** 按动画原生 FPS 循环或单次播放，提供播放 / 暂停和帧 scrubber；导出前可通过 FPS 滑块调整手感。
- **生物预设 chips。** 每种体型方案都提供一键原型种子提示词（人形骑士 / 忍者 / 巫师……，四足狼 / 熊 / 猫……，海洋鲨鱼 / 锦鲤……，飞行鹰 / 飞龙……，团块史莱姆 / 软泥……）。
- **面向引擎的导出。** 下载网格表、横向 strip（Phaser / Unity 2D / Godot / Defold 更偏好的格式），或下载包含两种表、每帧 PNG 和 `manifest.json` 的 ZIP。清单会记录 FPS、循环标志、帧尺寸，以及每帧在网格和 strip 中的坐标。

### Props Studio（散点装饰）

- **开放式、可增长的资产库。** 模型不是生成一张由固定物品组成的表，而是为你的生态环境自由发明装饰道具。每次点击“add more”都会在一次 AI 调用中绘制另一批 **8** 个道具，并 **追加** 到库中。资产库可以无限增长，而且不会重新抽掉已有内容。
- **两次调用的“美术总监 → 画师”流水线。** 第 1 次调用是一个 *文本 / 推理* 模型，作为美术总监：根据生态环境和资产库中已经存在的分类，发明下一批全新且彼此不同的道具创意（刻意覆盖植物、矿物、骨骼、碎片、图腾、容器、生物痕迹、光源等类别）。第 2 次调用是图像模型，精确绘制总监决定的内容。把 *构思* 和 *渲染* 分开，是避免“同样的灯笼 / 罐子 / 巢穴循环出现”的关键。
- **便宜的文本去重。** 每个道具都会报告一个单词分类，这些分类会被统计并作为文本预算提示反馈给模型，因此新批次会避免相似物，而不需要把整个资产库作为图片重新发送。
- **跨批次风格锁定。** 现有道具的小蒙太奇会作为视觉风格 anchor 附加，使资产库增长时调色板、光照和渲染方式保持一致。
- **自由整理。** 可以重抽或删除任意单个道具；所有内容都生成在透明背景上。
- **8 个生态环境预设**：森林空地、发光洞穴、沙漠绿洲、雪峰、火山、丛林遗迹、迷雾沼泽、糖果乐园。这些预设只设置调色板 / 氛围，不指定具体物品，让模型保持发明自由。
- **清单中的描述性名称。** 因为美术总监已经给每个道具命名（它决定要画的物品类型），导出会使用这些名称，而不是匿名的 `prop_001`。清单会给每个道具保留人类可读的 `name`，文件也按名称命名（如 `lantern.png`、`mushroom.png`，重复时追加 `_02` / `_03` 后缀），因此图集在引擎中也是自说明的。
- **图集 + ZIP 导出。** 可以把整个资产库导出为打包后的透明图集 PNG 与 JSON 清单，也可以导出包含独立透明 PNG、图集和清单的 ZIP。

## AI “美术总监” QA 模式

Tiles 和 Props 共享一种把推理与渲染拆开的两次调用模式，它通常比一次盲目生成更稳定：

- **Props** 正向使用它：推理模型先决定 **要做什么**，图像模型再渲染。
- **Tiles** 反向使用它：图像模型先生成，vision 模型再评审组合后的结果；如有需要，它会把简洁修复报告发回去驱动重绘，并通过保留最佳选择确保循环只会改善输出。（对于 GPT image 这类较慢模型，评审会自动跳过，以避免几分钟的盲等。）
- **Sprites** 依赖 **确定性的** 后处理检查，而不是 vision 批评模型，包括尺度归一化、基线着地、水平居中，以及像素级双体 / 溢出检测器。检测到重复个体时会强制重绘。这让精灵生成更快速、更可预测，而不会被 QA 模型阻塞。

批评模型只关注画师真正能修复的缺陷，使用低 temperature 保持一致性，并采用 fail-open 设计（不稳定的批评模型不会阻塞你）。

## 扩展工作原理

```text
┌─────────────┐   1. expand canvas with        ┌───────────────────┐
│  original   │ ──  light-gray blank area ──▶  │  expanded canvas  │
└─────────────┘     in chosen direction        └─────────┬─────────┘
                                                         │
                                                         ▼
                                              ┌─────────────────────┐
                                              │  Gemini outpaints   │
                                              │  the blank region   │
                                              └─────────┬───────────┘
                                                        │
                                                        ▼
                                              ┌─────────────────────┐
                                              │  pre-correct color  │
                                              │  drift at seam      │
                                              └─────────┬───────────┘
                                                        │
                                                        ▼
                                              ┌─────────────────────┐
                                              │  Poisson blend with │
                                              │  grown mask         │
                                              └─────────┬───────────┘
                                                        │
                                                        ▼
                                              ┌─────────────────────┐
                                              │  measure seam       │
                                              │  residual, repeat   │
                                              │  ×3, sort, present  │
                                              └─────────────────────┘
```

对于横向扩展，流水线会并行运行最多 3 次尝试（每次使用不同 temperature），测量每个融合结果的接缝残差，并按融合质量从好到坏展示候选。即使接缝最干净的版本不是你喜欢的内容，你也可以自由切换并选择另一个版本。

纵向扩展使用另一条分块路径，它足够确定，通常 1 次尝试就够。

## 快速开始

```bash
git clone https://github.com/boona13/image-extender.git
cd image-extender
npm install
npm run dev
```

打开 [http://localhost:3000](http://localhost:3000)。首次加载时，应用会提示输入默认 provider API key。粘贴一次后它会保存在本地，除非你在 Settings 中清除，否则不会再次弹出。

默认 OpenRouter 路径仍然可以使用来自 [openrouter.ai/keys](https://openrouter.ai/keys) 的密钥；自定义 provider 可以在 Settings 中配置，也可以通过环境变量配置。

### 可选：服务端环境变量 fallback

如果你不想在浏览器里输入密钥（或者你正在托管一个 demo，希望给访客提供密钥），可以把 `.env.example` 复制为 `.env.local` 并填入密钥：

```bash
cp .env.example .env.local
# 编辑 .env.local，添加 OPENROUTER_API_KEY 或各 provider 的密钥
```

设置后，任何没有携带客户端密钥的请求都会使用服务端密钥。

### 自定义 providers

Settings 暴露三个独立的 provider 槽位：

- **Image provider** - Extender、Parallax、Tiles、Sprites 和 Props 的渲染调用会使用它。
- **Text provider** - 场景简报和道具构思会使用它。
- **Vision provider** - 瓦片和精灵的 QA 评审会使用它。

每个槽位都可以使用不同的协议、base URL、密钥和模型。默认协议是 `openrouter-chat-completions`，保留原始 Nano Banana / Gemini 行为。`openai-responses` 适用于通过 Responses 风格 `image_generation_call` 输出返回图片的 provider，`openai-chat-completions` 覆盖 OpenAI-compatible 的文本和视觉网关。`openai-images` 可用于纯文本生图。

服务端环境变量也可以按能力分别配置：

```bash
IMAGE_PROVIDER_PROTOCOL=openai-responses
IMAGE_PROVIDER_BASE_URL=https://api.openai.com/v1
IMAGE_PROVIDER_API_KEY=...
IMAGE_PROVIDER_MODEL=gpt-image-2

TEXT_PROVIDER_PROTOCOL=openai-chat-completions
TEXT_PROVIDER_BASE_URL=https://openrouter.ai/api/v1
TEXT_PROVIDER_API_KEY=...
TEXT_PROVIDER_MODEL=google/gemini-2.0-flash-001

VISION_PROVIDER_PROTOCOL=openai-chat-completions
VISION_PROVIDER_BASE_URL=https://openrouter.ai/api/v1
VISION_PROVIDER_API_KEY=...
VISION_PROVIDER_MODEL=google/gemini-2.0-flash-001
```

## 使用方式

| 操作 | 方法 |
| --- | --- |
| **切换模式** | 点击顶部栏中的 `Extender` / `Parallax` / `Tiles` / `Sprite` / `Props` 胶囊按钮 |
| **上传图片** | 拖放、点击 dropzone，或从文本生成一张 |
| **扩展** | 点击四个边缘 handle 之一，或按 `↑` `↓` `←` `→`（parallax 模式仅支持 `←` `→`） |
| **切换候选** | `←` `→` 方向键（或图片下方胶囊中的 chevron） |
| **接受 / 重新生成 / 丢弃 / 下载** | `Enter` / `R` / `Esc` / `Download` 按钮 |
| **选择视差图层** | 点击左侧面板中的卡片（Sky / Far / Mid / Near） |
| **调整每层滚动速度** | 拖动每张图层卡片上的滑块，预览会实时更新 |
| **自动扩展（parallax）** | 设置目标宽度，点击 `Auto-extend`，点击 `Stop` 中断 |
| **生成可平铺 / 均衡（parallax）** | `Tileable` 修复循环接缝（自动扩展后会自动运行）；`Harmonize` 拉平累积漂移 |
| **导出项目（parallax）** | 点击 `ZIP` → 所有图层 + `parallax.json` 清单 |
| **生成瓦片集** | 描述材质，点击 generate；一次 AI 调用，自动角块调和 + QA 评审 |
| **重抽单块瓦片** | 点击单个瓦片单元上的 spark（之后会重新调和角块） |
| **导出瓦片集** | 图集（带 extrude padding）+ 单块 PNG + 清单 |
| **选择精灵体型方案** | 选择 `Humanoid` / `Quadruped` / `Serpent / Fish` / `Flyer / Bird` / `Blob`，会切换骨架、动画集和预设 |
| **选择精灵动画** | 点击动画 chip（可选项取决于体型方案） |
| **锁定精灵角色** | 选择 starter chip（或自行描述），点击 `Lock character + <anim>`，会运行两个阶段 |
| **重抽精灵动画 / 角色** | `Re-roll <anim>` 只运行第 2 阶段（身份保持不变）；`Re-roll character` 从零运行两个阶段 |
| **播放 / 暂停精灵，调整 FPS** | 实时播放器下方的播放按钮 + scrubber；拖动 `FPS` 滑块 |
| **导出精灵项目** | `Sheets + manifest` 导出网格 + strip PNG，或 `ZIP` 导出全部 |
| **生成道具** | 选择生态环境预设（或自行描述），点击 add，生成 8 个全新且不同的道具并追加 |
| **继续添加道具** | 再次按 add，追加另一批 8 个，并基于已有资产去重 |
| **整理道具** | 悬停某个道具即可重抽或删除 |
| **导出道具** | `Atlas + manifest` 导出打包透明图集，或 `ZIP` 导出独立 PNG + 图集 + 清单 |

可选的自定义提示词和美术风格位于底部命令栏。

## 技术栈

- **[Next.js 14](https://nextjs.org/)**（App Router）+ React 18 + TypeScript
- **[Tailwind CSS](https://tailwindcss.com/)** 用于深色工作室主题
- **HTML Canvas** 用于所有客户端图像处理（[app/utils/imageProcessor.ts](app/utils/imageProcessor.ts)）
- **[JSZip](https://stuk.github.io/jszip/)** 用于浏览器内项目打包
- **Provider adapters** 用于模型访问。OpenRouter 仍是默认值。
  - Image：`google/gemini-3.1-flash-image-preview`（Nano Banana 2，默认）、`google/gemini-3-pro-image-preview`（Nano Banana Pro）、`google/gemini-2.5-flash-image`（Nano Banana）和 `openai/gpt-5.4-image-2`（GPT-5.4 Image 2，高保真，较慢）
  - Reasoning / vision QA（场景简报、道具美术总监、瓦片评审）：`google/gemini-2.0-flash-001`
- **Codex Skill package** 位于 `skills/image-extender-studio/`，用于通过
  Markdown 编排、自定义 provider、Codex App imagegen 和确定性的 Python
  后处理脚本运行同一组工作流。

## 项目结构

```text
app/
├── api/
│   ├── extend/route.ts        扩图端点（由 provider 支撑）
│   ├── generate/route.ts      文生图 + 瓦片表 + 精灵表提示词
│   ├── scene-brief/route.ts   为项目提炼共享场景简报
│   ├── prop-brief/route.ts    Props “美术总监”：发明下一批道具
│   ├── tile-review/route.ts   Tile QA “美术总监”（vision 批评模型）
│   └── sprite-review/route.ts Sprite QA “美术总监”（vision 批评模型）
├── components/                按工作区拆分的 UI
│   ├── TopBar / CommandBar / Workspace / VariantSelector / Modals / icons
│   ├── ParallaxStudio.tsx
│   ├── TileStudio.tsx         + PlatformPreview 组合器
│   ├── SpriteStudio.tsx
│   └── PropStudio.tsx
├── lib/                       领域逻辑与常量
│   ├── app.ts / models.ts / artStyles.ts
│   ├── providers/             Provider 配置 + OpenRouter/OpenAI-compatible adapters
│   ├── parallax.ts / tileset.ts / sprite.ts / props.ts
│   └── bodyPlans.ts           精灵体型方案注册表（动画、预设、骨架）
├── utils/
│   ├── imageProcessor.ts      Canvas：分块、Poisson 融合、色键、
│   │                          可平铺、接缝评分、精灵对齐 / 缩放
│   ├── poseRig.ts             分派到体型方案骨架 + 测量主体
│   ├── rigCore.ts             共享骨架原语（capsule/dot/projection…）
│   └── rigs/                  每种体型方案的姿态骨架（双足、四足、
│                              蛇形、飞行、团块）
├── globals.css                深色“工作室”设计系统
├── layout.tsx                 根布局，Inter 字体
└── page.tsx                   应用外壳：状态、生成流水线、QA 循环

skills/
├── README.md                  Skill 模块概览
└── image-extender-studio/
    ├── SKILL.md               Codex Skill 入口与工作流路由
    ├── agents/openai.yaml     Codex skill 列表 UI 元数据
    ├── references/            Extender / Parallax / Tileset / Sprite /
    │                          Props 子流程指南与 provider 文档
    └── scripts/
        └── image_extender_skill.py
                               Provider 调用、prompt 生成、图像后处理、
                               打包和覆盖审计
```

## 配置旋钮

一些你可能想调整的小参数：

| 常量 | 位置 | 默认值 | 含义 |
| --- | --- | --- | --- |
| `EXTENSION_PERCENT` | `app/lib/app.ts` | `38` | 每次扩展会增加当前维度的多少比例 |
| `maxAttempts` | `app/lib/models.ts` 中的每个模型 | `1`-`3` | 每次横向扩展的 Best-of-N 候选数 |
| `MAX_TILE_REVIEW_PASSES` | `app/page.tsx` | `2` | QA 美术总监可以触发的额外瓦片重绘轮数 |
| `TILESET_TILE_SIZE` | `app/lib/tileset.ts` | `512` | 4×4 图表中每块瓦片的分辨率 |
| `TILESET_ATLAS_EXTRUDE_PX` | `app/lib/tileset.ts` | `2` | 每个导出图集瓦片周围复制边框的像素数 |
| `PROP_BATCH` | `app/lib/props.ts` | `8` | 每次“add more”绘制的道具数量 |
| `GROW_PX` | `app/utils/imageProcessor.ts` | `8` | Poisson mask 向原图内增长的像素数 |
| `iterations` | `app/utils/imageProcessor.ts` | `250` | Gauss-Seidel 最大迭代次数 |

## 隐私与安全

- 在 UI 中输入的 provider API key **只会** 存储在你的浏览器 `localStorage` 中。它们不会写入服务器磁盘，也不会被记录日志。服务器只会在每次请求中用它们代理调用配置的 provider，然后丢弃。
- 服务端 `OPENROUTER_API_KEY` 环境变量是 **可选的**，只作为没有客户端密钥的请求的 fallback。按槽位配置的 provider 环境变量可以覆盖它。
- 没有 analytics，没有 telemetry，没有 tracking。

## 致谢

- Poisson 图像编辑技术：**Pérez, Gangnet, and Blake (2003) - “Poisson Image Editing”**，SIGGRAPH。
- 感谢 Google 提供 Gemini 图像模型，感谢 OpenRouter 提供统一 API。

## 许可证

[MIT](LICENSE)
