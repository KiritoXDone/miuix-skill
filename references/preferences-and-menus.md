# Preference and Dropdown Menu Components

## Choose the Family

| User need | Prefer | Why |
|---|---|---|
| Binary/value control inside a form or custom layout | Basic `Switch`, `Checkbox`, `RadioButton`, or `Slider` | The surrounding layout owns the label and grouping |
| Complete settings row with a short title, optional summary, and row interaction | Matching `*Preference` component | The component owns HyperOS-style row structure, enabled state, and trailing control/value |
| Plain text option selection in a settings row | `OverlayDropdownPreference` or `WindowDropdownPreference` | Direct `List<String>` + selected-index API and current-value display |
| Rich options with icons/summaries, or spinner dialog mode | `OverlaySpinnerPreference` or `WindowSpinnerPreference` | Uses richer `DropdownItem` content and spinner-specific presentation |
| Standalone anchored action menu rather than a settings row | `*DropdownMenu` family | The caller owns the anchor and menu action semantics |

Choose Overlay versus Window lifetime with [Overlay and Window Components](overlays-and-windows.md#choose-the-host-first). For settings-page grouping and state ownership, continue with [UI composition patterns](usage-patterns.md#settings-and-grouped-content). Verify the chosen row's exact call shape in its doc/demo/source through [Source verification](source-verification.md).

按钮/选项的反馈遵循 [pressed、selected 与 held 的区别](styling-icons-and-effects.md#pressed-selected-and-held-state)。选中值传给 `selected` / `checked` / `selectedIndex`；`holdDownState` 用于交互明确要求的持续按压视觉，例如弹窗入口，不表达业务选中值。原生 Preference 已负责同一动作的点击与状态反馈，不在外面再包一层 toggleable/clickable。

选项行的高亮可以覆盖整行，但应在所属 Card 或 lazy 分段的外轮廓内；独立小按钮的高亮应止于按钮表面。矩形漏角或范围过大时，按 [反馈边界检查](styling-icons-and-effects.md#feedback-bounds-and-corner-clipping) 定位 indication 与裁剪层。

## Choose a Menu Variant

| Need | Choose |
|---|---|
| A settings row that displays one current value and owns a selected index | `*DropdownPreference` or richer `*SpinnerPreference`, not a menu |
| A full `BasicComponent`-style row that opens flat actions, grouped choices, or multi-select items | `OverlayDropdownMenu` / `WindowDropdownMenu` |
| A compact toolbar/action icon that opens the same flat menu model | `OverlayIconDropdownMenu` / `WindowIconDropdownMenu` |
| An icon action whose top-level items open child choices | `OverlayIconCascadingDropdownMenu` / `WindowIconCascadingDropdownMenu`; verify the pinned source before relying on a nesting-depth limit |

“Icon” describes the anchor (`IconButton`) rather than promising an icon on every menu item. Plain menu items may still expose selection state. Choose Overlay versus Window after choosing flat versus cascading and row versus icon anchor.

## Preference Components (miuix-preference)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| ArrowPreference | `docs/components/arrowpreference.md` | `docs/demo/ArrowPreferenceDemo.kt` | `miuix-preference/.../preference/ArrowPreference.kt` |
| SwitchPreference | `docs/components/switchpreference.md` | `docs/demo/SwitchPreferenceDemo.kt` | `miuix-preference/.../preference/SwitchPreference.kt` |
| CheckboxPreference | `docs/components/checkboxpreference.md` | `docs/demo/CheckboxPreferenceDemo.kt` | `miuix-preference/.../preference/CheckboxPreference.kt` |
| RadioButtonPreference | `docs/components/radiobuttonpreference.md` | `docs/demo/RadioButtonPreferenceDemo.kt` | `miuix-preference/.../preference/RadioButtonPreference.kt` |
| OverlayDropdownPreference | `docs/components/overlaydropdownpreference.md` | `docs/demo/OverlayDropdownPreferenceDemo.kt` | `miuix-preference/.../preference/OverlayDropdownPreference.kt` |
| OverlaySpinnerPreference | `docs/components/overlayspinnerpreference.md` | `docs/demo/OverlaySpinnerPreferenceDemo.kt` | `miuix-preference/.../preference/OverlaySpinnerPreference.kt` |
| WindowDropdownPreference | `docs/components/windowdropdownpreference.md` | `docs/demo/WindowDropdownPreferenceDemo.kt` | `miuix-preference/.../preference/WindowDropdownPreference.kt` |
| WindowSpinnerPreference | `docs/components/windowspinnerpreference.md` | `docs/demo/WindowSpinnerPreferenceDemo.kt` | `miuix-preference/.../preference/WindowSpinnerPreference.kt` |
| SliderPreference | `docs/components/sliderpreference.md` | — | `miuix-preference/.../preference/SliderPreference.kt` |
| RangeSliderPreference | `docs/components/rangesliderpreference.md` | — | `miuix-preference/.../preference/SliderPreference.kt` |

### RadioButtonPreference in v0.9.4

`RadioButtonPreference` remains a controlled row: hoist `selected` and update it from `onClick`. Version 0.9.4 removes the old `titleColor` and `summaryColor` parameters and groups them in `RadioButtonPreferenceColors` through `colors = RadioButtonPreferenceDefaults.radioButtonPreferenceColors(...)`; selected title and summary colors default to the theme primary color. The entire row owns the radio interaction and haptic feedback, so do not add a second clickable wrapper around the radio control.

Use `radioButtonLocation = RadioButtonLocation.Start` or `.End` when the control position matters. `startAction`, nullable `endActions`, and `bottomAction` are extension slots, and custom interactive content must receive the same `enabled` state explicitly.

Version 0.9.4's cascading menu state is tracked by entry/item position rather than the `DropdownItem` object reference. This lets selected-state values refresh without closing the submenu, but requires the entry and item order to remain stable while the popup is visible. Predictive back can also interrupt and reverse the active enter/expand track.

### RangeSliderPreference quick cue

There is no dedicated `docs/demo/` file, but the tagged component doc contains complete examples. It is the settings-row counterpart for selecting two related values, with `value` + `onValueChange` as the controlled state; the most common additional parameters are `valueRange`, `steps`, `enabled`, `summary`, and optional `valueText`. Read the shared `SliderPreference.kt` source when using key points, magnetic snapping, haptics, or custom slider colors.

## Enabled and Dependent States

- Public Preference components expose `enabled`. With `enabled = false`, the row is not clickable, built-in trailing controls such as the `Switch` are also disabled, and title/summary/action colors resolve through disabled roles. The controlled value remains the caller's state and should not be reset merely because the row is disabled.
- Pass the same prerequisite to every interactive child you supply yourself. A custom `startAction`, `endActions`, or `bottomAction` slot cannot infer how its nested buttons should be disabled.
- For a small dependent row, `AnimatedVisibility` can express conditional presence. Large dynamic groups should follow [row-level lazy guidance](performance-and-animation.md). Hide the setting when irrelevant or unsupported. Keep the row visible with `enabled = false` when discoverability matters, and show a short prerequisite hint in `summary` only if users need it to proceed. Omit feature explanations and redundant helper text; see [UI copy rules](design-language.md#user-facing-copy).
- For linked settings, derive child `enabled`/visibility from the parent value while hoisting both values to the same owner. Do not duplicate a second local Boolean inside the Preference.

## Dropdown Menu Components (miuix-preference/menu/)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| OverlayDropdownMenu | `docs/components/overlaydropdownmenu.md` | `docs/demo/OverlayDropdownMenuDemo.kt` | `miuix-preference/.../menu/OverlayDropdownMenu.kt` |
| OverlayIconDropdownMenu | `docs/components/overlayicondropdownmenu.md` | `docs/demo/OverlayIconDropdownMenuDemo.kt` | `miuix-preference/.../menu/OverlayIconDropdownMenu.kt` |
| OverlayIconCascadingDropdownMenu | `docs/components/overlayiconcascadingdropdownmenu.md` | `docs/demo/OverlayIconCascadingDropdownMenuDemo.kt` | `miuix-preference/.../menu/OverlayIconCascadingDropdownMenu.kt` |
| WindowDropdownMenu | `docs/components/windowdropdownmenu.md` | `docs/demo/WindowDropdownMenuDemo.kt` | `miuix-preference/.../menu/WindowDropdownMenu.kt` |
| WindowIconDropdownMenu | `docs/components/windowicondropdownmenu.md` | `docs/demo/WindowIconDropdownMenuDemo.kt` | `miuix-preference/.../menu/WindowIconDropdownMenu.kt` |
| WindowIconCascadingDropdownMenu | `docs/components/windowiconcascadingdropdownmenu.md` | `docs/demo/WindowIconCascadingDropdownMenuDemo.kt` | `miuix-preference/.../menu/WindowIconCascadingDropdownMenu.kt` |

## Standalone single-choice dialog

`WindowDropdownDialog` is public in `miuix-preference/.../popup/WindowDropdownPopup.kt`; do not assume the source file is named after the function. It accepts a `DropdownEntry` or multiple entries. The single-entry overload defaults `collapseOnSelection` to true; the grouped overload defaults it according to `entries.size <= 1`.

Use it for immediate selection with `DropdownDefaults.dialogDropdownColors()` rather than recreating a list of buttons plus confirm. The entry items carry `DropdownItem.onClick`; `onSelect` is often an application-wrapper callback, **not** a parameter to invent on this API. Selection state stays with the caller. This function uses `onDismiss`, not `onDismissRequest`; the callback must update the caller's `show` state when selection, cancellation or back requests dismissal. Verify the required title, cancellation text, `onDismissFinished` and color arguments before emitting code.

If an application wrapper caches entries, include option data and selected state among its real keys. Use `rememberUpdatedState` for a callback that must remain fresh without invalidating the whole entry cache. Do not freeze selection to stabilize callbacks, and do not add duplicate dismissal when the chosen component already invokes the same controlled callback.

## Usage contracts

社区组合见 [表单与选择实践](source-verification.md#community-form-and-selection)；以下按 v0.9.4 公共 API 组织，不沿用项目自定义 Preference 名称。summary 和次级标签默认省略。

| 组件族 | 状态和动作 | 实际组合方式 |
|---|---|---|
| `ArrowPreference` | title + onClick；没有隐含业务选择值 | 打开下一页、编辑弹窗或详情；用 startAction 放图标。导航箭头只是 affordance，不代替导航事件；弹窗需要时才绑定 held |
| `SwitchPreference` | checked + onCheckedChange | 设置行直接绑定上提的 Boolean；无需另加可点击 Row 或独立持久化的 Switch。禁用依赖传给整行与自定义动作 |
| `CheckboxPreference` | checked + onCheckedChange | 多选或待确认选项；单次提交的表单维护本地草稿集合，确认后交给业务层；筛选后仍按稳定 ID 保存选择 |
| `RadioButtonPreference` | selected + onClick | 互斥项由同一 selected key 派生；选中标题/summary 的颜色由 colors 工厂控制，内部 radio 已是展示端，勿再加第二个 onClick |
| `SliderPreference` | value + onValueChange，valueRange/steps，必要时 valueText/onValueChangeFinished | 单值设置直接用完整行，拖动草稿与持久化时机分离；valueText 只显示必要值/单位，不附功能说明 |
| `RangeSliderPreference` | 一份 ClosedFloatingPointRange 的 value + onValueChange | 上下限作为一个设置；显示与提交同时包含两端，不能两边各自写回造成瞬间非法范围 |
| `OverlayDropdownPreference` / `WindowDropdownPreference` | 简单 List<String> + selectedIndex + onSelectedIndexChange；也有 entry 模型重载 | 少量互斥文本选项；从业务值映射 index，再把回调映回业务值。列表改变/为空时明确处理选择与 enabled，不盲用 index 0 |
| `OverlaySpinnerPreference` / `WindowSpinnerPreference` | 带图标/必要辅助内容的 DropdownItem 列表，或 entries；索引重载同上 | 需要富选项时采用，showValue 控制是否在入口重复显示值。spinner 的配色参数名为 spinnerColors；不要沿用 DropdownPreference 的 dropdownColors 名称 |
| `OverlayDropdownMenu` / `WindowDropdownMenu` | DropdownEntry/entries 中每项持有动作及可选 selected | 完整行作为菜单入口；动作菜单与业务单选区分，重命名/删除等动作不需要持久 selected |
| `OverlayIconDropdownMenu` / `WindowIconDropdownMenu` | 同样的菜单数据，图标是入口；onExpandedChange 处理展开相关副作用 | 放顶栏/工具栏；如需打开时清焦点，用公开展开回调，不用额外 clickable 拦截。入口 held/关闭优先由原生组件管理 |
| `OverlayIconCascadingDropdownMenu` / `WindowIconCascadingDropdownMenu` | entry/item 组织子菜单；选择属于具体叶项 | 有真实层级的命令才用；展开期间避免随刷新重排父子项，验证返回、焦点、鼠标和预测返回 |

### Identity, selection, and events

- 业务层保存稳定 ID/enum，UI 层根据当前 options 求 selectedIndex；示例项目中为显示兜底的索引值不代表新项目应自动选中首项。
- DropdownEntry 是库模型；项目里同名的 DropdownItem composable、DropDownOption、Choice 等可能只是 wrapper，先查 import。`DropdownItem` 的过时构造参数也需以目标源码为准。
- 在索引重载和 DropdownItem.onClick 同时配置业务动作前检查实现；Spinner 索引重载可顺序调用两个回调，同一提交不要各写一遍。
- 缓存 options/entries 包含真实选项和选中数据；回调需要最新引用时使用 rememberUpdatedState。任何缓存都不能冻结 selected 状态。
- 想在打开菜单时清焦点，使用所选组件公开的 onExpandedChange 等回调；不提供该能力时选择更合适的公开入口或小型应用封装，而不是把同动作点击层叠在原生行外面。

### Minimal selected-value row

局部示例需要已有 MiuixTheme 与合适的 Scaffold（Overlay host）。selectedIndex 来自调用方，options 更新时由调用方同步选择；超出范围先显示为禁用，避免隐式改写业务值。

```kotlin
import androidx.compose.runtime.Composable
import top.yukonga.miuix.kmp.preference.OverlayDropdownPreference

@Composable
fun ModeRow(
    options: List<String>,
    selectedIndex: Int,
    onSelectedIndexChange: (Int) -> Unit,
) {
    OverlayDropdownPreference(
        title = "模式",
        items = options,
        selectedIndex = selectedIndex,
        enabled = options.isNotEmpty() && selectedIndex in options.indices,
        onSelectedIndexChange = onSelectedIndexChange,
    )
}
```
