# Source Verification

Use this reference after another catalog or workflow identifies an evidence path. It defines how every abbreviated path in this Skill maps to the public Miuix repository and pins the current catalog evidence to the `0.9.4` stable snapshot.

Contents: [evidence workflow](#evidence-resolution-workflow) · [snapshot scope](#snapshot-scope) · [path convention](#path-convention) · [supporting internals](#supporting-internals) · [Example app](#example-app)

## Evidence Resolution Workflow

1. Start from the component catalog, [usage-pattern evidence levels](usage-patterns.md#evidence-levels), or the exact file named by another reference.
2. Expand abbreviated paths with the table below.
3. Read the relevant `docs/**/*.md` file for intent, a matching `docs/demo/` file when an isolated call is unclear, and source for uncertain API/behavior contracts. Use the closest `example/shared/` section only if a concrete integration question remains. Stop when the task has the evidence it needs.
4. For current catalog work, keep all evidence on commit `39c40f99` / tag `v0.9.4`. The stable tag was verified on 2026-11-08. Do not use the rolling rendered documentation site or generated Dokka pages for version-sensitive parameters. If the target project uses another version, state that mismatch and verify that version separately.
5. Return to the [Code Delivery Contract](../SKILL.md#code-delivery-contract) before presenting implementation or validation claims.

The [official documentation site](https://compose-miuix-ui.github.io/miuix/) is useful for concepts and discovery; verify version-sensitive claims in tagged Markdown/source. A local checkout or cache of the same tag/commit is equally valid evidence. If no matching evidence is available, mark the specific claim unverified and continue independent work; do not invent APIs or block the whole task unnecessarily.

## Snapshot scope

| Evidence role | Pinned snapshot |
|---|---|
| Maintained stable catalog | `v0.9.4` at commit `39c40f99844227b853f0049a0933b1f3ae6c00ba` |
| Other versions | Resolve the target project's exact tag or commit before making version-sensitive claims |

Maintain one baseline in this guide; keep component behavior in its topic reference rather than separate release-history files. For a project on another version, inspect that version on demand. This table does not replace checking the project's actual dependency.

## Path Convention

All file paths in the tables below are relative to the Miuix repository root. Resolve them using GitHub URLs:

| Source | URL pattern |
|--------|------------|
| Read a version-pinned Markdown doc, demo, or source file | `https://raw.githubusercontent.com/compose-miuix-ui/miuix/39c40f99844227b853f0049a0933b1f3ae6c00ba/<file-path>` |
| Browse a **directory** | `https://github.com/compose-miuix-ui/miuix/tree/39c40f99844227b853f0049a0933b1f3ae6c00ba/<dir-path>` |

The repository Markdown file is the documentation evidence. The rendered site may represent a later commit and must not replace the pinned `docs/**/*.md` file during a version-pinned task. For another target version, substitute its verified tag or commit explicitly.

| Abbreviation | Relative path |
|---|---|
| `miuix-ui/.../basic/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/` |
| `miuix-ui/.../overlay/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/overlay/` |
| `miuix-ui/.../window/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/window/` |
| `miuix-ui/.../layout/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/layout/` |
| `miuix-ui/.../theme/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/theme/` |
| `miuix-ui/.../utils/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/utils/` |
| `miuix-blur/.../blur/` | `miuix-blur/src/commonMain/kotlin/top/yukonga/miuix/kmp/blur/` |
| `miuix-ui/.../icon/basic/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/icon/basic/` |
| `miuix-preference/.../preference/` | `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/preference/` |
| `miuix-preference/.../menu/` | `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/menu/` |
| `miuix-preference/.../popup/` | `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/popup/` |
| `miuix-core/.../icon/` | `miuix-core/src/commonMain/kotlin/top/yukonga/miuix/kmp/icon/` |
| `miuix-icons/.../extended/` | `miuix-icons/src/commonMain/kotlin/top/yukonga/miuix/kmp/icon/extended/` |
| `miuix-shader/.../shader/` | `miuix-shader/src/commonMain/kotlin/top/yukonga/miuix/kmp/shader/` |
| `miuix-squircle/.../squircle/` | `miuix-squircle/src/commonMain/kotlin/top/yukonga/miuix/kmp/squircle/` |
| `miuix-nav/.../core/` | `miuix-nav/src/commonMain/kotlin/top/yukonga/miuix/kmp/nav/core/` |
| `miuix-nav/.../gesture/` | `miuix-nav/src/commonMain/kotlin/top/yukonga/miuix/kmp/nav/gesture/` |
| `miuix-nav/.../runtime/` | `miuix-nav/src/commonMain/kotlin/top/yukonga/miuix/kmp/nav/runtime/` |
| `miuix-nav/.../state/` | `miuix-nav/src/commonMain/kotlin/top/yukonga/miuix/kmp/nav/state/` |
| `miuix-nav/.../transition/` | `miuix-nav/src/commonMain/kotlin/top/yukonga/miuix/kmp/nav/transition/` |
| `docs/components/` | `docs/components/` |
| `docs/demo/` | `docs/demo/src/commonMain/kotlin/` |
| `example/shared/.../` | `example/shared/src/commonMain/kotlin/` |
| `example/shared/.../navigation/` | `example/shared/src/commonMain/kotlin/navigation/` |

## Supporting Internals

These files underpin multiple components. They're read-on-demand, not listed per component above:

| File | Role |
|------|------|
| `miuix-ui/.../basic/Component.kt` | `BasicComponent` — universal base for all preference and overlay components |
| `miuix-ui/.../utils/MiuixPopupUtils.kt` | Popup position/anchor utilities for all overlay and window popups |
| `miuix-ui/.../utils/WindowNavigationEventScope.kt` | Rebinds predictive/system back handling to a focused platform window |
| `miuix-ui/.../layout/DialogContentLayout.kt` | Shared dialog content layout, including `maxWidth`, large-screen mode, and corner-radius resolution |
| `miuix-ui/.../layout/BottomSheetContentLayout.kt` | Shared bottom sheet content layout |
| `miuix-ui/.../layout/ListPopupLayout.kt` | Shared list popup content layout |
| `miuix-ui/.../layout/CascadingListPopupLayout.kt` | Shared cascading list popup layout |
| `miuix-ui/.../layout/CascadingMorphContent.kt` | Shared cascading morph content |
| `miuix-ui/.../layout/MorphHeaderRow.kt` | Shared morph header row |
| `miuix-preference/.../popup/DropdownEntriesContent.kt` | Internal dropdown entries content |
| `miuix-ui/.../basic/Dropdown.kt` | `DropdownImpl` and `SpinnerItemImpl` — internal dropdown/spinner building blocks used by preference selectors |
| `miuix-ui/.../basic/ListPopup.kt` | `ListPopupContent`, `ListPopupColumn`, `rememberListPopupLayoutInfo` — internal list popup building blocks |
| `miuix-ui/.../basic/PullToRefresh.kt` | `PullToRefreshState` threshold/progress state machine and real-gesture gating |
| `miuix-ui/.../basic/TopAppBar.kt` | `SmallTopAppBar` pinned-state handling and `TopAppBarState.Saver` |
| `miuix-blur/.../TextureEffect.kt` | Uniform and progressive texture-blur modifier overloads |
| `miuix-blur/.../ProgressiveBlur.kt` | `ProgressiveBlur` descriptor and edge-fade presets |
| `miuix-blur/.../BackdropEffects.kt` | Custom backdrop effect-block APIs, including progressive blur |
| `miuix-ui/.../utils/MiuixIndication.kt` | Theme-provided press/hold/hover/focus highlight rendering |
| `miuix-ui/.../utils/PressFeedback.kt` | Explicit Sink/Tilt effects; verify component support before applying |
| `miuix-ui/.../utils/Overscroll.kt` | Modifier overscroll API, real-gesture gating, and stale-offset recovery |
| `miuix-ui/.../utils/OverscrollFactory.kt` | Theme-level `MiuixOverscrollFactory` / `MiuixOverscrollEffect` behavior |

## Example App

`example/` is a full Compose Multiplatform app built with Miuix. Use it to study integrated composition after selecting components; use `docs/demo/` for isolated component calls.

| Path | What's inside |
|------|---------------|
| `docs/demo/src/commonMain/kotlin/<Component>Demo.kt` | Minimal, isolated usage for one component |
| `example/shared/.../App.kt`, `AppState.kt`, `ui/Theme.kt` | Application theme, theme inputs, and cross-tree state setup |
| `example/shared/.../AppContent.kt` | Adaptive app shell, compact/wide navigation, and shared controls |
| `example/shared/.../MainPage.kt` | Integrated home page with Scaffold, app bar actions, search, scrolling sections, and popup menus |
| `example/shared/.../SettingsPage.kt` | Integrated settings page with titled Cards, Preference groups, dependent settings, and adaptive padding |
| `example/shared/.../component/` | `LazyListScope` sections and popup examples composed into `MainPage`; these are sections, not one screen per component |
| `example/shared/.../utils/PageUtils.kt` | Example-specific adaptive app bar, insets, scroll, haptic, and blur helpers |

Read [UI composition patterns](usage-patterns.md) before translating these files into application code. Reuse repeated hierarchy and state patterns, but verify APIs in source and do not treat Example-specific helpers as public Miuix requirements.

## Application practice evidence

Application examples can reveal useful composition tradeoffs, but they do not define Miuix APIs, mandatory wrappers, dimensions, breakpoints or business behavior. For public contracts, use the selected library version's docs/source; for product choices, use the user's goals and the target project's conventions. Consult an application example only when it resolves a concrete integration question.

Supplementary sources consulted while developing this skill:

| Source | Evidence contribution |
|---|---|
| [Mishka UI guide](https://github.com/YuKongA/Mishka/blob/91e17c02d6eadbca6752ed30014660e378dc9c33/docs/ui-guidelines.md) | Insets ownership, adaptive layout, lazy grouping and animation-state observations |
| [KernelSU Miuix UI](https://github.com/tiann/KernelSU/tree/730f644615969cfd4a84b45d7f62a667b7b95a50/manager/app/src/main/java/me/weishu/kernelsu/ui) | Native component feedback, controlled held-state and nested interaction examples |
| [Original skill](https://github.com/limczhh/miuix-skill) | Component catalog and version-pinned reference organization inherited from the original local installation |

The application insights are expressed as conditional, reusable guidance in the topic references. No application helper implementation is bundled. Preserve upstream attribution and check the relevant license if copying source code; the source citations here do not change upstream licensing.

## Community usage evidence

核对日期：2026-11-10。发现入口为 [awesome-miuix 固定清单](https://github.com/YuKongA/awesome-miuix/blob/823f1e9e98b4da2c8f08a5d54894d8279702568f/README.md)。本轮按表单、任务流程、颜色编辑与跨平台场景选取下列项目；不是逐仓库审计，也不把被收录等同于最佳实践。已有的状态卡、反馈和安全区来源沿用上方固定引用。

### Dependency compatibility

| 项目 / 固定提交 | 实际依赖证据 | 使用边界 |
|---|---|---|
| Updater-KMP / `18207977` | [0.9.4 版本目录](https://github.com/YuKongA/Updater-KMP/blob/18207977bb0e3a558e42e80d878ad4790c6b0e28/gradle/libs.versions.toml) | 公共组件调用可与当前基线对照；自定义输入/定位/结果布局仍属应用代码 |
| InstallerX Revived / `2bdd19dc` | [0.9.4-ea2dbb3a-SNAPSHOT](https://github.com/wxxsfxyzm/InstallerX-Revived/blob/2bdd19dc552a3742b7b563dd9fa9a74ce56bb644/gradle/libs.versions.toml) | 快照版，部分 UI 混用 Material；只复用任务阶段/操作布局，签名回到目标版本核对 |
| XiaomiHelper / `a2eeb724` | [app 依赖 hyperx-compose](https://github.com/HowieHChen/XiaomiHelper/blob/a2eeb724b4d2427825b09d2d75f4fe3b3782500f/app/build.gradle.kts)；[子模块 a68c174 的 build.gradle](https://github.com/HowieHChen/hyperx-compose/blob/a68c174b1269bc598c01e989fba37816d92cdd28/build.gradle.kts) 指向 Miuix 0.9.0 | 颜色草稿模式可复用；SeekBarPreference、itemPreferenceGroup 等属于其封装，非当前 Miuix API |
| NexioSchedule / `8b767ed4` | [ui-shared 使用本地 :miuix](https://github.com/HaoZai000/NexioSchedule/blob/8b767ed474c89be15edf9e0c14625971ed3e47c1/ui-shared/build.gradle.kts)，排除官方 miuix-ui；[版本目录](https://github.com/HaoZai000/NexioSchedule/blob/8b767ed474c89be15edf9e0c14625971ed3e47c1/gradle/libs.versions.toml)不能单独证明 API 相同 | 本地修改的库；采用任务状态思路，不复制 liquidGlassBackdrop、原生文本框等私有扩展 |
| Payload-Dumper-Compose / `17277855` | [0.3.4 版本目录](https://github.com/rcmiku/Payload-Dumper-Compose/blob/17277855253deb12f434e9c3d1234495e508a7b0/gradle/libs.versions.toml) | 历史实现，仅作概念对照；SuperDialog 与 MiuixPopupUtil.dismissDialog 不作为当前调用模板 |

### Community page and actions

- [Updater App.kt](https://github.com/YuKongA/Updater-KMP/blob/18207977bb0e3a558e42e80d878ad4790c6b0e28/app/shared/src/commonMain/kotlin/App.kt)：Scaffold、折叠/小顶栏、工具栏图标菜单，以及共享 LazyListState 的 VerticalScrollBar。采用 slot 与状态连接方式，不复刻断点、padding 或应用 helper。
- [InstallerX MiuixButtons.kt](https://github.com/wxxsfxyzm/InstallerX-Revived/blob/2bdd19dc552a3742b7b563dd9fa9a74ce56bb644/app/src/main/java/com/rosan/installer/ui/page/miuix/widgets/MiuixButtons.kt)：IconButton 包 Icon，动作描述与图标颜色来自调用场景；wrapper 名称不是库组件。
- [InstallerX MiuixBadge.kt](https://github.com/wxxsfxyzm/InstallerX-Revived/blob/2bdd19dc552a3742b7b563dd9fa9a74ce56bb644/app/src/main/java/com/rosan/installer/ui/page/miuix/widgets/MiuixBadge.kt)：项目提示徽章/详情弹窗组合；不要与 Miuix Badge/BadgedBox 的 API 混淆。

### Community form and selection

- [Updater BasicViews.kt](https://github.com/YuKongA/Updater-KMP/blob/18207977bb0e3a558e42e80d878ad4790c6b0e28/app/shared/src/commonMain/kotlin/ui/BasicViews.kt)：TextField、普通 DropdownPreference、富项 SpinnerPreference 和提交按钮的受控数据连接。其额外 clickable 清焦点不是推荐模式；优先公开 onExpandedChange 或合适的事件边界。
- [Updater LoginDialog.kt](https://github.com/YuKongA/Updater-KMP/blob/18207977bb0e3a558e42e80d878ad4790c6b0e28/app/shared/src/commonMain/kotlin/ui/LoginDialog.kt)：TextFieldState、输入法 Next/Done、密码展示切换、草稿与提交。说明字段与验证文案按本 skill 精简，不照搬全部内容。
- [Updater AutoCompleteTextField.kt](https://github.com/YuKongA/Updater-KMP/blob/18207977bb0e3a558e42e80d878ad4790c6b0e28/app/shared/src/commonMain/kotlin/ui/components/AutoCompleteTextField.kt)：焦点/查询驱动建议，TextField 与锚定 OverlayListPopup 组合。其自定义列表测量、位置 provider 和行实现不属于公共 API。
- [InstallerX MiuixNumPickerWidget.kt](https://github.com/wxxsfxyzm/InstallerX-Revived/blob/2bdd19dc552a3742b7b563dd9fa9a74ce56bb644/app/src/main/java/com/rosan/installer/ui/page/miuix/widgets/MiuixNumPickerWidget.kt)：名称含 NumberPicker，但实际用 Slider + 整数映射与值标签。它与真正的 Miuix NumberPicker 是不同交互，不能按 wrapper 名称选型。

### Community color editing

- [XiaomiHelper ColorPickerDialog.kt](https://github.com/HowieHChen/XiaomiHelper/blob/a2eeb724b4d2427825b09d2d75f4fe3b3782500f/app/src/main/kotlin/dev/lackluster/mihelper/app/screen/systemui/ColorPickerDialog.kt)：ColorPicker 与 HEX 草稿联动，取消不提交，确认处理 alpha。移植时保留输入中间态并校验；`toColorInt` 是 Android 解析依赖，不是 Miuix 跨平台能力。

### Community progress and overlays

- [Updater DeviceListDialog.kt](https://github.com/YuKongA/Updater-KMP/blob/18207977bb0e3a558e42e80d878ad4790c6b0e28/app/shared/src/commonMain/kotlin/ui/DeviceListDialog.kt)：OverlayDialog 内的选项和异步更新动作，禁用重复操作，数据状态由调用方传入。
- [InstallerX MiuixInstallerPage.kt](https://github.com/wxxsfxyzm/InstallerX-Revived/blob/2bdd19dc552a3742b7b563dd9fa9a74ce56bb644/app/src/main/java/com/rosan/installer/ui/page/miuix/installer/MiuixInstallerPage.kt)：WindowBottomSheet 宿主按任务阶段展示内容；应用的状态机与关闭策略不作为库强制流程。
- [InstallerX InstallingContent.kt](https://github.com/wxxsfxyzm/InstallerX-Revived/blob/2bdd19dc552a3742b7b563dd9fa9a74ce56bb644/app/src/main/java/com/rosan/installer/ui/page/miuix/installer/sheetcontent/InstallingContent.kt) 与 [ProgressButton.kt](https://github.com/wxxsfxyzm/InstallerX-Revived/blob/2bdd19dc552a3742b7b563dd9fa9a74ce56bb644/app/src/main/java/com/rosan/installer/ui/page/miuix/widgets/ProgressButton.kt)：未知任务等待与进度反馈；ProgressButton 是自定义 Surface 组合，不能虚构同名库函数。
- [Nexio UpdateDialog.kt](https://github.com/HaoZai000/NexioSchedule/blob/8b767ed474c89be15edf9e0c14625971ed3e47c1/ui-shared/src/commonMain/kotlin/com/haooz/chedule/ui/components/UpdateDialog.kt)：准备/下载/安装阶段分开，未知进度与可靠百分比分开，防重复提交同时保留关闭/后台入口。仅移植状态和操作语义，排除其本地库参数。
- [Payload PartitionDialog.kt](https://github.com/rcmiku/Payload-Dumper-Compose/blob/17277855253deb12f434e9c3d1234495e508a7b0/app/src/main/java/com/rcmiku/payload/dumper/compose/ui/component/PartitionDialog.kt)：旧版列表弹窗；新版改用目标版本 Overlay/Window 的显式 show/dismiss 契约，不带入旧静态关闭工具。

社区阅读不代表运行或编译过这些应用。通用组件的签名与默认行为由本 skill 的固定 Miuix doc/demo/source 补齐；未找到社区使用的组件（如某些 tooltip、breadcrumb、range/vertical slider 变体）明确依据官方资料，不为“社区覆盖”编造使用案例。无需在普通任务中读取所有项目，按组件定位一项证据即可。
