# Overlay and Window Components

## Choose the Host First

| Required lifetime/host | Choose | Integration rule |
|---|---|---|
| Page-scoped popup that follows the current page and its bounds | `Overlay*` | Require the intended ancestor `Scaffold`; reuse it rather than creating an accidental nested host |
| Popup independent of the current page's Scaffold, including cross-page/global UI | `Window*` | Use the Window counterpart; no Scaffold popup host is required |

Resolve standalone versus existing host ownership in [Project setup and theme](setup-and-theme.md). For visibility, dismissal, and integrated action patterns, read [Overlay and Window usage patterns](usage-patterns.md#overlay-and-window-components); verify exact parameters through [Source verification](source-verification.md).

## Dismissal and System Back

- At the current stable snapshot, dialog, bottom-sheet, and list-popup layouts route system back dismissal to `onDismissRequest`; Window variants also use their platform window host where applicable. The caller must update the controlled `show` state. Always provide the callback for a dismissible popup.
- A manual `BackHandler`/`NavigationBackHandler` is normally unnecessary and can compete with the component's own handler. Add one only for an explicit product rule such as a non-dismissible confirmation, and verify that it does not double-handle back or predictive-back gestures.
- `onDismissFinished` runs after the hide animation completes, not when dismissal is merely requested, and may be skipped if the hide is cancelled by showing the popup again. Use it for delayed cleanup; use `onDismissRequest` for the state transition.
- Do not depend on undocumented stacking order. Prefer one active page popup; if a deliberate parent/child flow uses two, close the child first and test back, focus, scrim, and accessibility on every target platform.
- Version 0.9.4 adds `WindowNavigationEventScope` in `miuix-ui/.../utils/`. Window dialogs, sheets, and list/cascading popups apply it automatically so `NavigationBackHandler`/predictive-back handlers resolve against the focused platform window. Wrap custom separate-window hosts that contain the same handlers.
- Animated dialog, sheet, and list-popup layouts place their library `graphicsLayer` before the caller's `modifier`; caller draw/transform modifiers therefore follow the component animation. Keep this ordering when building a wrapper and do not add a second corrective transform.

## Overlay Components (require Scaffold wrapper)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| OverlayDialog | `docs/components/overlaydialog.md` | `docs/demo/OverlayDialogDemo.kt` | `miuix-ui/.../overlay/OverlayDialog.kt` |
| OverlayBottomSheet | `docs/components/overlaybottomsheet.md` | `docs/demo/OverlayBottomSheetDemo.kt` | `miuix-ui/.../overlay/OverlayBottomSheet.kt` |
| OverlayListPopup | `docs/components/overlaylistpopup.md` | `docs/demo/OverlayListPopupDemo.kt` | `miuix-ui/.../overlay/OverlayListPopup.kt` |
| OverlayCascadingListPopup | `docs/components/overlaycascadinglistpopup.md` | `docs/demo/OverlayCascadingListPopupDemo.kt` | `miuix-ui/.../overlay/OverlayCascadingListPopup.kt` |

## Window Components (standalone popups, no Scaffold needed)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| WindowDialog | `docs/components/windowdialog.md` | `docs/demo/WindowDialogDemo.kt` | `miuix-ui/.../window/WindowDialog.kt` |
| WindowBottomSheet | `docs/components/windowbottomsheet.md` | `docs/demo/WindowBottomSheetDemo.kt` | `miuix-ui/.../window/WindowBottomSheet.kt` |
| WindowListPopup | `docs/components/windowlistpopup.md` | `docs/demo/WindowListPopupDemo.kt` | `miuix-ui/.../window/WindowListPopup.kt` |
| WindowCascadingListPopup | `docs/components/windowcascadinglistpopup.md` | `docs/demo/WindowCascadingListPopupDemo.kt` | `miuix-ui/.../window/WindowCascadingListPopup.kt` |

## Dialog sizing and large screens

In the current stable snapshot, `OverlayDialog` and `WindowDialog` both accept:

- `maxWidth: Dp = DialogDefaults.MaxWidth` to prevent dialogs from stretching across tablet/desktop windows.
- `largeScreen: Boolean? = null` to override the centered scale/fade versus bottom-attached presentation; `null` uses the window-size detection.
- `cornerRadius: Dp? = null` to override the centered radius or bottom-attached screen-radius-derived value.

`DialogDefaults.MaxWidth` existed before v0.9.4; the new public `maxWidth` parameter exposes that limit for both dialog wrappers. `DialogDefaults.CornerRadius` is new. Use both Defaults when a product does not have a deliberate design value. Keep the dialog's content and dismissal state controlled by the caller; these parameters do not remove the need for a `Scaffold` ancestor for `OverlayDialog`.

The default large-screen breakpoint is 840 dp wide and 480 dp high. Centered dialogs use the 32 dp `CornerRadius`; bottom-attached dialogs derive a radius from the screen corner and clamp it to 32..48 dp unless `cornerRadius` is supplied.

## Long dialogs and action reachability

At v0.9.4, the shared `DialogContentLayout.kt` caps large-screen height but leaves the compact content height unspecified. Do not assume a long body will automatically scroll while keeping actions visible.

Give the body a viewport-aware maximum height, a weighted scroll area (`weight(1f, fill = false)` in a bounded Column), and non-weighted actions. Derive the limit from the actual window, safe areas and IME; reuse a product limit only if it remains valid under these constraints. Recheck long text, large font, compact landscape and keyboard-open states.

For full-width option rows, distinguish the dialog's `insideMargin` from each row's own horizontal padding; removing both can put labels against the edge. Let full-width rows own their text insets, and preserve the title and action spacing required by the selected dialog layout. For a held trigger, follow [held-feedback lifecycle](styling-icons-and-effects.md#held-feedback-lifecycle): simple wrappers can bind holdDownState to show, while native dropdowns own exit-completion release. Do not conflate held feedback with a selected option.

For immediate mutually exclusive selection use the real [WindowDropdownDialog](preferences-and-menus.md#standalone-single-choice-dialog) instead of rebuilding a confirm-based form unless the product explicitly needs a draft/confirm interaction.

## BottomSheet: IME is not all safe-area padding

Verified in `miuix-ui/.../layout/BottomSheetContentLayout.kt` and `window/WindowBottomSheet.kt` at v0.9.4:

- `defaultWindowInsetsPadding = true` installs `imePadding()`. Top status/caption/cutout values also participate in the maximum-height calculation; that calculation does not pad the content's bottom edge.
- Inspect remaining content insets. When bottom system UI is not already accounted for, apply the needed bottom portion of `systemBars.union(displayCutout)`; `systemBars` includes caption-bar information. Do not add IME a second time if the host already owns it.
- The default sheet is centered horizontally and width-limited (640 dp at this tag). Blindly padding just one side can shift its contents away from center. Still test narrow landscape, custom full-width sheets and side cutouts: “never handle horizontal insets” is not a universal rule.
- Default internal margins are styling, not proof that system UI is avoided. Account for consumed insets and the actual platform window.
- Loading-to-content and item-count changes can change wrap-content height abruptly. An application wrapper may animate measured content size with a verified Miuix `folmeSpring` spec; avoid competing with enter/exit/IME animation or clipping fixed actions. Implement any additional inset/size wrapper within the application, using the actual host contract.

Check open/close, focused field, IME dismissal, freeform/caption bars and resizes on the target platform before claiming this behavior verified.

## Usage by overlay family

以下选型补充上方源码目录。社区组合证据见 [表单与选择](source-verification.md#community-form-and-selection)、[进度与弹层](source-verification.md#community-progress-and-overlays)；未观察到的社区变体直接依据官方 doc/demo，不以名称相似推断调用形状。

| 组件 | 适合的流程 | 调用方负责 |
|---|---|---|
| `OverlayDialog` | 当前页面的确认、输入草稿或颜色编辑 | 页面宿主、show/dismiss、草稿初始化及确认提交；普通输入错误留在弹窗内，不关闭后丢草稿 |
| `WindowDialog` | 独立于页面 Scaffold 的确认/全局流程 | 当前主题/密度与实际窗口行为；单独验证焦点、IME、back。独立 window 不是跨页面共享业务状态的自动实现 |
| `OverlayBottomSheet` | 页面内较长的表单、列表或上下文详情 | 内容滚动、操作区、安全区与返回；列表/输入超高时保持底部操作可达 |
| `WindowBottomSheet` | 独立窗口承载的详情或多阶段任务 | 将准备/确认/执行/结果建模为明确状态，Sheet 作为稳定宿主；任务退出与关闭 UI 的后果分别定义 |
| `OverlayListPopup` | 页面内锚定到输入/按钮的建议或菜单 | anchor、show、定位、可用空间、选择和关闭；输入建议不必加遮罩，宽高应由窗口和内容约束而非固定设备值 |
| `WindowListPopup` | 需要独立窗口定位的短列表 | 与 Overlay 相同的数据/选择责任，另核对屏幕坐标、窗口 inset、桌面缩放与焦点 |
| `OverlayCascadingListPopup` | 页面内有真实父子层级的菜单 | 稳定项顺序和层级、叶项动作、逐层返回；若原生 cascading menu 已满足，不再自写 popup 栈 |
| `WindowCascadingListPopup` | 独立窗口中的级联菜单 | 在上项基础上验证窗口边缘翻转/空间不足和子层焦点，不依赖内部 z-index 作为应用流程协议 |

复杂编辑的基线/草稿/提交与多色集合见 [settings-and-editors](settings-and-editors.md)；后台任务、批量应用和日志见 [collections-and-workflows](collections-and-workflows.md)。

### Form, picker, and background-task lifetimes

- 编辑弹窗：打开时创建当前对象的草稿，输入过程中只更新草稿；确认校验通过才提交。取消/返回不写回。复用同一 show 状态编辑不同对象时，初始化键包含对象身份，不能沿用上一份草稿。
- 颜色选择器：picker、HEX 和预览共用草稿，非法/未完成文本不覆盖最后合法颜色；同 [ColorPicker 用法](component-usage.md#color-and-appearance-controls)。
- 任务 Sheet/Dialog：关掉 UI、取消任务、留后台运行是不同事件。根据实际阶段禁用重复操作并保留可用的退出路径，回到页面时从任务所有者恢复状态。不要把网络/安装协程绑在某个临时内容分支的 composition 生命周期上。
- 替换内容时让外层宿主和 show 稳定；内部在需要时做尺寸过渡。只在真正结束时清理，onDismissFinished 不作为业务完成信号。
- 动态主题、无障碍字号和 IME 是这些交互的共同条件，不照搬示例应用的固定高度、字数或补充说明文案。
