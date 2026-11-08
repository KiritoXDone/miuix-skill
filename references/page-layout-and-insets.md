# Page Layout, Insets, and Adaptive UI

用于页面骨架、搜索、滚动和屏幕适配。基于 Miuix v0.9.4 的 `Scaffold.kt`、`PullToRefresh.kt`、官方 utils 指南及 [Mishka 实践来源](mishka-patterns.md)。以下选择依赖宿主，不是一份全局强制页面模板。

## Insets ownership before spacing

先列出边缘及其所有者，再写 padding。Miuix `Scaffold` 向内容 lambda 提供 `PaddingValues`，不自动替内容应用它们。

| 场景 | 处理方式 | 常见错误 |
|---|---|---|
| 页面有顶栏和底栏 | 列表/内容消费对应 Scaffold padding，并核查 system insets | 只取 top 导致末项藏在底栏下 |
| 无底栏的详情页 | 可消费完整 padding；也可明确由末尾 item 负责底部导航安全区与呼吸空间 | 同时用 bottom contentPadding、navigationBarsPadding 和尾部安全区造成重复 |
| Pager 子页 | 外层 shell 有 bottomBar 时，把尚未消费的底部空间传到各页 | 机械禁止 `bottomPadding` 参数 |
| 横屏全宽内容 | 检查未被宿主消费的左右 display cutout/navigation insets，按 start/end 处理 | 仅处理 top/bottom，内容落在侧边缺口下 |
| NavigationRail + 内容 | rail 与内容分别拥有其边缘；消费已处理的 start inset，再处理内容剩余边缘 | rail 与列表各补一次 start，RTL 时仍写死 left |
| BottomSheet | 单独检查 IME、内容底部与窗口高度限制 | 由 `defaultWindowInsetsPadding` 名字推断所有边缘已安全 |

Mishka 详情页采用 top padding + `24.dp` 尾部 Spacer + navigation inset；这是其无底栏页面的约定。主 Pager 必须保留外层底栏空间。移植时选择一种清晰的底部处理方式，不照搬“永远不设 bottom padding”。

## Scroll and spacing

- 有折叠顶栏时，列表的 `nestedScroll` 接同一 `scrollBehavior.nestedScrollConnection`，不要重新创建另一份行为状态。固定顶栏无需为了模板而加折叠逻辑。
- `overScrollVertical` 与滚动触觉按项目交互和平台使用；确认 theme 已有的 overscroll 配置，避免重复效果。`scrollEndHaptic` 在 Mishka 是项目 helper，不是通用可导入 API。v0.9.4 支持自定义鼠标拖动参与 overscroll；不要把滚轮与拖动视为同一种输入，核对目标版本的 `Overscroll.kt` / `OverscrollFactory.kt`。
- 第一项是 Card/裸表单时，可用项目节奏补顶部间隔；第一项是自带上下内边距的 `SmallTitle` 时不要再无条件加同一间隔。
- Mishka 以 12 dp 作为横向/组间间距，并选择逐 item bottom padding。目标项目可采用自己的 spacing 或 `Arrangement.spacedBy`；分段卡片内部需要无缝衔接，不能对所有段统一加间距。
- `TextField` 已有自己的背景和交互样式，只有分组语义需要时才包 Card。
- `PullToRefresh.contentPadding` 在 v0.9.4 用于刷新指示器的 top offset，内容本身仍要布局。分别计算刷新头和首项位置，包含必要的首项顶部空白，不重复偏移列表。

## One adaptive decision, two independent dimensions

- 将 shell 的宽窄判断集中到一个函数/状态；`NavigationBar` 与 `NavigationRail`、顶栏模式、搜索顶距共享它。
- 应用若允许改变 `LocalDensity` 实现 UI 缩放，决定 shell 的窗口宽度可使用缩放前平台密度；内容尺寸仍按实际布局密度计算。`LocalPlatformDensity` 是 Mishka 自定义 CompositionLocal，不能从 Miuix 导入。
- shell 切换断点与内容最大宽度是两个参数。Mishka 用 600 dp / 800 dp；官方 Example 有另一套断点。遵循目标产品，不把其中任一套称为库标准。
- 需要侧边留白也能拖动列表时，让 `LazyColumn` 保持可用区域全宽，将居中留白加到 `contentPadding`。只缩小列表节点会缩小可滚动命中区域。
- 内容可用宽度应已扣除 rail 与安全区，侧边留白不小于零。宽窄切换保留 destination、back stack、列表和输入状态。
- 横屏/矮窗口可使用固定小顶栏；已有 hero/视差页不要额外叠加大标题。搜索框的动态顶距需与顶栏模式一致。
- 滚动内容要求“至少一屏”时用最小高度约束，不用恰好一屏的固定高度掩盖溢出；检查长文本和横屏的可滚动性。

## Search as state flow

查询词 → 过滤结果 → 空/非空/未搜索状态，保持单向数据流。不要在 composable 主体读取结果后直接回写查询状态。优先纯派生值；组件确需同步时通过 effect/明确事件更新。

Mishka 的 `rememberSearchScreenStatus`、`SearchResultStatusEffect`、`rememberSearchBarTopPadding`、`TopAppBarAnim` 属于应用封装。复用思想时检查官方 `SearchBar`/`InputField` 与实际版本，而不是复制 helper 名称。帧级 top inset 使用延迟读取，见 [performance-and-animation](performance-and-animation.md)。

## Pager gesture ownership in v0.9.4

只有列表惯性/回弹与横向切页确实冲突时，才采用官方 `pagerGestureOverride`：

- `CrossAxisInterceptor` 模式配套 `userScrollEnabled = false` **及** `pageNestedScrollConnection = PagerGestureNestedScrollConnection`，缺一不可。
- 改回 `Native` 或 `TapToHalt` 时同时恢复原生 pager 的两项配置。
- 禁用滑动切页时，同时禁用 modifier 与 pager 的用户滚动。
- 点击 tab 或导航项可按官方调用使用 `pagerState.springAnimateToPage(targetPage)`；同样由单一 pager state 管理选中页。
- TabRow 的选择值直接来自 `pagerState.currentPage`；点击触发滚动，不用两个互相同步的 `LaunchedEffect` 维持重复 selected state。

精确 imports 和调用形状查 `miuix-ui/.../utils/PagerGestureUtils.kt` 与 `docs/guide/utils.md`，通过 [source-verification](source-verification.md) 定位。

## Runtime checks

覆盖本次触及的窄/宽窗口、横屏侧缺口、RTL、UI 缩放/大字体、键盘、最后一项及首项刷新头。改变 shell 以后再测导航状态；增加 padding 后再查重复消费。截图可验证边界，手势/焦点/键盘需实际运行。
