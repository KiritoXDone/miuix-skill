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

核对日期：2026-11-10。以 [awesome-miuix 固定清单](https://github.com/YuKongA/awesome-miuix/blob/823f1e9e98b4da2c8f08a5d54894d8279702568f/README.md) 为范围，已覆盖全部 **15 个应用项目**，每个项目核查依赖与代表性界面源码。完整提交、文件证据、可提炼模式和局限集中在 [全项目索引](community-projects.md)。不是对所有业务代码逐行审计，也未编译/运行这些应用。

### Dependency compatibility

[项目覆盖表](community-projects.md#coverage-matrix) 区分官方发布版、旧版、快照、间接依赖和本地 fork。主版本目录不能证明实际源码相同：需要继续检查子模块、source inclusion、排除官方 artifact 的配置和实际 import。公共签名仍以目标项目的确切版本为准。

### Community page and actions

页面、bar、scrollbar 与动作入口可从 [Updater](community-projects.md#updater)、[KernelSU](community-projects.md#kernelsu)、[NetProxy](community-projects.md#netproxy) 和 [GhostLock](community-projects.md#ghostlock) 定位；完整任务宿主见 [InstallerX](community-projects.md#installerx)。其布局尺寸/系统操作不形成库默认要求。

### Community form and selection

表单/建议、数值/重置、多选/排序分别参考 [Updater](community-projects.md#updater)、[HyperIsland](community-projects.md#hyperisland)、[HyperStar](community-projects.md#hyperstar)、[HyperLyric](community-projects.md#hyperlyric)、[YumeBox](community-projects.md#yumebox)、[Lyricon](community-projects.md#lyricon)。通用规则已整理到 [设置编辑器](settings-and-editors.md) 和 [集合流程](collections-and-workflows.md)，不复制其存储层或 wrapper 命名。

### Community color editing

[XiaomiHelper](community-projects.md#xiaomihelper) 的单色/HEX 草稿、[Lyricon](community-projects.md#lyricon) 的多色集合、[HyperIsland](community-projects.md#hyperisland) 的外观参数补充了颜色与预览组合；公共 ColorPicker/ColorPalette 的数据契约仍以固定源码为准。

### Community progress and overlays

多阶段任务、列表项进度与编辑弹层可参考 [InstallerX](community-projects.md#installerx)、[Nexio](community-projects.md#nexio)、[Payload](community-projects.md#payload)、[GhostLock](community-projects.md#ghostlock)；原始系统能力不在 Miuix 组件职责内。

### Community layout and configuration

[Mishka](community-projects.md#mishka) 的分组/安全区/动画、[StatusBarLyric](community-projects.md#statusbarlyric) 的旧版分组编辑、[HyperStar](community-projects.md#hyperstar) 的搜索设置，以及 [Nexio](community-projects.md#nexio) 的自定义范围选择提供了不同组合的证据。保留通用状态/几何原则，明确哪些是旧 API、外部库或私有实现。

没有社区调用证据的公开组件（如某些 tooltip、breadcrumb、range/vertical slider 变体），继续使用官方 doc/demo/source，不编造项目使用记录。日常实现按任务选取相关一节；本 skill 内容全面不意味着每次都加载全部参考。
