# Styling, Icons, and Effects

Use this reference for styling resources that have dependency or platform consequences. Read only the relevant guide and exact source files from the `0.9.4` stable snapshot at commit `39c40f99` through [Source verification](source-verification.md). For semantic colors, typography, grouping, and customization boundaries, continue with [Design language and Defaults](design-language.md).

## Version-Pinned Guides

| Topic | Tagged Markdown guide | Key source files |
|-------|-----------------------|------------------|
| Icons | `docs/guide/icons.md` | `miuix-core/.../icon/MiuixIcons.kt`, `miuix-ui/.../icon/basic/`, `miuix-icons/.../extended/` |
| Colors | `docs/guide/colors.md` | `miuix-ui/.../theme/Colors.kt` |
| Theme + ThemeController | `docs/guide/theme.md` | `miuix-ui/.../theme/MiuixTheme.kt`, `miuix-ui/.../theme/ThemeController.kt` |
| Text styles | `docs/guide/textstyles.md` | `miuix-ui/.../theme/TextStyles.kt` |
| Utilities | `docs/guide/utils.md` | `miuix-ui/.../utils/` |
| Blur | `docs/guide/blur.md` | `miuix-blur/src/commonMain/kotlin/` |
| Squircle | `docs/guide/squircle.md` | `miuix-squircle/src/commonMain/kotlin/` |
| Multiplatform behavior | `docs/guide/multiplatform.md` | Platform source sets named by the selected API |

Do not use the unversioned rendered docs site or generated online Dokka to infer a version-pinned signature. Read the pinned Kotlin source when a guide does not show the exact contract.

## Icons

- `miuix-ui` includes the seven basic Regular-weight icons: `ArrowRight`, `ArrowUpDown`, `Check`, `Close`, `Search`, `SearchCleanup`, and `Sidebar`.
- Extended icons require an explicit `miuix-icons` dependency: `top.yukonga.miuix.kmp:miuix-icons:0.9.4` for commonMain or `top.yukonga.miuix.kmp:miuix-icons-android:0.9.4` for Android-only projects.
- Verify every requested name in `docs/guide/icons.md` and its source file. Use `MiuixIcons.Name` for Regular; use `MiuixIcons.Light.Name`, `MiuixIcons.Normal.Name`, `MiuixIcons.Medium.Name`, or `MiuixIcons.Demibold.Name` only when that weight exists in tagged source.
- Prefer an existing semantic icon. Do not invent a Material icon name or add a custom vector before checking the basic and extended sets.

### Icon placement decision

| Placement | Use | Rule |
|---|---|---|
| Compact independent action, toolbar action, or clickable glyph | `IconButton { Icon(...) }` | Do not put `clickable` directly on `Icon`; `IconButton` owns button semantics and a default 40 dp minimum target |
| Leading decoration in a setting/content row | The row's `startAction` or other dedicated leading slot | `BasicComponent` has `startAction`, not a generic `icon` parameter; let the row own alignment and disabled behavior where supported |
| TextField decoration or navigation item | The component's `leadingIcon`/`trailingIcon`/`icon` slot | Prefer the semantic slot over rebuilding the component with a custom `Row` |
| Inline icon + text with no owning slot | Compose `Row(verticalAlignment = Alignment.CenterVertically)` | Add explicit spacing, keep both under the surrounding content color, and preserve text wrapping |

`Icon` uses the vector/painter's intrinsic size and falls back to 24 dp when none exists; `IconButton` supplies the touch target, not the glyph size. Use a localized `contentDescription` for a meaningful image/action and `null` only when the icon is purely decorative or its adjacent text already provides the semantics.

## Blur

- Add `miuix-blur` explicitly; it is not included by `miuix-ui`.
- On Android, blur requires API 33. Gate shader-backed behavior with the public support check shown by the tagged guide and Example, and preserve a readable non-blurred surface when unsupported.
- Treat Example blur backdrops and bar helpers as application-specific composition. Copy only the effect and fallback the product actually needs.

### Progressive blur

Version 0.9.4 adds `ProgressiveBlur` and `Modifier.progressiveTextureBlur`. Use `ProgressiveBlur.Top`, `.Bottom`, `.Left`, or `.Right` for edge fades, or copy a preset and tune `startFraction`, `endFraction`, and positive `curve`. `colors` and `noiseCoefficient` fade with the blur; progressive noise defaults to `0f`.

Use progressive blur for navigation bars and narrow edge bands. Its pixel-sharp clear end adds a full-resolution overlay pass, so it is more expensive than a uniform `textureBlur` over the same area. In a custom `drawBackdrop` pipeline, call `progressiveBlur(...)` or `progressiveTextureBlurEffect(...)` in the effect block and pass the same `ProgressiveBlur` as `drawBackdrop(progressiveGradient = ...)`; the convenience modifier wires both sides together. Keep the progressive noise default (`BlurDefaults.ProgressiveNoiseCoefficient = 0f`) unless a grain effect is deliberate.

## Squircle

- `miuix-squircle` is already brought in by `miuix-ui`; add it explicitly only when using it without `miuix-ui`.
- Use the public modifiers `Modifier.squircleBackground`, `squircleClip`, `squircleSurface`, and `squircleBorder`. Do not invent a `SquircleShape` API.
- Shader-backed modifiers fall back to matching rounded-corner rendering below Android API 33. Keep the same corner parameters so the fallback preserves geometry.
- Prefer component Defaults before applying a custom squircle modifier. Add custom surface, clipping, or border behavior only for an explicit visual requirement.

### Choose the necessary rendering work

| Requirement | Modifier / ownership | Avoid |
|---|---|---|
| Non-interactive solid fill, no child clipping needed | `squircleBackground` | An extra clip/offscreen layer merely for the same outline |
| Image or content must remain inside continuous corners | `squircleClip` | A background-only modifier that lets children escape |
| Rounded clickable surface | `squircleSurface` followed by the clickable content/modifier | Square ripple escaping a fill-only background |
| Plain rectangular middle segment of a grouped card | Ordinary `background` when clipping is unnecessary | Shader/clipping work with zero corners |
| Existing Miuix Card | Reuse its fill and clip | Re-clipping the same rounded boundary |

At v0.9.4, `Card`'s internal `BasicCard` uses `squircleSurface` and propagates minimum constraints. A watermark/chart layer should not determine content height: use an appropriate `Box` overlay with `matchParentSize()`, let text measure the card, and clip the drawing's own rectangular bounds if it moves beyond them. See [performance and animation](performance-and-animation.md).

### Blur ownership and fallback

Mishka's `BlurredBar` and `rememberBlurBackdrop` coordinate a content capture with transparent bars. They are application helpers. Reuse an existing target-project implementation or build the minimal supported composition from the official blur guide. Each intended page capture must be scoped to its host; do not capture unrelated pages accidentally. Use transparent bar colors only while the effect is active; unsupported/disabled blur needs a readable opaque theme surface. Do not add blur to every page solely because the reference app does.
