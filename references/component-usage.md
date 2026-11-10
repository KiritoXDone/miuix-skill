# Component Usage

用于选定组件后的具体实现。每节说明“何时用、谁持有状态、怎样接回调、检查什么”。公共 API 基线为 Miuix v0.9.4；项目用法来自 [awesome-miuix 实践核验](source-verification.md#community-usage-evidence)。有些项目使用旧版、快照或本地分支，只提炼组合方式，参数回到目标版本源码核对。

按本次涉及的组件阅读一节即可；无需把所有控件放进一个页面。文档中的选项文字均为示例，落地时使用项目本地化资源。遵循 [界面文案](design-language.md#user-facing-copy)与 [交互表面边界](styling-icons-and-effects.md#feedback-bounds-and-corner-clipping)。

## Page and navigation components

实践入口：[页面与工具栏](source-verification.md#community-page-and-actions)。布局与 Insets 的完整规则见 [page-layout-and-insets](page-layout-and-insets.md)。

| 组件 | 使用方式与状态 | 组合与注意点 |
|---|---|---|
| `Scaffold` | 页面有 bar、FAB、toolbar、snackbar 或 Overlay host 时使用；回调提供的 padding 由内容消费 | 复用既有页面宿主；列表与 scrollbar 分别处理其内容/轨道 padding，底部浮动操作不能遮住最后一项 |
| `TopAppBar` | 大标题/折叠标题场景；同一 scrollBehavior 传入顶栏并连接列表 nestedScroll | 标题用短语；actions 放 IconButton 或原生图标菜单，不在图标外叠 clickable；透明背景需要真实的下层表面或 blur |
| `SmallTopAppBar` | 固定小标题、紧凑窗口或详情页 | 沿用页面导航回调与 bar inset；不要为了模板再叠大标题 |
| `NavigationBar` / `NavigationBarItem` | 紧凑窗口的顶层目的地；每项 selected/onClick 来自同一导航状态 | 只表示目的地切换，不在项内保存第二份 selected；角标用 item 的 badge slot |
| `FloatingNavigationBar` / `FloatingNavigationBarItem` | 产品明确需要悬浮导航外观时使用；状态模型同上 | 与页面底部安全区、列表留白及 snackbar 协调；不要把项背景再次包进另一套按钮反馈 |
| `NavigationRail` / `NavigationRailItem` | 宽窗口的目的地导航；固定模式用 expanded，交互展开用 rememberNavigationRailState + state | 宽窄布局共享目的地；固定/状态重载择一。组件决定 rail 外观，应用决定断点与内容布局 |
| `TabRow` | 同页分类/分页，tabs + selectedTabIndex + onTabSelected | 接 Pager 时选中值从 pagerState.currentPage 派生，点击触发滚动；不通过两个相互同步的 effect 复制状态 |
| `VerticalScrollBar` / `HorizontalScrollBar` | 使用同一滚动容器的 ScrollState/LazyListState/LazyGridState 创建 rememberScrollBarAdapter | 作为滚动内容的 sibling 对齐相应边缘，trackPadding 对齐内容安全区；需 ExperimentalScrollBarApi，不另外维护滑块位置 |
| `BreadcrumbBar` / `BreadcrumbItem` | 有真实层级路径的文件/目录等导航；items 表示路径，onItemClick 收到索引 | highlightIndex 表示当前高亮段；joinToPath 只是路径组合，不代替业务层权限、路径校验或数据加载 |

`NavigationBar/Rail/TabRow` 不拥有返回栈。页面路由用 [miuix-nav](miuix-nav.md) 的 NavDisplay/entry/rememberNavBackStack；父子导航和自适应外观复用同一应用状态。没有对应社区调用证据的组件，以 [官方目录中的 doc/demo/source](component-selection.md#component-reference-table) 为依据，不假称项目实际使用了它。

## Surfaces, content, and actions

实践入口：[页面与工具栏](source-verification.md#community-page-and-actions)、[进度与弹层](source-verification.md#community-progress-and-overlays)。

| 组件 | 何时使用 | 状态、回调与边界 |
|---|---|---|
| `Surface` | 需要颜色、内容色、形状/边框等底层表面能力，Card 的纵向组合不合适时 | 使用对应的非交互/交互重载。视觉和输入由同一表面负责；不要把 Surface 当作自动附带 Card 的所有默认值 |
| `Card` | 一组相关信息、独立操作表面或状态概览 | 普通容器不设置动作；可交互时用 onClick/onLongPress，按需 showIndication。高亮在内部圆角中；大列表组是否拆行见性能指南 |
| `BasicComponent` | 需要完整的 Miuix 行布局，但现有 Preference 不能表达该行 | title/summary 按需，图标/动作放 startAction/endActions/bottomAction；复杂子动作要核对 enabled 与父子命中，勿用重复行点击补槽位 |
| `Text` | 展示用户需要的标题、标签、状态或业务内容 | 优先 MiuixTheme.textStyles；长文本按内容决定换行/截断，不把功能解释或实现说明写入 UI |
| `SmallTitle` | 多组内容需要短标签区分时 | 单组且含义明确可省略；标签自带间距，不再无条件加顶部空白 |
| `HorizontalDivider` / `VerticalDivider` | 空白和分组仍不足以区分内容时 | 横向分隔行、纵向分隔并排区域；无交互也无需额外语义。不要每个 Preference 都自动加线 |
| `Button` | 操作内容需要自定义 RowScope 组合，如图标与文字 | onClick 触发业务事件，enabled 来自有效性/任务状态；ButtonDefaults 提供普通/primary 配色，不额外包 clickable 或 pressed 遮罩 |
| `TextButton` | 用短动作文字表达操作，无需手写内容布局 | `text` + `onClick`，重要操作可用 ButtonDefaults.textButtonColorsPrimary；名称不代表必须呈现无背景的 Material 文字按钮 |
| `IconButton` / `Icon` | 紧凑独立动作；Icon 也可用于行的装饰槽位 | IconButton 管命中与反馈，Icon 管图形。动作提供简短 contentDescription，装饰图标为 null；扩展图标核对依赖和名称 |
| `FloatingActionButton` | 页面有明确且持续的主操作 | 放入 Scaffold 的 floatingActionButton slot；不要为每个次级操作造一个悬浮按钮，保存/刷新状态由页面所有者控制 |
| `FloatingToolbar` | 一组上下文动作需悬浮显示，如选择后批量操作 | 使用 Scaffold.floatingToolbar + floatingToolbarPosition；按钮事件来自选中集合，清空选择时按产品行为隐藏/禁用，不再手工覆盖 snackbar |

进度按钮、提示徽章、自动完成输入等可能是应用 wrapper；相似名称不等于 Miuix 公开组件。优先现有 Button/ProgressIndicator/Badge 组合，只有明确需求才实现 wrapper。

## Input, search, and selection controls

实践入口：[表单与选择](source-verification.md#community-form-and-selection)。完整设置行见 [Preference 用法](preferences-and-menus.md#usage-contracts)；裸控件适用于表单或已有标签布局。

| 组件 | 数据与回调 | 使用注意 |
|---|---|---|
| `TextField` | value + onValueChange（String/TextFieldValue），或 state（TextFieldState） | 选择一种状态模型。value 重载用 singleLine/keyboardActions/visualTransformation；state 重载用 lineLimits/onKeyboardAction/inputTransformation/outputTransformation，不跨重载混参数 |
| `SearchBar` / `InputField` | 同一 query 和 expanded 所有者；InputField 的 onQueryChange/onSearch 与外层 onExpandedChange 一致 | 查询、建议、结果状态由应用派生；后台过滤/请求需要去重或取消旧结果。SearchBar 不等于已有自动完成数据源；胶囊颜色参数属于 InputField |
| `Switch` | checked + onCheckedChange | 表示立即生效的开/关。数据保存失败时恢复已确认状态或展示必要错误；不要另用 pressed 表示开关值 |
| `Checkbox` | state: ToggleableState + onClick；On/Off/Indeterminate | 独立选择或多选集合。筛选/离屏不应清除未显示项目；如果由整行接管同一动作，将子控件 onClick 置空，保留行的选择语义；完整设置行则用 CheckboxPreference 的 checked/onCheckedChange |
| `RadioButton` | selected + onClick | 互斥选择；单一选择键由父级保存，各项据此派生 selected。没有必要把点击已选项解释成取消选择 |
| `Slider` | value + onValueChange，范围用 valueRange，提交时机可用 onValueChangeFinished | 拖动期间更新 UI 草稿，昂贵保存可在结束后提交。steps 是端点之间的离散点数，不是总选项数 |
| `VerticalSlider` | 与 Slider 相同的连续/离散值契约 | 给出可用高度，验证方向和嵌套滚动，不用旋转整个横向布局模拟其语义 |
| `RangeSlider` | value/onValueChange 接 ClosedFloatingPointRange，valueRange 是允许边界 | 始终保留起止值与顺序；两端格式化和提交作为一个范围，不用两份独立滑块状态制造交叉 |
| `NumberPicker` | value: Int + onValueChange，range: IntRange；label 格式化显示 | 适合精确整数/少量离散值的滚轮选择。不是 Slider wrapper；range 改变后应用状态也要修正，不能只依赖控件显示时的 clamp |

### Form submission and input suggestions

把输入草稿、校验和提交事件分开；输入法提交和主按钮调用同一操作，不在 composition 中发请求。按钮有效性来自当前草稿和任务状态；失败保留输入。文案只留短字段名、动作与必要校验提示。

输入建议是一种应用组合：TextField + OverlayListPopup。为焦点、查询、建议列表、显式关闭建立清晰状态；选择建议时先更新输入，再收起建议并按产品需要处理焦点。Popup 要锚定输入区域并适配 IME/窗口；是否启用背景遮罩与列表宽高由这个交互决定。不要照抄应用里的自定义位置测量函数，也不在 TextField 外套 clickable 强制焦点。

### Minimal controlled inputs

以下为 v0.9.4 局部组件示例，调用方已提供 MiuixTheme、值和回调；输入使用 String 模型，数字选择按有效范围回传。没有新增 App/Scaffold 或数据存储逻辑。

```kotlin
import androidx.compose.runtime.Composable
import androidx.compose.ui.text.input.KeyboardType
import androidx.compose.foundation.text.KeyboardOptions
import top.yukonga.miuix.kmp.basic.NumberPicker
import top.yukonga.miuix.kmp.basic.TextField

@Composable
fun NameInput(value: String, onValueChange: (String) -> Unit) {
    TextField(
        value = value,
        onValueChange = onValueChange,
        label = "名称",
        singleLine = true,
        keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Text),
    )
}

@Composable
fun CountInput(value: Int, onValueChange: (Int) -> Unit) {
    NumberPicker(value = value, onValueChange = onValueChange, range = 1..12)
}
```

整数 Slider 在有效范围 a..b 至少含三个整数（b - a >= 2）时，可用 steps = b - a - 1 并将回调 roundToInt；包含两端。steps = 0 表示连续模式，不表示仅选两个端点；只有两个离散选项时优先用明确的选择控件。数据无序、值稀疏或含“自动”等非数值选项时使用明确的选项映射/Dropdown，不能套这个公式。值集合改变也要同步当前选择。

## Progress, refresh, and feedback

实践入口：[进度与弹层](source-verification.md#community-progress-and-overlays)。后台任务的生命周期与 UI 显示生命周期分开；隐藏弹窗不自动意味着取消任务。

| 组件 | 使用方式 | 需要避免 |
|---|---|---|
| `LinearProgressIndicator` | 已知进度用 0f..1f；未知总量用 progress = null 或省略，适合任务/下载进度 | 未知工作量伪造百分比、重组时重复启动任务，或拿 progress=0 当作无限进度 |
| `CircularProgressIndicator` | 同样支持已知/未知进度，适合局部内容区域 | 用过大 spinner 阻断整页，或已经显示相同状态还叠加说明文字 |
| `InfiniteProgressIndicator` | 不确定等待过程，如准备、解析或提交；只表示工作尚未结束 | 把等待动画当作成功、超时恢复或错误提示的替代品 |
| `PullToRefresh` | isRefreshing 来自任务所有者，onRefresh 触发事件；状态结束由真实结果决定 | 用动画定时器结束刷新、重复请求；contentPadding 只负责刷新指示器偏移，列表单独消费 padding |
| `Snackbar` / `SnackbarHost` / `SnackbarHostState` | remember 一个 host state，交给 Scaffold.snackbarHost；从事件/协程调用 showSnackbar | composition 主体反复入队，永久错误只放短暂 snackbar；有 action 时根据 SnackbarResult 执行一次动作 |
| `Badge` / `BadgedBox` | 无 content 的 Badge 表示点，有 content 显示必要计数/短状态；BadgedBox 绑定锚点 | 默认红色并不等同于任何业务状态；0 是否隐藏按产品定义，数量已有文字时避免重复标签 |
| `TooltipBox` / `RichTooltipBox` | 给紧凑动作提供必要的短名称或额外操作入口；可用 rememberTooltipState 管理 | 不把开发说明塞进 tooltip，不用它独占错误信息；长按与控件自身长按冲突时核对手势设计 |
| `PlainTooltip` / `RichTooltip` | 是 TooltipScope 的内容构建块；通过 TooltipBox 的 tooltip slot 使用 | 将其当作无宿主的任意悬浮 Text；交互内容需验证 focusable、关闭路径和键盘行为 |

任务转换建议用明确的阶段数据驱动：准备（未知进度）→ 执行（有可靠总量才显示比例）→ 成功/失败。只禁用会重复提交的动作；关闭、取消、后台继续是否可用由任务语义决定，不能用一份 isLoading 隐藏所有退出入口。

## Color and appearance controls

实践入口：[颜色编辑](source-verification.md#community-color-editing)。精确签名以 `docs/components/colorpalette.md`、`docs/components/colorpicker.md` 与目标版本源码为准。

| 组件 | 使用方式 | 状态与提交 |
|---|---|---|
| `ColorPalette` | 色板式选择，color + onColorChanged；按需求决定 includeGrayColumn/showPreview | 与同页面颜色输入共用草稿，不用“已选色”和“预览色”两套互不相干状态 |
| `ColorPicker` | 连续调色，color + onColorChanged，colorSpace 明确使用 HSV/OKHSV/OKLAB/OKLCH 中目标版本支持的值 | 颜色空间、alpha 支持是编辑策略；不要按颜色字符串长度推断图形控件已禁止透明度 |
| `MiuixTheme` / `ThemeController` | 根部组织模式、动态色与文字层级；完整设置见 [setup-and-theme](setup-and-theme.md) | 设置页改主题输入，业务组件消费角色 token，不为每个预览卡复制一套应用主题 |
| 图标、squircle 与 blur | 依赖/平台能力按 [styling-icons-and-effects](styling-icons-and-effects.md) 配置 | 首先使用组件自带边界；需要自定义捕获/裁剪时保证反馈与内容同一视觉表面 |

调色弹窗用一份 Color 草稿与允许不完整的 HEX 文本草稿；仅在文本格式合法时更新 Color。确认提交，取消保留原值；打开新的编辑对象时重置草稿。仅 RGB 的产品在提交时显式处理 alpha；不要把暂时输入不完整的字符串变成黑色或直接保存。自定义解析器属于应用代码，不从示例项目的 Android 扩展推断跨平台 API。

## Advanced composition

- 数值输入、重置、依赖设置、草稿、多色和实时预览：[settings-and-editors](settings-and-editors.md)。
- 列表刷新、筛选后多选、三态树、排序、批量任务和反馈：[collections-and-workflows](collections-and-workflows.md)。
- 固定清单全部 15 个项目的依赖和代表源码：[community-projects](community-projects.md)。

## Menu and overlay continuation

- 完整设置行与行/图标菜单：[preferences-and-menus](preferences-and-menus.md#usage-contracts)。
- Dialog、BottomSheet、ListPopup 与 cascading popup：[overlays-and-windows](overlays-and-windows.md#usage-by-overlay-family)。
- 交互、密度、精简文案、主题与编译/运行验证仍遵循 [Code Delivery Contract](../SKILL.md#code-delivery-contract)。社区实现只作为取材来源，不作为未经验证的源码模板。
