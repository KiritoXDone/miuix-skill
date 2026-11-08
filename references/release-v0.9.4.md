# v0.9.4 Stable Baseline

Verified 2026-11-08: upstream tag `v0.9.4` resolves to `39c40f99844227b853f0049a0933b1f3ae6c00ba`. The [official release](https://github.com/compose-miuix-ui/miuix/releases/tag/v0.9.4) was published on 2026-09-20. This is the maintained baseline, not a claim that every consuming project should upgrade.

The former local skill used `v0.9.4-rc01` / `4a6b750b`. The [rc01-to-stable comparison](https://github.com/compose-miuix-ui/miuix/compare/v0.9.4-rc01...v0.9.4) includes 26 commits. Existing catalog paths were checked against the stable tree; changed navigation, gesture, layout and color contracts were reconciled. Historical rc notes remain in [release-v0.9.4-rc01](release-v0.9.4-rc01.md), and are not the default version source.

## Changes after rc01 that affect generated code

| Area | Stable behavior / migration decision | Evidence relative to the pinned repo |
|---|---|---|
| Navigation item colors | `NavigationBarItem` and `FloatingNavigationBarItem` accept `colors`; use `NavigationBarDefaults.navigationBarItemColors(unselectedContentColor, selectedContentColor)` and preserve state alpha | `docs/components/navigationbar.md`, `miuix-ui/.../basic/NavigationBar.kt` |
| NavigationRail | Fixed overload takes `expanded: Boolean = false`; stateful overload requires non-null `state`. Do not carry forward `state = null` | `docs/components/navigationrail.md`, `miuix-ui/.../basic/NavigationRail.kt` |
| Pager gestures | `pagerGestureOverride`, `PagerInterceptionMode`, `PagerGestureNestedScrollConnection`, `springAnimateToPage`; interceptor requires matched pager settings | `docs/guide/utils.md`, `miuix-ui/.../utils/PagerGestureUtils.kt` |
| TabRow | Avoids horizontal nested-scroll interference; derive selected tab from pager state instead of bidirectional effects | `docs/components/tabrow.md`, `miuix-ui/.../basic/TabRow.kt` |
| Navigation effects | Entry content can access `LocalNavTransitionScope.current`; `isRunning` is coarse lifecycle state, continuous values belong in deferred graphics/layout reads | `docs/guide/miuix-nav.md`, `miuix-nav/.../core/LocalNavTransitionScope.kt` |
| Entry ViewModels | SavedState CreationExtras are forwarded for entry-scoped `SavedStateHandle`/ViewModels | `miuix-nav/.../state/NavEntryViewModel.kt` |
| Overscroll | Gesture coordination changes include custom mouse drag handling; do not reuse rc assumptions that all mouse input is excluded | `miuix-ui/.../utils/Overscroll.kt`, `OverscrollFactory.kt` |

## Validation when adopting in an app

Resolve actual dependencies first, then compile the affected source set and inspect navigation/pager, back gestures, saved state, insets and popup behavior on supported targets. This skill repository has no runnable application: source review and skill validation are not a claim of device or app build verification.

For artifacts and theme setup use [setup-and-theme](setup-and-theme.md); for path expansion use [source-verification](source-verification.md).
