# Design Language and Defaults

Use this reference when creating, restyling, or reviewing a Miuix interface. Resolve root/host ownership in [Project setup and theme](setup-and-theme.md), then use [UI composition patterns](usage-patterns.md) for composition. The token and Defaults names below are verified at the `0.9.4` stable snapshot. Composition choices depend on the product hierarchy and the selected component contracts; example applications supply optional evidence.

Contents: [decision order](#decision-order) · [surface and color](#surface-and-color-roles) · [typography](#typography-roles) · [component Defaults](#component-defaults) · [hierarchy](#hierarchy-and-grouping) · [errors and loading](#error-and-failure-states) · [state and async](#state-and-async-ownership) · [customization](#when-customization-is-justified)

## Decision Order

Make each visual decision in this order:

1. Preserve an intentional convention already used by the target project.
2. Use the public Miuix component that owns the behavior and its default parameters.
3. If customization is necessary, use that component's `*Defaults` object and `MiuixTheme` semantic tokens.
4. Use Compose layout primitives such as `Row`, `Column`, `Box`, padding, and size constraints to arrange Miuix components.
5. Add custom colors, shapes, drawing, or effects only for a concrete product requirement that the public component and defaults cannot express.

Do not introduce a Material or Material 3 component merely because its API is familiar. First verify that Miuix does not provide the intended component or interaction.

## Surface and Color Roles

Prefer the component's default colors. When code must choose a token directly, select it by semantic role rather than by its current light-theme appearance.

For a natural-language request such as “蓝色主按钮” or “灰色摘要文字”, use [Color semantics and visual lookup](color-lookup.md). It verifies v0.9.4 `Colors` source and component Defaults, and distinguishes a semantic role from a fixed default-theme hue.

| UI role | Start with | Notes |
|---|---|---|
| Page canvas | `background` + `onBackground` | Use for the root page and content placed directly on it |
| General elevated or chrome surface | `surface` + `onSurface` | Follow the selected component default and the intended surface role |
| Grouped content container | `CardDefaults.defaultColors()` | At the stable snapshot, Card already uses `surfaceContainer` + `onSurfaceContainer` |
| Primary action or selected control | The component's primary/selected Defaults | For example, use `ButtonDefaults.buttonColorsPrimary()` instead of recreating primary and disabled colors |
| Preference summary | `BasicComponentDefaults.summaryColor()` | Its default uses `onSurfaceVariantSummary` and handles disabled state |
| Row action or trailing affordance | Component default or `onSurfaceVariantActions` | Use the latter only where an action-role color is required explicitly |
| Divider or boundary | `dividerLine` or `outline` | Use `Divider` defaults for dividers; use `outline` for a true outline role |
| Dialog or popup scrim | Component default | The window and overlay implementations use the semantic `windowDimming` role |
| Disabled content | The component's disabled state/defaults | Do not simulate disabled UI with arbitrary alpha; Miuix has role-specific disabled tokens |

Do not pair an `on*` color with an unrelated container. Avoid copying literal colors from screenshots or examples: theme modes and dynamic color can change their rendered values.

Application status roles such as warning/healthy/latency belong in a centralized app-owned palette with light/dark handling. Keep additional roles application-owned and distinct from Miuix tokens. Avoid scattered literal colors or inventing library roles.

## Typography Roles

Let Miuix components choose typography unless the product requires custom text. For explicit `Text`, choose from `MiuixTheme.textStyles` by the target project's content hierarchy and readability requirements.

| Content | Preferred starting point |
|---|---|
| Page or prominent heading | The appropriate `title1`–`title4` scale used by the nearest page pattern |
| Section label | `SmallTitle`; its default style is `subtitle` |
| Preference or component title | Keep `BasicComponent`/Preference defaults; the title uses the component's headline role |
| Preference summary | Keep the component default; it uses the supporting body role |
| Long-form or ordinary text | Select among `main`, `paragraph`, `body1`, and `body2` by context |
| Compact supporting text | `footnote1` or `footnote2` when the nearby design uses a footnote role |
| Button label | Keep the Button default or use `button` when building a compatible custom label |

These names are a type scale, not a rigid HTML-like heading hierarchy. Do not choose a style from its numeric size alone, and do not hardcode font size or weight when a semantic style already fits.

## Component Defaults

Search the selected component source for its `*Defaults` object before adding a magic number. Common public defaults include `BasicComponentDefaults`, `CardDefaults`, `SmallTitleDefaults`, `ButtonDefaults`, `TopAppBarDefaults`, `DialogDefaults`, `BottomSheetDefaults`, `NavigationBarDefaults`, `TextFieldDefaults`, `SliderDefaults`, `SwitchDefaults`, `CheckboxDefaults`, `RadioButtonDefaults`, `RadioButtonPreferenceDefaults`, `BadgeDefaults`, `SnackbarDefaults`, `TabRowDefaults`, `ProgressIndicatorDefaults`, `ScrollBarDefaults`, `TooltipDefaults`, and `BreadcrumbBarDefaults`; use [Color semantics and visual lookup](color-lookup.md) for their verified color mappings.

- Omit optional visual parameters when the stock result is acceptable.
- Call the component's color factory when changing one supported role, instead of recreating all state colors manually.
- Reuse exported dimensions and margins when they express the same component contract.
- Treat spacing found only in `example/shared/` as composition evidence, not as a library constant.
- Recheck source when a Defaults member is not shown in the component doc; do not infer its name from another component.

## Hierarchy and Grouping

- Give every page one clear structural shell. Avoid nested Scaffolds or nested Cards unless they represent distinct behavior or containment.
- Group related settings under `SmallTitle` + `Card`; keep unrelated actions separate. For large dynamic groups, preserve the visual grouping with [independent lazy rows](performance-and-animation.md), rather than composing the whole group at once.
- In the pinned source, Card is a plain `Column` with `CardDefaults.InsideMargin = 0.dp`; it does not insert gaps or Dividers between children. Preference rows provide their own internal padding and disabled styling. Do not add row spacing or separators by habit—add a Divider only when the grouping remains ambiguous.
- Place `SmallTitle` before its Card for a section label. Its own Defaults provide label padding; it is not a row inside the Card and there is no public `SectionHeader` component at this tag.
- Use project layout tokens for page/group spacing. When none exist, choose a coherent scale from content density, viewport and accessibility requirements, then validate it across relevant layouts.
- Avoid nested Cards for visual decoration. Nest only when the inner Card has a distinct containment or interaction role that remains understandable in accessibility traversal.
- Use the title for the setting or action and the summary for consequence, status, or context. Do not repeat the same wording in both.
- Keep the primary action visually dominant and secondary/destructive actions distinguishable through supported component roles, placement, and wording.
- Prefer whitespace and grouping over extra borders. Add a Divider only when separation is still ambiguous.
- Preserve state ownership and interaction semantics while changing appearance; visual cleanup must not silently change behavior.

## Error and Failure States

- `TextField` at the stable snapshot has no `isError` or `supportingText` parameter. Track validation state outside it, use `TextFieldDefaults.textFieldColors(labelColor = error, borderColor = error)` with `MiuixTheme.colorScheme.error`, and place a concise supporting `Text` below the field. Do not communicate the error by color alone.
- Preference components do not expose one uniform error flag. For a row-level validation problem, keep the row visible, explain the problem in its summary or nearby inline text, and use an exposed `summaryColor`/`BasicComponentDefaults` error role only after verifying that row's signature.
- Use `Snackbar` for transient operation/network feedback when the current content remains usable. Use an inline page/section error with a retry action for persistent or blocking failures; preserve entered form data.
- Validate error, disabled, loading, empty, and retry states independently in light/dark and dynamic color modes when affected.

### Loading and async states

- For initial page loading, use `ProgressIndicator` or a meaningful placeholder and keep the surrounding hierarchy stable.
- For list refresh, use `PullToRefresh` with a hoisted `isRefreshing` state and an explicit refresh callback; preserve existing content while the refresh runs.
- For a single action, show local progress and prevent duplicate submission when appropriate; use `Snackbar` for the eventual success/failure result rather than as the only loading indicator.

### State and async ownership

- Keep persistent or business state in the screen owner/ViewModel; use `remember` for transient presentation state such as a popup or pressed state.
- Keep a form draft in `rememberSaveable` where the target supports it, or in the screen owner when restoration/serialization requires more control.
- Model loading, empty, error, success, disabled, and retry states explicitly rather than deriving them from one loosely related Boolean.
- Emit one-shot navigation or Snackbar effects from an explicit event/effect path, launched once per event rather than directly during composition.
- Cancel obsolete work, prevent duplicate submission where appropriate, and preserve user input when an async operation fails.

## When Customization Is Justified

Customize beyond defaults only when at least one of these is true:

- The user supplied a concrete visual target, brand rule, or screenshot that requires it.
- The target project already has an intentional reusable token or component convention.
- The public Miuix component cannot represent the required hierarchy, state, or platform constraint.
- A visual defect remains after the correct component, theme, host, and defaults are in place.

Keep the smallest coherent customization, document any non-obvious reason in code, and verify light/dark or dynamic color behavior when affected.

Before delivery, use the single authoritative [Code Delivery Contract](../SKILL.md#code-delivery-contract); for an existing screen review, use [UI review and improvement workflow](ui-review-workflow.md).
