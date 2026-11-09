# UI Composition Patterns

Use this reference to compose Miuix components into application roots, navigation shells, grouped content and popup flows. Select patterns by product needs and target architecture. Resolve theme/host ownership with [Project setup and theme](setup-and-theme.md); use the [Code Delivery Contract](../SKILL.md#code-delivery-contract) for the requested implementation scope.

## Contents

- [Evidence Levels](#evidence-levels)
- [Scenario to Integrated Example Map](#scenario-to-integrated-example-map)
- [Derive a Pattern Before Coding](#derive-a-pattern-before-coding)
- [Application Root and Theme](#application-root-and-theme)
- [Page Shell and Adaptive Navigation](#page-shell-and-adaptive-navigation)
- [`miuix-nav` Routing](#miuix-nav-routing)
- [Settings and Grouped Content](#settings-and-grouped-content)
- [State and Component Variants](#state-and-component-variants)
- [Overlay and Window Components](#overlay-and-window-components)

## Evidence Levels

| Evidence | Role | How strongly to follow it |
|---|---|---|
| Component source and defaults | Exact parameters, behavior, host requirements, tokens | Contract; verify before writing uncertain or version-sensitive code |
| Component doc | Intended purpose, supported states, conceptual guidance | Primary explanation; confirm stale or ambiguous details in source |
| `docs/demo/<Component>Demo.kt` | Minimal runnable call shape and basic states | Starting point for a single component, not a complete page template |
| `example/shared/` | Integrated page hierarchy, state flow, grouping, navigation, and adaptive layout | Optional integration evidence; read only when it answers a concrete question |
| Example-only effects and helpers | Showcase visuals and application infrastructure | Optional; do not present them as library requirements |

## Scenario to Integrated Example Map

Use this optional map when the docs and target project leave an integration question unresolved. Unless a path starts with another root, resolve it under `example/shared/src/commonMain/kotlin/`. It identifies first-party integration evidence; it does not replace the component doc, demo, or source contract.

| Scenario | Start with | Extract |
|---|---|---|
| Application theme and global UI state | `App.kt`, `AppState.kt`, `ui/Theme.kt` | Root theme, theme inputs, and stable cross-screen state |
| Adaptive app shell and navigation | `AppContent.kt`, `AppState.kt` | Compact/wide branching, Scaffold placement, NavigationBar/Rail, and shared controls |
| `miuix-nav` routing and transitions | `App.kt`, `AppState.kt`, `AppContent.kt`, `navigation/`, then `docs/guide/miuix-nav.md` | Serializable back stack, entry DSL, transitions, gestures, and their separation from NavigationBar/Rail presentation |
| Home/list page with search and actions | `MainPage.kt`, `component/SuperSearchBar.kt` | App bar, scroll content, search, section composition, and popup actions |
| Settings page | `SettingsPage.kt` | `SmallTitle` + `Card` grouping, Preference rows, dependent visibility, and content padding |
| Dialog flow | `component/DialogSection.kt` | Overlay/Window choice, trigger rows, visibility, dismissal, actions, and exit cleanup |
| Bottom-sheet flow | `component/BottomSheetSection.kt` | Overlay/Window choice, scrolling content, action layout, and inset handling |
| Dropdown or spinner selection | `component/DropdownSection.kt`, `component/SpinnerSection.kt` | Controlled selection, grouped entries, and row integration |
| Form and basic controls | `component/TextFieldSection.kt`, `SwitchSection.kt`, `CheckboxSection.kt`, `SliderSection.kt` | Controlled state, disabled variants, labels, and validation-relevant states |
| Pull-to-refresh page | `PullToRefreshPage.kt` | Refresh state ownership, scroll integration, and content padding |
| Adaptive, inset, scroll, blur, or haptic helpers | `utils/PageUtils.kt`, then its caller | Call-site intent and platform conditions; copy only the behavior the target app needs |

## Derive a Pattern Before Coding

1. Map the required hierarchy, state ownership, navigation and insets in the target project.
2. Choose the public components and check the relevant doc/demo for uncertain contracts.
3. If integration remains unclear, inspect the nearest official example and separate public APIs from application helpers.
4. Adapt the useful pattern to the product; verify uncertain names and parameters against the exact target source.

Do not copy a whole Example file into an application. The Example includes navigation infrastructure, blur helpers, diagnostics, and showcase-only effects that most projects do not need.

## Application Root and Theme

Evidence: `example/shared/src/commonMain/kotlin/App.kt` and `ui/Theme.kt`.

- Apply the ownership decision from [Project setup and theme](setup-and-theme.md): create the root only for a standalone app/example; otherwise integrate below the existing application theme.
- Provide `MiuixTheme` once around the application subtree rather than recreating it in every screen.
- Create `ThemeController` from the application's actual color-mode inputs. Remember it with every input that changes the resulting theme.
- Keep cross-screen theme and UI configuration in stable application state. Use composition locals only when the target architecture already benefits from tree-wide access; they are not required by Miuix.
- Use `MiuixTheme.colorScheme` and `MiuixTheme.textStyles` inside the themed subtree.

## Page Shell and Adaptive Navigation

Evidence: `MainPage.kt`, `SettingsPage.kt`, and `utils/PageUtils.kt`.

- Use `Scaffold` as the page shell when the page has bars, floating controls, snackbars, or Overlay components. Reuse an existing intended host instead of nesting one only for visual structure.
- Account for every edge of the `PaddingValues` supplied by `Scaffold`; pass them to `LazyColumn.contentPadding`, or explicitly assign individual edges to their owners. Follow [page layout and insets](page-layout-and-insets.md) for terminal spacers, Pager bottom bars and cutouts; do not apply the same inset twice.
- Connect a top app bar's scroll behavior to the scrolling content when the page uses collapsing or scroll-aware bars.
- At the stable snapshot, create `val scrollBehavior = MiuixScrollBehavior()`, pass it to `TopAppBar(scrollBehavior = scrollBehavior)`, and apply `Modifier.nestedScroll(scrollBehavior.nestedScrollConnection)` to the scrolling container. Keep the `LazyListState` used by `LazyColumn(state = ...)` separate from the app-bar behavior state. When a `SmallTopAppBar` shares a scroll behavior with a collapsible bar, verify the pinned-state behavior in source rather than adding an external offset workaround.
- If the page owns a `FloatingToolbar`, put it in the existing `Scaffold(floatingToolbar = ..., floatingToolbarPosition = ...)` slot; v0.9.4 fixes the layout so a bottom snackbar stays above the toolbar.
- Keep adaptive width, system insets, scroll haptics, blur, and scrollbar helpers conditional. The Example's `PageUtils.kt` is application infrastructure, not public Miuix API.

### NavigationBar ↔ NavigationRail switching

In v0.9.4, choose the fixed `NavigationRail(expanded = ...)` overload or the expandable `NavigationRail(state = ...)` overload. The stateful overload requires a non-null state; do not pass `state = null`.

Choose breakpoints from the space required by navigation, content panes and accessibility settings. Rail expansion and content maximum width are separate product choices; none requires copying an example app's thresholds.

- Reuse the target project's existing adaptive system. Android-only apps may already use a Window Size Class; multiplatform apps may use `LocalWindowInfo` or another shared policy. Keep the decision in one named function rather than scattering width checks through components.
- Remember or hoist the selected destination/pager/back-stack before branching. Render `NavigationBar` and `NavigationRail` from that same state, so a resize changes chrome without resetting selection or creating two sources of truth.
- Use compact/wide branches for materially different shells. If only the navigation component changes, keep page content and business state outside the branch.
- Validate the chosen policy around its transition widths, including resizable windows, orientation changes and supported foldable/tablet layouts.

## `miuix-nav` Routing

Use `miuix-nav` for route/back-stack ownership, and use `NavigationBar`/`NavigationRail` for top-level navigation chrome. The current minimal route flow is `@Serializable` route hierarchy → `rememberNavBackStack<Route>` → `NavDisplay` entry DSL:

```kotlin
@Serializable
sealed interface Route : NavKey {
    @Serializable data object Home : Route
    @Serializable data class Detail(val id: String) : Route
}

val backStack = rememberNavBackStack<Route>(Route.Home)
NavDisplay(backStack = backStack) {
    entry<Route.Home> { HomePage() }
    entry<Route.Detail> { DetailPage(it.id) }
}
```

Register every concrete route type; the DSL matches exact runtime classes. Use `NavController` when `push`, `pop`, `replace`, or `popUntil` reads more clearly than direct list operations. Built-in transitions are `NavTransitions.MiuixDefault`, `Modal`, and `None`; swipe-to-dismiss is opt-in through `entry(swipeDismiss = ...)`, and physical directions are not automatically mirrored for RTL. Use the complete [miuix-nav routing reference](miuix-nav.md) for saveable identity, `contentKey`, nested displays, multi-pane clipping, and v1 limitations.

For a bottom/rail navigation shell, derive selection from the application's chosen navigation source of truth: pager state, a top-level destination, or an explicit route-to-destination mapping. Keep selection consistent with displayed content and shared across compact/wide shells; do not introduce independent states that can disagree. Do not import the removed `androidx.navigation3` scene flow into v0.9.4 code.

## Settings and Grouped Content

Evidence: `SettingsPage.kt` and the files under `example/shared/src/commonMain/kotlin/component/`.

- Build a settings page from scrollable sections. Use the [Preference family guide](preferences-and-menus.md#choose-the-family) to distinguish a complete setting row from a standalone control or menu.
- Use `SmallTitle` to label a section and `Card` to group related Preference components.
- Put related `SwitchPreference`, `CheckboxPreference`, `ArrowPreference`, dropdown, and slider rows in the same Card rather than styling every row as an unrelated surface.
- Use `summary` for consequences or context, not to repeat the title.
- Show a small dependent setting conditionally with `AnimatedVisibility` when appropriate. For long dynamic groups, use row-level lazy items and the [performance workflow](performance-and-animation.md), while keeping state at the screen owner.
- Hide a dependent setting when it is irrelevant or unsupported; leave it visible but disabled when discoverability matters and the summary can explain the prerequisite. See [Preference enabled and dependent states](preferences-and-menus.md#enabled-and-dependent-states).
- Use the target project's layout tokens for group margins; see [Design language and Defaults](design-language.md) for semantic styling decisions.

For large/dynamic groups, prefer the conditional [segmented-card approach](performance-and-animation.md#lazy-boundaries-and-grouped-cards); it is not needed for this small group.

Illustrative composition under an existing MiuixTheme; the caller supplies its group spacing as `PaddingValues`:

```kotlin
@Composable
fun SettingsScreen(
    groupPadding: PaddingValues,
    enabled: Boolean,
    onEnabledChange: (Boolean) -> Unit,
) {
    Scaffold(
        topBar = { TopAppBar(title = "Settings") },
    ) { innerPadding ->
        LazyColumn(contentPadding = innerPadding) {
            item {
                SmallTitle(text = "General")
                Card(
                    modifier = Modifier.padding(groupPadding),
                ) {
                    SwitchPreference(
                        title = "Feature",
                        summary = "Explain what enabling it changes",
                        checked = enabled,
                        onCheckedChange = onEnabledChange,
                    )
                }
            }
        }
    }
}
```

Use this as a structural pattern. Confirm imports and exact component APIs against the target project and pinned source.

## State and Component Variants

Evidence: `docs/demo/` and the Example component sections.

- Hoist persistent or business state to the owning screen/model. Use `remember` for transient presentation state such as whether a local popup is open.
- Pass the current value and callback to controlled components (`checked` + `onCheckedChange`, `selectedIndex` + callback, `value` + `onValueChange`).
- Cover relevant states demonstrated by the component evidence: normal, selected/checked, summary, disabled, expanded, and dismissal. Do not add showcase states that the product does not need.
- Prefer the component's default colors, margins, shapes, and haptics. Reach for `ComponentDefaults` only when customization has an explicit reason.

## Overlay and Window Components

Evidence: `component/DialogSection.kt`, `component/BottomSheetSection.kt`, and their matching `docs/demo/` files.

- Choose the lifecycle/host family with [Overlay and Window Components](overlays-and-windows.md#choose-the-host-first).
- Use Overlay components for page-scoped UI and place them under the intended `Scaffold` popup host.
- Use Window components when the popup must be independent of that page's Scaffold.
- Model visibility explicitly with `show` and close it through `onDismissRequest`.
- For a dismissible popup, `onDismissRequest` must update the controlled show/active-overlay state. If a product rule conditionally prevents dismissal, implement and test that rule explicitly; the callback does not update the Boolean for you.
- Use `onDismissFinished` only when cleanup must wait for the exit animation, such as resetting a pressed/hold-down state or discarding a draft after the popup is no longer visible. It is not called when a hide animation is cancelled by showing the component again.
- Keep dialog actions balanced and make the primary action visually primary with the provided button defaults when the evidence supports it.

For multiple popup entry points, prefer one page-scoped state such as `ActiveOverlay?` and render each popup from an equality check. The current Overlay host assigns internal z-indices, but that ordering is not a public workflow contract; do not depend on arbitrary simultaneous `show = true` values. An intentional parent → child popup flow should dismiss child first and must be tested for back, focus, scrim, and accessibility behavior.

For mutually exclusive popups, an enum is one compact state representation; reuse the target architecture when it already models this flow. `onDismissFinished` handles only cleanup that must wait for animation completion.

```kotlin
private enum class ActiveOverlay { EditName, DeleteConfirm }

var activeOverlay by remember { mutableStateOf<ActiveOverlay?>(null) }
var nameDraft by remember { mutableStateOf(savedName) }

OverlayDialog(
    show = activeOverlay == ActiveOverlay.EditName,
    onDismissRequest = { activeOverlay = null },
    onDismissFinished = { nameDraft = savedName },
) { /* edit nameDraft; commit only from the confirm action */ }
```

Place this flow below one intended `Scaffold`. Render the second dialog from `activeOverlay == ActiveOverlay.DeleteConfirm` and give it the same dismissal contract. When one popup replaces another without overlap, sequence the replacement through dismissal completion. Deliberately stacked parent/child surfaces instead need an explicit back/focus policy; test that flow rather than forcing every interaction into a replacement sequence.

### Form popup lifecycle

Keep saved/business data outside the popup and follow this compact lifecycle:

1. On open, copy the saved value into a local draft.
2. Edit and validate the draft inside the popup; validation failure keeps it open and preserves input.
3. Confirm commits the draft, then requests dismissal.
4. Cancel, outside click, or system back requests dismissal without committing.
5. Reset or discard the draft in `onDismissFinished` only when cleanup must wait for the exit animation.

All delivered code must satisfy the single authoritative [Code Delivery Contract](../SKILL.md#code-delivery-contract).
