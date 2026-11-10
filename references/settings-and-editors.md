# Settings and Editors

面向完整设置页、参数面板与编辑流程。单组件参数见 [component-usage](component-usage.md) 和 [preferences-and-menus](preferences-and-menus.md)。这里将所有项目中的相关实现归纳为可选择的组合模式；不要求目标项目采用其中某个 DSL、持久化库或页面结构。

## Pattern map

| 需求 | 组合 | 状态所有者 | 证据入口 |
|---|---|---|---|
| 立即生效的开关/选项 | SwitchPreference / RadioButtonPreference / DropdownPreference | 已确认配置 + 待提交动作 | [KernelSU](community-projects.md#kernelsu)、[StatusBarLyric](community-projects.md#statusbarlyric) |
| 条件展开的一组设置 | 控制行 + 条件内容/独立 lazy 行 | 配置值与 UI 展开状态分别建模 | [HyperStar](community-projects.md#hyperstar)、[Nexio](community-projects.md#nexio) |
| 滑块 + 精确值 + 重置 | SliderPreference 或 Slider + 数值输入 Dialog | 一份数值草稿 + 范围/精度/默认值 | [HyperIsland](community-projects.md#hyperisland)、[HyperStar](community-projects.md#hyperstar) |
| 复杂表单与输入建议 | TextField / SearchBar + 校验 + 列表建议 | 以编辑对象身份划分的草稿 | [Updater](community-projects.md#updater)、[YumeBox](community-projects.md#yumebox) |
| 单色/多颜色编辑 | ColorPicker/ColorPalette + HEX + swatch 列表 | 草稿色/草稿集合 + 当前编辑项 | [XiaomiHelper](community-projects.md#xiaomihelper)、[Lyricon](community-projects.md#lyricon) |
| 拖动排列同时勾选 | CheckboxPreference + lazy 列表 + 拖拽能力 | 顺序与选择集独立 | [HyperLyric](community-projects.md#hyperlyric) |
| 批量修改一部分字段 | 目标集合 + 有变化字段的 patch + 应用动作 | 待提交 patch，区分不修改/设置/清空 | [HyperIsland](community-projects.md#hyperisland) |
| 外观参数和实时预览 | Slider/Dropdown/ColorPicker + 预览容器 | 预览读取同一草稿，保存单独触发 | [HyperLyric](community-projects.md#hyperlyric)、[XiaomiHelper](community-projects.md#xiaomihelper) |

## Saving policy and dependent settings

先决定每个编辑动作的提交时机，不由使用哪种 Miuix 组件推断：

- **即时修改**：回调发出更新；存储/服务可能失败时，区分提交中与已确认值。仅在明确采用乐观更新时先改显示，并定义失败恢复。不要同时在行、内部控件与 effect 写三遍配置。
- **确认后提交**：打开时读取基线，控件只改草稿，保存统一校验/提交；取消、外点或返回保留基线。不要复用一次未提交的旧草稿给另一个对象。
- **需要应用或重启**：分别表达保存值与实际生效值。只有用户需要采取下一步操作时，显示短状态和明确动作；后台服务、权限或重启是应用行为，不写入组件通用回调。
- **批量 patch**：字段未触及不等于 false、0 或空值。将“不修改”“设为具体值”“显式清空/恢复默认”区分开；保存时先固化目标集合，处理失败后不悄悄扩大目标范围。

依赖设置由实际前提决定 enabled/可见性。单纯“展开更多”是临时 UI 状态，不能顺便关闭业务功能；业务开关关闭后是否保留子值按产品契约处理，避免隐藏行就删除配置。小组可条件组合；大组采用独立 lazy 行。相关控件的状态上提到同一所有者，避免父子 remembered 副本互相覆盖。

## Numeric editor with slider and reset

组成：短标题、滑块、必要的当前值/单位、可选精确输入与重置。优先 `SliderPreference` 的 valueText/endActions 或公开 slots；只在其不能表达产品需求时创建 wrapper。

1. 用一个数值表示草稿；Slider、数字显示、精确输入确认和重置都更新它。范围、单位和格式来自同一设置定义。
2. 拖动时更新草稿；昂贵写入按产品选择 onValueChangeFinished 或显式保存。精确输入确认与重置走相同规范化和提交路径，不能绕过校验。
3. 文本编辑保留原始字符串及光标，允许必要的中间态（空、负号、尚未完成的小数）。输入键盘类型不等于验证；解析、范围、精度以及 finite 值在确认/适当提交边界检查。
4. 非法输入不自动变成 0 或默认值。需要时显示一句短提示，并阻止无效提交；取消不覆盖已有值。处理粘贴、键盘 locale、小数点和删除操作。
5. 离散步数按目标版本契约计算：Slider 的 steps 是两端之间的点数，0 是连续模式。不要把十进制精度无条件变成巨大 steps 数组；细密范围可用连续拖动 + 明确量化/精确输入。
6. 重置动作只在有实际默认值且用户需要时显示；动态默认值来自设置定义/平台能力，不能把范围下界假定为默认值。默认值超出当前范围时重新验证。

示例项目中的 NumberTextField、FilterFloat、SliderResetAction 和 Pr/SpPreference 都是应用封装。当前 Miuix TextField 不因此获得同名 isError 等参数。公共签名仍按 [组件用法](component-usage.md#input-search-and-selection-controls) 核查。

## Object drafts and external updates

编辑器至少区分对象 ID、已加载基线、草稿、是否有改动、提交状态与结果。草稿可以含业务值和文本输入值，但不与页面路由参数或全局主题状态混为一体。

- 从未加载 → 已加载时初始化；切换对象 ID 时重建；同一对象的数据刷新不应无条件覆盖用户正在输入的内容。决定保留、重载或显式冲突提示。
- 用稳定对象 ID 绑定输入和临时 UI；不要用整个大 uiState 作为 remember key，导致图标刷新或任务进度重建草稿。
- 异步校验/自动完成携带查询或对象身份，丢弃过期结果；焦点、查询、建议显示状态分别可解释。选择建议更新值，不重复触发同一次提交。
- 对有意义的变化生成 patch，避免“保存”重写未编辑字段。提交失败保留草稿并释放 isSaving 门控；取消旧请求不能清理新请求的状态。
- 文本框缓存同步须保留选择区与 IME 组合；目标支持时优先 TextFieldState/inputTransformation，不同时建立互相回写的两份文本状态。

本节参考 [YumeBox](community-projects.md#yumebox) 的编辑草稿、[Updater](community-projects.md#updater) 的表单、[Lyricon](community-projects.md#lyricon) 的数字中间态。实现不依赖这些项目的数据类或平台服务。

## Color collections and precise color input

单色模式见 [颜色组件](component-usage.md#color-and-appearance-controls)。多色/渐变编辑增加一个“当前编辑项”，而不是为每个 swatch 独立创建永久 ColorPicker。

- 使用稳定项 ID 管理色集合与当前项。增加、删除、重排在事件中完成；删除当前项时选择相邻项或明确的空态，不能在 composition 中发现空列表后直接写回。
- 调色器和 HEX 修改同一选中项的草稿；文本未合法前保留最后一个合法预览值。改变选中 swatch 时同步草稿，不把上一项未完成文本提交到下一项。
- 明确是否允许空集合、单色、透明度和重复色。渐变顺序有语义，不按颜色数值偷偷重排；位置/权重若存在与对应 ID 同时保存。
- 确认一次提交整组数据，取消不修改基线。产品若明确采用即时预览到真实目标，须能撤回或明确区分预览与保存。
- swatch 是独立交互表面，按自身形状裁剪选中/按压反馈；选中指示可用边框/图标，不能只依赖色差。颜色名称或必要数值才显示，省略开发解释。

复用 [Lyricon](community-projects.md#lyricon) 的集合编辑思路及 [XiaomiHelper](community-projects.md#xiaomihelper) 的 HEX/alpha 处理；不复制它们的组合期状态修正或平台专属解析器。

## Preview-driven appearance settings

预览读取当前草稿，提交才影响正式配置，除非产品明确要求立即生效。文字、图标、间距、背景或动画的预览要使用与真实组件相同的数据含义和约束；不通过根 LocalDensity 缩放整个应用来模拟某个局部组件尺寸。

- 在有限预览区域表现实际容器，必要时提供明暗背景或文字缩放变体；预览不能因方便而重新创建全局导航/主题状态。
- 连续 Slider 或颜色变更先更新预览，I/O 分离；帧级状态按 [性能指南](performance-and-animation.md) 延迟读取。
- 不可见的动效停止工作，重新可见有明确恢复路径；减少动效/禁用效果时仍能理解配置值。
- 预览区域的点击如果只是选择预览对象，不执行真实页面的业务动作；正式组件动作测试使用真实交互验证。

## Searchable settings and configuration navigation

设置搜索索引保存稳定设置 ID、短标题及必要检索词。搜索结果导航到目标页面/字段，再根据真实能力决定启用；不要复制整个设置状态到搜索层，或只让可见行进入索引而使关闭的依赖项永远搜不到。

区分“跳转到设置”“直接修改结果行”和“展开该组”。返回搜索时保留用户查询及合理的列表位置；不因结果变化重置整个路由栈。多语言索引随资源变化重建，检索词不需要都展示为标签。

大型设置页可组合页面/section 构建器，但 DSL 不应强制绑定某个存储后端或隐藏异步失败。参考 [HyperStar](community-projects.md#hyperstar)、[StatusBarLyric](community-projects.md#statusbarlyric) 的分组/搜索意图，组件实现仍使用当前公开 API。

## Editor validation

只检查任务涉及的组合：更换编辑对象、异步刷新、取消再开、重置、空/非法数值、精度边界、失败重试、配置依赖切换、明暗/大字体、键盘遮挡和多窗口。按钮/标签保持短语，必要提示保持短句。静态说明与 snippet 未编译时，按统一交付合同明确其验证层级。
