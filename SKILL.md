---
name: miuix
description: "构建、优化和审查 Miuix / HyperOS 风格的 Compose Multiplatform UI。用于明确采用 Miuix 的页面、组件、主题、导航、弹窗、安全区、布局密度与滚动动画性能任务，或询问 MiuixTheme、miuix-nav 等 Miuix API。通用 Compose 控件问题或明确指定 Material 3 等其他设计系统时不触发。"
---

# Miuix

使用真实的 Miuix 组件和目标版本 API，交付符合项目结构、状态契约与平台约束的界面。Miuix 是独立的 Compose Multiplatform 库，不是小米官方 SDK，也不能套用 Material 3 的参数与 token。

## 先确定上下文

1. 读取目标项目相关约定、Gradle/version catalog 和现有页面，确认 Miuix 版本、平台/source set、主题、导航、状态与 inset 的所有者。不要为使用本 skill 擅自升级依赖。
2. 当前维护基线是 **Miuix v0.9.4 / `39c40f99`**。项目使用其他版本时，以其确切 tag/commit 为准；未知版本时标明假设。版本与路径见 [source-verification](references/source-verification.md)。
3. 分清官方组件契约、可选组合模式与目标项目约定。以公共 API 和 Defaults 为基础，按产品需求决定布局、尺寸、断点和交互；示例应用只提供参考，不决定实现必须采用哪套页面或 helper。证据优先级见 [source-verification](references/source-verification.md)。
4. 有截图或预览时结合代码定位问题。先确认层级、宿主、状态和安全区，再改间距、颜色和装饰。

| 代码上下文 | 实现边界 |
|---|---|
| 现有页面/组件 | 复用应用的 `MiuixTheme` 和合适的 `Scaffold`；只有新页面边界或 popup 生命周期需要时才新增宿主 |
| 独立可运行示例 | 提供 remembered `ThemeController`、一个根 `MiuixTheme`，按 bars/Overlay/snackbar 需要配置 `Scaffold` |
| 局部调用示例 | 给最小可接入代码及所需祖先、依赖、状态和回调，不默认生成整个 App |

## 按任务加载参考

只读相关参考。组件名及常见英文空格写法（如 navigation bar、search bar）在 Miuix 上下文中先查组件目录。

| 任务 | 读取 |
|---|---|
| 依赖、主题、完整 App、Preview、跨页面 DPI/布局密度 | [setup-and-theme](references/setup-and-theme.md) |
| 页面创建、设置分组、组合方式 | [usage-patterns](references/usage-patterns.md) + [design-language](references/design-language.md) |
| 界面文案、标题/按钮/标签、提示与说明精简 | [界面文案规则](references/design-language.md#user-facing-copy) |
| 已有 UI 改造、截图审查、无障碍 | [ui-review-workflow](references/ui-review-workflow.md) |
| Scaffold、滚动、搜索、宽屏、刘海、键盘、安全区 | [page-layout-and-insets](references/page-layout-and-insets.md) |
| 长列表、分段卡片、展开收起、帧级动画、Flow 与稳定性 | [performance-and-animation](references/performance-and-animation.md) |
| 基础控件与导航栏选型 | [component-selection](references/component-selection.md) |
| 各组件具体用法、受控状态、回调与组合示例 | [component-usage](references/component-usage.md)，只读对应组件节 |
| 完整设置、精确数值/重置、编辑草稿、多色与预览 | [settings-and-editors](references/settings-and-editors.md) |
| 搜索多选、三态树、拖拽排序、批量应用、任务与事件 | [collections-and-workflows](references/collections-and-workflows.md) |
| awesome-miuix 全项目实现参考、依赖和迁移边界 | [community-projects](references/community-projects.md)，覆盖固定清单 15 个应用 |
| 设置行、单选对话框、Dropdown/Spinner | [preferences-and-menus](references/preferences-and-menus.md) |
| Overlay/Window、长对话框、BottomSheet | [overlays-and-windows](references/overlays-and-windows.md) |
| 状态概览、大对号卡片、绿/黄/红状态变体 | [状态卡视觉模式](references/design-language.md#status-overview-cards) |
| 自然语言配色、主题语义色、状态色 | [color-lookup](references/color-lookup.md) + [design-language](references/design-language.md) |
| 按压/选中反馈、高亮越界/四角突出、图标、squircle、blur、自定义绘制 | [styling-icons-and-effects](references/styling-icons-and-effects.md) |
| 路由、返回栈、转场、预测返回 | [miuix-nav](references/miuix-nav.md) |
| 版本基线与源码核验 | [source-verification](references/source-verification.md) |

## 从证据到实现

- 从[官方文档](https://compose-miuix-ui.github.io/miuix/)理解概念和组件选择；版本敏感的参数用目标 tag 下的 Markdown 文档、`docs/demo` 和 Kotlin 源码核对。组合页面时再读最接近的 `example/shared` 页面，避免遍历所有示例。
- [组件用法](references/component-usage.md)给出基础组件的使用契约；社区组合来源见 [15 个项目索引](references/community-projects.md)及 [实践核验](references/source-verification.md#community-usage-evidence)，不将旧版/快照/本地分支的参数直接复制到目标版本。
- 单组件调用只需确认签名、import/module、祖先与回调；页面改造再确认结构、状态、insets 和必要的视觉证据。证据足够后开始实施。
- 优先公共 Miuix 组件 → `*Defaults` / 主题 token → Compose Foundation/Layout/Animation。确无对应组件时做应用自有封装，标明归属；仅在项目或用户有意混用时引入其他 UI 库。
- `miuix-ui` 提供核心组件；设置行用 `miuix-preference`；扩展图标、模糊和路由分别按需添加 `miuix-icons`、`miuix-blur`、`miuix-nav`。避免无需求地加依赖。
- 官方网页无法访问时，可用同一版本的本地源码/缓存。无法证实的 API 明确标注，继续不依赖它的工作；不要猜参数或因一处缺证据放弃整个任务。

## 关键约束

- **界面文案**：只显示最终用户需要的文字。标题、按钮、标签使用短语，必要提示可用短句；需求说明、功能解释和实现理由写代码注释，不进入 UI。summary、副标题和次级标签默认省略，让布局表达层级，详见 [文案规则](references/design-language.md#user-facing-copy)。
- **状态与宿主**：业务状态上提；Overlay 使用页面 `Scaffold` popup host，Window 独立于该 host。调用方落实选中、启用、提交、取消、返回与关闭回调，不把 `onDismissFinished` 当作关闭请求。
- **交互反馈**：分清瞬时 pressed、业务 selected/checked 与受控保持 held；以独立交互表面为单位统一管理动作入口、反馈与形状边界，适用于按钮、卡片、列表/菜单项和嵌套区域；优先组件公开交互 API、自带 indication 和 Defaults。同一动作不叠加点击层、按压遮罩或缩放。参考 [交互状态与反馈规则](references/styling-icons-and-effects.md#pressed-selected-and-held-state)，`holdDownState` 不绑定业务选中值。
- **密度一致性**：首页、二级页面和整个 `NavDisplay` 共享应用缩放入口；无自定义缩放时沿用平台 density。页面继承一次计算的结果，不局部重乘或重置。整体偏大先查 density/fontScale 与样式来源，按 [密度排查](references/setup-and-theme.md#density-ownership-across-navigation) 取得证据后再改尺寸。
- **安全区**：记录 top/bottom/start/end/IME 各由谁处理，一次消费。内容必须为实际存在的底栏与浮动控件预留空间；尾部间距与系统安全区分别计算。`PullToRefresh.contentPadding` 不会替列表加 padding。
- **层级与颜色**：小设置组可用 `SmallTitle + Card`；大动态分组按行 lazy 化。默认样式优先；应用状态色集中定义，不杜撰 `MiuixTheme.colorScheme.warning/success`。
- **形状与效果**：纯填色用 `squircleBackground`；需要裁剪时用 `squircleClip`；可点击的圆角表面用 `squircleSurface` 裁住按压高亮，裁剪须在高亮绘制层外侧；每个独立子表面遵守自身形状边界，不能仅依赖父级裁剪。高亮越界按 [边界排查](references/styling-icons-and-effects.md#feedback-bounds-and-corner-clipping) 检查，触摸目标与视觉范围分别验收。组件已有裁剪时不重复添加。模糊要有支持检查及不透明降级。
- **性能**：布局/绘制所需的帧级状态在相应阶段读取。拆分大 lazy item、缓存真实依赖、保持 key 语义；`@Immutable` 是真实性承诺，普通 `List` 不等于必然不能 skip。按实际编译器和 profiling 判断。
- **导航**：`NavigationBar/Rail` 是导航外观，`miuix-nav` 管理返回栈。宽窄布局共享选中状态；持久化路线验证 `@Serializable`；不要凭旧模块名推断 Navigation 3 scene/result API。

## Code Delivery Contract

这是唯一交付清单；参考文件补充各领域细节。

- 交付与请求范围一致的代码：局部组件、完整页面或可运行 App。包含必要 import、状态、回调和宿主假设；不附带无关的主题/导航重建。
- 逐段执行界面文案检查：“最终用户需要读到吗？”不需要就删；必要文字再检查短语/短句、重复信息和空白占位。
- 保留业务行为与项目约定。任何自定义 helper 都必须已有实现，或在本次实现中提供，不能伪装成库 API。
- 对实际代码变更运行相关静态检查与最小目标编译。布局、弹窗、手势、宽屏、性能变更按风险补充预览/设备/trace 验证。
- 分别报告 **源码/API 核对、编译、视觉/设备、性能验证**。没有运行环境就明确未验证；截图不能证明交互或流畅度，编译不能证明视觉正确。
