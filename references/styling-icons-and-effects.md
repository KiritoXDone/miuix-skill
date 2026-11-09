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

## Pressed, selected, and held state

用于 button / option 按下后突兀缩放、重复变色、高亮粘住或选中状态混乱的问题。先区分状态，再选择反馈；不要给所有可点击组件套同一套自制缩放/变色动画。

| 状态 | 来源与生命周期 | 实现边界 |
|---|---|---|
| 按压 pressed | 手势按下，到 release/cancel 结束 | 由组件交互与 indication 驱动；不在 onClick 中保存一个永久 pressed 标记 |
| 选中 selected / checked | 应用选项值，提交后持续存在 | 传给 Radio/Checkbox/Switch/Dropdown 等组件的受控状态；不是 `holdDownState` |
| 入口保持 held | 该入口的菜单或弹窗仍处于打开/退出过程 | 仅为需要保持反馈的 trigger 使用 `holdDownState`；按选定关闭生命周期释放 |

### Default feedback first

- Miuix v0.9.4 的 `MiuixTheme` 提供 `LocalIndication = MiuixIndication`：用主题色在控件范围绘制动画高亮层，并处理 press、cancel、hover、focus 和 hold-down。它不是 Material 的扩散圆形 ripple；裁剪由外围表面决定。
- `Button` / `TextButton` 默认采用 `LocalIndication.current`，`IconButton` / `BasicComponent` 的点击也消费所在主题的 indication。保留组件 Defaults；主操作只用对应 primary 颜色工厂，不因点击临时改成另一套选中色。
- 原生组件已有点击和反馈时，不再包同一动作的 `clickable` / `toggleable`，不额外画 pressed 遮罩、叠 `pressable` 或通用 `scale` 动画。确需自定义时明确替换哪一层；不要靠全局 `indication = null` 消除所有反馈。
- 检查最近的 `MiuixTheme` / `LocalIndication` provider，尤其混用 Material 的项目。只放 Miuix 组件而继承另一套 indication，仍可能出现不同风格的反馈。
- 自定义行确有必要时，只保留一个同动作交互所有者，使用当前 indication 和正确的 selectable/toggleable 语义；独立组件不要共用一个 InteractionSource 造成联动高亮。原生 Preference 内部的行与开关协作由库负责，调用方不再另包一层。
- 圆角交互区域要包含并裁住完整高亮，使用组件已有表面或 `squircleSurface`；外部 margin 在命中区外，内部 padding 在命中区内。无圆角中段无需额外 clip。
- `PressFeedbackType.Tilt` / `.Sink` 是针对合适表面的显式选择。v0.9.4 `Card` 默认 `None` 且 `showIndication = false`；可点击卡片按需求启用。不要给普通 Button 虚构 `pressFeedbackType` 参数，也不把首页展示卡的 Tilt 推广到所有设置选项。

### Hold only the popup trigger

KernelSU 的简单入口把 `ArrowPreference` / `IconButton` 的 `holdDownState` 直接绑定到自身弹窗 `show`，在关闭请求中置 false。普通导航行、已选选项、确认/取消按钮不因此保持按下。

原生 `OverlayDropdownPreference` 已经管理展开与 held 生命周期：打开时保持，退出完成后释放。优先使用它，不在外部另维护一个 pressed 标记。自定义弹窗若也需要保持到退出结束，才分离 show 与 held，并在 `onDismissFinished` 清理；快速重开导致退出取消时仍以当前打开状态为准。不要用延时任务猜测动画结束，也不要导入内部 `HoldDownObserver`。

单选/多选的持久视觉交给组件 selected/checked 颜色及指示标记；释放手指后只消除瞬时 press 层，不能清空真实选中项。鼠标 hover 或键盘 focus 仍可产生高亮，不应一概当成“卡住”而清除焦点。

### KernelSU evidence and limits

核对的是 `tiann/KernelSU` 的 **Miuix 分支界面**，固定提交 `730f644615969cfd4a84b45d7f62a667b7b95a50`（核对日期 2026-11-09）；其 [版本目录](https://github.com/tiann/KernelSU/blob/730f644615969cfd4a84b45d7f62a667b7b95a50/manager/gradle/libs.versions.toml) 使用 Miuix 0.9.4。

| 已观察到的做法 | 源码 |
|---|---|
| 设置页直接使用 SwitchPreference / OverlayDropdownPreference 的状态与回调 | [SettingsMiuix.kt](https://github.com/tiann/KernelSU/blob/730f644615969cfd4a84b45d7f62a667b7b95a50/manager/app/src/main/java/me/weishu/kernelsu/ui/screen/settings/SettingsMiuix.kt) |
| 编辑入口以 showDialog 驱动 holdDownState；确认 TextButton 使用 primary Defaults | [SuperEditArrow.kt](https://github.com/tiann/KernelSU/blob/730f644615969cfd4a84b45d7f62a667b7b95a50/manager/app/src/main/java/me/weishu/kernelsu/ui/component/miuix/SuperEditArrow.kt) |
| 重启菜单图标以 showTopPopup 驱动 holdDownState；动作项执行时关闭菜单 | [RebootListPopupMiuix.kt](https://github.com/tiann/KernelSU/blob/730f644615969cfd4a84b45d7f62a667b7b95a50/manager/app/src/main/java/me/weishu/kernelsu/ui/component/rebootlistpopup/RebootListPopupMiuix.kt) |
| 首页部分可交互卡片显式使用 Tilt 与 showIndication | [HomeMiuix.kt](https://github.com/tiann/KernelSU/blob/730f644615969cfd4a84b45d7f62a667b7b95a50/manager/app/src/main/java/me/weishu/kernelsu/ui/screen/home/HomeMiuix.kt) |

这些是应用模式，组件契约仍查固定版本的 `MiuixTheme.kt`、`Button.kt`、`IconButton.kt`、`Component.kt`、`Card.kt`、`utils/MiuixIndication.kt` 与 `preference/OverlayDropdownPreference.kt`，路径见 [source-verification](source-verification.md)。KernelSU 同时包含 Material UI；不能把 Material 目录或 `SingleSelectDialog.kt` 的 Material3 实现当成 Miuix 模板。其 [InstallMiuix.kt](https://github.com/tiann/KernelSU/blob/730f644615969cfd4a84b45d7f62a667b7b95a50/manager/app/src/main/java/me/weishu/kernelsu/ui/screen/install/InstallMiuix.kt) 也有外层 toggleable 包 CheckboxPreference 的特例：移植时须核查嵌套交互，不把整个项目的每一种写法都升级为通用规则。未运行这些页面，不从源码推断实际帧感或断言特例存在可复现故障。

### Interaction checks

按下 → 拖出/转为滚动取消、快速点击、点击当前选项、切换其他选项、禁用状态、菜单外点/返回/选项关闭、退出中重开、hover/focus 都要检查。验证同一动作只触发一次、按压层能释放、选择状态正确保留、高亮不越过圆角；覆盖明暗主题。没有交互录屏或设备运行证据时，仅报告 API/状态设计已核对。

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
| Rounded clickable surface | `squircleSurface` followed by the clickable content/modifier | Press highlight escaping a fill-only background |
| Plain rectangular middle segment of a grouped card | Ordinary `background` when clipping is unnecessary | Shader/clipping work with zero corners |
| Existing Miuix Card | Reuse its fill and clip | Re-clipping the same rounded boundary |

At v0.9.4, `Card`'s internal `BasicCard` uses `squircleSurface` and propagates minimum constraints. A watermark/chart layer should not determine content height: use an appropriate `Box` overlay with `matchParentSize()`, let text measure the card, and clip the drawing's own rectangular bounds if it moves beyond them. See [performance and animation](performance-and-animation.md).

### Blur ownership and fallback

Mishka's `BlurredBar` and `rememberBlurBackdrop` coordinate a content capture with transparent bars. They are application helpers. Reuse an existing target-project implementation or build the minimal supported composition from the official blur guide. Each intended page capture must be scoped to its host; do not capture unrelated pages accidentally. Use transparent bar colors only while the effect is active; unsupported/disabled blur needs a readable opaque theme surface. Do not add blur to every page solely because the reference app does.
