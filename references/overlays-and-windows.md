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

Give the body a viewport-aware maximum height, a weighted scroll area (`weight(1f, fill = false)` in a bounded Column), and non-weighted actions. Mishka's 500 dp limit is an application value; account for the actual window and IME instead of hardcoding it for every platform. Recheck long text, large font, compact landscape and keyboard-open states.

For full-width option rows, distinguish the dialog's `insideMargin` from each row's own horizontal padding; removing both can put labels against the edge. Mishka uses `DpSize(0.dp, 24.dp)` around rows that already inset their text, preserving vertical title space. For a held trigger, follow [popup-trigger feedback](styling-icons-and-effects.md#hold-only-the-popup-trigger): simple wrappers can bind holdDownState to show, while native dropdowns own exit-completion release. Do not conflate held feedback with a selected option.

For immediate mutually exclusive selection use the real [WindowDropdownDialog](preferences-and-menus.md#standalone-single-choice-dialog) instead of rebuilding a confirm-based form unless the product explicitly needs a draft/confirm interaction.

## BottomSheet: IME is not all safe-area padding

Verified in `miuix-ui/.../layout/BottomSheetContentLayout.kt` and `window/WindowBottomSheet.kt` at v0.9.4:

- `defaultWindowInsetsPadding = true` installs `imePadding()`. Top status/caption/cutout values also participate in the maximum-height calculation; that calculation does not pad the content's bottom edge.
- Inspect remaining content insets. Mishka adds the **bottom** of `systemBars.union(displayCutout)` to its content; `systemBars` includes caption-bar information. Do not add IME a second time if the host already owns it.
- The default sheet is centered horizontally and width-limited (640 dp at this tag). Blindly padding just one side can shift its contents away from center. Still test narrow landscape, custom full-width sheets and side cutouts: “never handle horizontal insets” is not a universal rule.
- Default internal margins are styling, not proof that system UI is avoided. Account for consumed insets and the actual platform window.
- Loading-to-content and item-count changes can change wrap-content height abruptly. An application wrapper may animate measured content size with a verified Miuix `folmeSpring` spec; avoid competing with enter/exit/IME animation or clipping fixed actions. Mishka's `sheetContentSafePadding` and `sheetHeightTransition` are application helpers, not library functions.

Check open/close, focused field, IME dismissal, freeform/caption bars and resizes on the target platform before claiming this behavior verified.
