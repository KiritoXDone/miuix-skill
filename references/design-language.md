# Design Language and Defaults

Use this reference when creating, restyling, or reviewing a Miuix interface. Resolve root/host ownership in [Project setup and theme](setup-and-theme.md), then use [UI composition patterns](usage-patterns.md) for composition. The token and Defaults names below are verified at the `0.9.4` stable snapshot. Composition choices depend on the product hierarchy and the selected component contracts; example applications supply optional evidence.

Contents: [decision order](#decision-order) · [surface and color](#surface-and-color-roles) · [typography](#typography-roles) · [component Defaults](#component-defaults) · [hierarchy](#hierarchy-and-grouping) · [status cards](#status-overview-cards) · [errors and loading](#error-and-failure-states) · [state and async](#state-and-async-ownership) · [customization](#when-customization-is-justified)

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

## Status overview cards

这是可选的状态概览模式：当用户要求“KSU 经典绿色大对号卡片”、绿/黄/红状态卡，或产品需要突出展示总体运行状态时采用。使用 Miuix `Card` 组合内容，不引入虚构的库级 `StatusCard`。普通设置分组无需改成这种大图标样式。

### Shared visual structure

- 一张完整的圆角状态表面：左上放状态标题与简短说明，左下可放版本、模式、更新时间等元信息；右下放超大的状态图标作为视觉锚点，允许图标的一部分越出内容区并被 Card 外轮廓裁掉。
- 背景图标层用 `Box` 叠放，以 `matchParentSize()` 等方式跟随已测量的内容区域，不参与决定卡片高度。正文负责测量；按图标占用预留文字空间或限制装饰图标区域，长文本、大字体时增加高度/调整排布，避免遮字。
- 圆角、填色和点击反馈统一由 Card 管理；复用 `CardDefaults`，用 `CardDefaults.defaultColors(color = ..., contentColor = ...)` 配对容器与文字。装饰图标 `contentDescription = null`，状态信息由标题/语义完整表达。
- 有动作时用 Card 的 `onClick` / `onLongPress` 和 `showIndication`；`PressFeedbackType.Tilt` 是贴近该视觉参考的可选反馈。无动作时使用 `onClick = null` / `onLongPress = null` 并关闭反馈，不塞空 lambda 来伪装可点击。三种状态使用一致的交互策略，颜色本身不决定 Tilt/Sink。

经典绿色参考的确切出处是 [KernelSU HomeMiuix.kt / StatusCard](https://github.com/tiann/KernelSU/blob/730f644615969cfd4a84b45d7f62a667b7b95a50/manager/app/src/main/java/me/weishu/kernelsu/ui/screen/home/HomeMiuix.kt)。该实现的大图标为 `Icons.Rounded.CheckCircleOutline`（import 来自 `androidx.compose.material.icons.rounded`），不是 `MiuixIcons.CheckCircleOutline`。精确复刻时确认图标资产及依赖；通用实现可接收调用方提供的 `ImageVector`，不得猜测 Miuix 图标名称。

该参考使用 110 dp 图标、右下对齐后偏移 27/31 dp，标题/说明/元信息分别约 22/15/16 sp。这些数值只用于复刻此可选视觉配方；其他尺寸按容器、字体缩放和产品 token 调整。关键特征是大圆圈对号、状态底色、左侧文字层级与右下局部裁切，不要求照搬其业务字段或布局实现。

### Green, amber, and red variants

三个变体共享布局，仅由明确状态改变文案、图标和色组。加载/未知/尚未检测要使用中性或进度表现，不能默认显示成功对号。

| 变体 | 适用语义 | 图标与文案方向 | 浅色容器 / 深色容器 | 参考强调色 |
|---|---|---|---|---|
| 绿色 | 已确认正常、成功、健康或运行中 | 大圆圈对号 + 明确的正常状态文字 | `#DFFAE4` / `#1A3825` | `#36D167` |
| 黄色/琥珀色 | 需要注意、受限、降级或有待处理事项 | 警告/感叹号图标 + 原因和可采取的动作 | `#FFF0DB` / `#3E2F1B` | `#F5A623` |
| 红色 | 错误、失败、不可用或需要立即处理 | 错误/叉号图标 + 故障说明及恢复入口 | `#F8E2E2` / `#310808` | `#F72727` |

绿色数值来自上面的状态卡；黄/红数值来自 [KernelSU WarningCard.kt](https://github.com/tiann/KernelSU/blob/730f644615969cfd4a84b45d7f62a667b7b95a50/manager/app/src/main/java/me/weishu/kernelsu/ui/component/miuix/WarningCard.kt) 的 Notice/Error 配色。**黄/红采用同一大图标概览布局，是本 skill 的可选扩展**；上游 WarningCard 本身是紧凑提示行，不能声称三张大图标卡都来自上游现成组件。

- 将每个状态的 container、on-container、accent 集中定义在应用语义配色中；表中强调色可用于大图标，不直接当作小号正文色。为文字另选可读的前景色并验证明暗主题对比度，尤其浅黄底与黄色文字的组合。
- 明确要求“经典绿/黄/红”时，默认保留三种语义色相。若产品明确选择动态主题，可接入主题配色，但不能承诺动态色仍是绿/黄/红。参考实现的动态分支中，绿色卡使用 secondaryContainer/primary，Notice 使用 tertiaryContainer/onTertiaryContainer，Error 使用 errorContainer/onErrorContainer。
- Miuix 有 `error` / `errorContainer`，没有现成的 success/warning 色系；不能编造 `MiuixTheme.colorScheme.successContainer` 或 `warningContainer`。本配方的固定三色是应用自有样式，不修改其他控件的全局 primary。
- 让状态、标题、说明、元信息、图标、配色及可选动作由调用方提供。状态切换保留布局稳定，并用文字与图标共同表达；不要仅换背景色或在黄/红状态继续显示成功对号。

按三态分别检查明暗主题、长文本/大字体、宽窄容器、图标边缘裁切与按压反馈；RTL 对齐及偏移应遵循布局方向；切换加载/未知时检查是否短暂误报正常。源码配色与布局核验不等于实际设备视觉验证。

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
