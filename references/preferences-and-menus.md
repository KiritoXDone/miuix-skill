# Preference and Dropdown Menu Components

## Choose the Family

| User need | Prefer | Why |
|---|---|---|
| Binary/value control inside a form or custom layout | Basic `Switch`, `Checkbox`, `RadioButton`, or `Slider` | The surrounding layout owns the label and grouping |
| Complete settings row with title, summary, and row interaction | Matching `*Preference` component | The component owns HyperOS-style row structure, enabled state, and trailing control/value |
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
- For a small dependent row, `AnimatedVisibility` can express conditional presence. Large dynamic groups should follow [row-level lazy guidance](performance-and-animation.md). Hide the setting when irrelevant or unsupported. Keep the row visible with `enabled = false` when discoverability matters, and explain the prerequisite in `summary`.
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
