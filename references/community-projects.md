# Community Project Reference

按 awesome-miuix 的应用列表全量覆盖，共 **15/15 个项目**。核对基准是 [清单提交 823f1e9](https://github.com/YuKongA/awesome-miuix/blob/823f1e9e98b4da2c8f08a5d54894d8279702568f/README.md)，核对日期 2026-11-10。每项均核查依赖与代表性界面源码；“全量覆盖”指所有列出的项目都有证据，不代表逐行审计所有业务代码，也不代表已运行其应用。

这是一份按需检索的来源索引。实际实现优先读 [逐组件用法](component-usage.md)、[设置与编辑器](settings-and-editors.md)、[集合与任务流程](collections-and-workflows.md)。保留本 skill 的文案、密度、交互边界与状态要求，不把项目业务/命名/尺寸当作规范。版本差异只记录在此，不新增历史版本说明文件。

## Coverage matrix

| 项目 | 固定提交 | 依赖形态 | 主要参考主题 |
|---|---|---|---|
| [InstallerX Revived](#installerx) | `2bdd19dc` | 快照 0.9.4-ea2dbb3a-SNAPSHOT | 多阶段任务、Sheet、进度动作 |
| [Payload-Dumper-Compose](#payload) | `17277855` | 旧版 0.3.4 | 文件/URL 输入、列表项进度、详情弹窗 |
| [Updater-KMP](#updater) | `18207977` | 官方 0.9.4 | 跨平台表单、自动完成、富选项、响应式列表 |
| [Mishka](#mishka) | `91e17c02` | 官方 0.9.4 | 分段 lazy 卡片、搜索、自适应、动画状态 |
| [NetProxy](#netproxy) | `a7ce375f` | 官方 0.9.4 | 刷新/搜索列表、事件 Snackbar、分组表面 |
| [YumeBox](#yumebox) | `b073c8f2` | 目录标 0.9.4；本地同步 fork + glass | 排序草稿、表单脏状态、选择列表、独立弹层 |
| [KernelSU](#kernelsu) | `730f6446` | 官方 0.9.4；另有 Material 分支 | 状态卡、原生设置行、held 入口、普通动作 |
| [GhostLock](#ghostlock) | `4ebc726b` | 官方 0.9.4 | 能力门控、任务日志、导航、配置树与编辑 |
| [HyperIsland](#hyperisland) | `764fc020` | 官方 0.9.4 + 应用 wrapper | 三态树、多字段 patch、精确输入与重置 |
| [HyperLyric](#hyperlyric) | `21524033` | 官方 0.9.4 + reorderable | 排序勾选、布局编辑 Sheet、拖动态、路由 |
| [HyperStar](#hyperstar) | `8a1362a9` | 旧版 0.9.0 + 自建 Preference DSL | 精确数值、重置、依赖折叠、设置搜索 |
| [Lyricon](#lyricon) | `c93da0ee` | 旧版 0.9.3 + custom.miuix | 多颜色、数值输入中间态、搜索多选 Sheet |
| [StatusBarLyric](#statusbarlyric) | `bd2ec998` | 旧版 0.4.3 | 分组设置、编辑入口、预览确认、启用依赖 |
| [XiaomiHelper](#xiaomihelper) | `a2eeb724` | hyperx 子模块引入 0.9.0 | 单色/HEX 编辑、依赖设置与预览参数 |
| [NexioSchedule](#nexio) | `8b767ed4` | 本地 :miuix 分支；目录含多个版本 | 时间参数、范围网格、多阶段更新、原生平台差异 |

## Component and pattern lookup

先由组件/需求找到一两个实现，再回到公共契约核对；同一项目同时包含好几种写法，不意味着每一种都需要采纳。

| 组件或组合 | 项目入口 | 可观察的内容 |
|---|---|---|
| Scaffold / TopAppBar / SmallTopAppBar / ScrollBar | [Updater](#updater)、[NetProxy](#netproxy)、[Mishka](#mishka) | 页面宿主、列表/顶栏接线、自适应内容、独立滚动状态 |
| NavigationBar / Rail / NavDisplay | [Mishka](#mishka)、[HyperLyric](#hyperlyric)、[GhostLock](#ghostlock) | 导航外观与返回栈分离、页面状态、手势方向 |
| Card / Surface / BasicComponent / IconButton | [KernelSU](#kernelsu)、[InstallerX](#installerx)、[YumeBox](#yumebox) | 普通容器、动作表面、状态概览、行与子动作边界 |
| Button / TextButton / ProgressIndicator | [InstallerX](#installerx)、[Nexio](#nexio)、[GhostLock](#ghostlock) | 任务阶段、禁用条件、已知/未知进度与退出动作 |
| TextField / 输入法 / 自动完成 | [Updater](#updater)、[Lyricon](#lyricon)、[YumeBox](#yumebox) | 草稿、输入中间态、焦点、建议、提交校验 |
| SwitchPreference / 依赖内容 | [KernelSU](#kernelsu)、[HyperStar](#hyperstar)、[StatusBarLyric](#statusbarlyric) | 受控配置、临时展开与业务值、即时/确认提交 |
| Dropdown / Spinner / 菜单 | [Updater](#updater)、[HyperIsland](#hyperisland)、[KernelSU](#kernelsu) | 简单/富选项、索引到业务值、入口与动作 |
| Checkbox / CheckboxPreference / 三态树 | [HyperIsland](#hyperisland)、[YumeBox](#yumebox)、[Lyricon](#lyricon) | 叶子集合、范围全选、筛选与选择、父子不同动作 |
| Slider / SliderPreference / 精确编辑 | [HyperIsland](#hyperisland)、[HyperStar](#hyperstar)、[InstallerX](#installerx) | 范围、步进、草稿、精确值和重置 |
| ColorPicker / ColorPalette / HEX | [XiaomiHelper](#xiaomihelper)、[Lyricon](#lyricon) | 单色/多色、alpha、合法中间态、确认/取消 |
| OverlayDialog / WindowDialog | [Updater](#updater)、[HyperIsland](#hyperisland)、[GhostLock](#ghostlock) | 编辑和确认、草稿生命周期、对象身份 |
| BottomSheet / 长表单 / 多阶段内容 | [InstallerX](#installerx)、[HyperLyric](#hyperlyric)、[Nexio](#nexio) | 稳定宿主、安全区、退出与任务独立 |
| PullToRefresh / 搜索列表 | [NetProxy](#netproxy)、[Lyricon](#lyricon) | 初次加载与刷新分开、匹配结果、多选 |
| Snackbar / 事件反馈 / 行进度 | [NetProxy](#netproxy)、[Payload](#payload) | 事件 ID、列表项任务身份、失败恢复 |
| lazy 分组 / 排序与选择 | [Mishka](#mishka)、[NetProxy](#netproxy)、[HyperLyric](#hyperlyric)、[YumeBox](#yumebox) | 稳定 key、边角、顺序与选择解耦、拖动态 |
| 批量 patch / 文件导入 / 配置范围 | [HyperIsland](#hyperisland)、[GhostLock](#ghostlock)、[Payload](#payload) | 目标集合、显式更改字段、预览/确认、部分失败 |
| 自定义网格 / 时间配置 / 实时预览 | [Nexio](#nexio)、[HyperLyric](#hyperlyric)、[XiaomiHelper](#xiaomihelper) | 公共组件之外的应用扩展与状态边界 |

## Official examples and demos

清单中的官方 Miuix 仓库、文档、Example、JsCanvas/WasmJs Demo 作为第一层证据；固定版本 doc/demo 路径见 [组件目录](component-selection.md) 与 [源码核验](source-verification.md)。网页 Demo 用来观察组件，不据其滚动版本推断目标项目签名。没有社区样例的组件仍可使用官方公开组件，不能为了“每个都有项目案例”编造来源。

## installerx

**InstallerX Revived** · `2bdd19dc552a3742b7b563dd9fa9a74ce56bb644` · 快照 0.9.4-ea2dbb3a-SNAPSHOT。

依赖：[gradle/libs.versions.toml](https://github.com/wxxsfxyzm/InstallerX-Revived/blob/2bdd19dc552a3742b7b563dd9fa9a74ce56bb644/gradle/libs.versions.toml)。

界面证据：[MiuixInstallerPage](https://github.com/wxxsfxyzm/InstallerX-Revived/blob/2bdd19dc552a3742b7b563dd9fa9a74ce56bb644/app/src/main/java/com/rosan/installer/ui/page/miuix/installer/MiuixInstallerPage.kt)、[InstallingContent](https://github.com/wxxsfxyzm/InstallerX-Revived/blob/2bdd19dc552a3742b7b563dd9fa9a74ce56bb644/app/src/main/java/com/rosan/installer/ui/page/miuix/installer/sheetcontent/InstallingContent.kt)、[ProgressButton](https://github.com/wxxsfxyzm/InstallerX-Revived/blob/2bdd19dc552a3742b7b563dd9fa9a74ce56bb644/app/src/main/java/com/rosan/installer/ui/page/miuix/widgets/ProgressButton.kt)、[MiuixNumPickerWidget](https://github.com/wxxsfxyzm/InstallerX-Revived/blob/2bdd19dc552a3742b7b563dd9fa9a74ce56bb644/app/src/main/java/com/rosan/installer/ui/page/miuix/widgets/MiuixNumPickerWidget.kt)。

可提炼：固定 Sheet 宿主承载准备、确认、执行和结果；未知进度与可度量进度分开；滑块配数值显示；图标按钮透传动作语义。

边界：ProgressButton 是应用 Surface 组合，名称含 NumPicker 的 wrapper 实际用 Slider；不能作为同名 Miuix 组件。快照参数和混入的 Material 字体需按目标版本重核。

## payload

**Payload-Dumper-Compose** · `17277855253deb12f434e9c3d1234495e508a7b0` · 旧版 0.3.4。

依赖：[gradle/libs.versions.toml](https://github.com/rcmiku/Payload-Dumper-Compose/blob/17277855253deb12f434e9c3d1234495e508a7b0/gradle/libs.versions.toml)。

界面证据：[PayloadDumperApp](https://github.com/rcmiku/Payload-Dumper-Compose/blob/17277855253deb12f434e9c3d1234495e508a7b0/app/src/main/java/com/rcmiku/payload/dumper/compose/ui/PayloadDumperApp.kt)、[InputDialog](https://github.com/rcmiku/Payload-Dumper-Compose/blob/17277855253deb12f434e9c3d1234495e508a7b0/app/src/main/java/com/rcmiku/payload/dumper/compose/ui/component/InputDialog.kt)、[PartitionDialog](https://github.com/rcmiku/Payload-Dumper-Compose/blob/17277855253deb12f434e9c3d1234495e508a7b0/app/src/main/java/com/rcmiku/payload/dumper/compose/ui/component/PartitionDialog.kt)。

可提炼：输入来源、已解析结果和每项执行进度分开；详情由所选数据身份驱动，任务进度映射回该行而非整页单一 Boolean。

边界：SuperDialog、旧 popup 工具与旧图标路径不能直接复制；新版用 Overlay/Window 的显式 show/dismiss。运行状态和 I/O 由任务所有者管理，不在 composable 主体执行。

## updater

**Updater-KMP** · `18207977bb0e3a558e42e80d878ad4790c6b0e28` · 官方 0.9.4。

依赖：[gradle/libs.versions.toml](https://github.com/YuKongA/Updater-KMP/blob/18207977bb0e3a558e42e80d878ad4790c6b0e28/gradle/libs.versions.toml)。

界面证据：[App](https://github.com/YuKongA/Updater-KMP/blob/18207977bb0e3a558e42e80d878ad4790c6b0e28/app/shared/src/commonMain/kotlin/App.kt)、[BasicViews](https://github.com/YuKongA/Updater-KMP/blob/18207977bb0e3a558e42e80d878ad4790c6b0e28/app/shared/src/commonMain/kotlin/ui/BasicViews.kt)、[LoginDialog](https://github.com/YuKongA/Updater-KMP/blob/18207977bb0e3a558e42e80d878ad4790c6b0e28/app/shared/src/commonMain/kotlin/ui/LoginDialog.kt)、[AutoCompleteTextField](https://github.com/YuKongA/Updater-KMP/blob/18207977bb0e3a558e42e80d878ad4790c6b0e28/app/shared/src/commonMain/kotlin/ui/components/AutoCompleteTextField.kt)、[DeviceListDialog](https://github.com/YuKongA/Updater-KMP/blob/18207977bb0e3a558e42e80d878ad4790c6b0e28/app/shared/src/commonMain/kotlin/ui/DeviceListDialog.kt)。

可提炼：Scaffold slot 与共享滚动状态、输入法提交、Dropdown/Spinner 的业务值映射、查询/焦点驱动建议列表、异步更新禁用重复动作。

边界：自动完成的位置测量属于应用代码；不复制额外 clickable 清焦点，不把所有 summary、固定间距或高级选项字段推广到其他产品。

## mishka

**Mishka** · `91e17c02d6eadbca6752ed30014660e378dc9c33` · 官方 0.9.4。

依赖：[gradle/libs.versions.toml](https://github.com/YuKongA/Mishka/blob/91e17c02d6eadbca6752ed30014660e378dc9c33/gradle/libs.versions.toml)。

界面证据：[UI guide](https://github.com/YuKongA/Mishka/blob/91e17c02d6eadbca6752ed30014660e378dc9c33/docs/ui-guidelines.md)、[GroupedCardItems](https://github.com/YuKongA/Mishka/blob/91e17c02d6eadbca6752ed30014660e378dc9c33/app/src/main/kotlin/top/yukonga/mishka/ui/component/GroupedCardItems.kt)、[WindowSize](https://github.com/YuKongA/Mishka/blob/91e17c02d6eadbca6752ed30014660e378dc9c33/app/src/main/kotlin/top/yukonga/mishka/ui/util/WindowSize.kt)、[SearchScreenState](https://github.com/YuKongA/Mishka/blob/91e17c02d6eadbca6752ed30014660e378dc9c33/app/src/main/kotlin/top/yukonga/mishka/ui/component/SearchScreenState.kt)、[AppNavigation](https://github.com/YuKongA/Mishka/blob/91e17c02d6eadbca6752ed30014660e378dc9c33/app/src/main/kotlin/top/yukonga/mishka/ui/navigation/AppNavigation.kt)。

可提炼：按行 lazy 化保留完整组轮廓；安全区逐边归属；宽屏外观与内容最大宽度分开；搜索查询和结果状态单向更新；连续收起时保持可测量退场内容。

边界：相关 helper、固定断点、代理业务和动画常数不属于库契约。稳定性判断遵循实际 Compose strong skipping；不要照搬“普通 List 一定不可跳过”。

## netproxy

**NetProxy** · `a7ce375fcf41291ed3add9661fa16ba613c8f04b` · 官方 0.9.4。

依赖：[src/android/gradle/libs.versions.toml](https://github.com/Fanju6/NetProxy-Magisk/blob/a7ce375fcf41291ed3add9661fa16ba613c8f04b/src/android/gradle/libs.versions.toml)。

界面证据：[AppsScreen](https://github.com/Fanju6/NetProxy-Magisk/blob/a7ce375fcf41291ed3add9661fa16ba613c8f04b/src/android/app/src/main/java/com/fanjv/netproxy/feature/apps/presentation/AppsScreen.kt)、[AppSnackbar](https://github.com/Fanju6/NetProxy-Magisk/blob/a7ce375fcf41291ed3add9661fa16ba613c8f04b/src/android/app/src/main/java/com/fanjv/netproxy/core/ui/component/AppSnackbar.kt)、[ModulePage](https://github.com/Fanju6/NetProxy-Magisk/blob/a7ce375fcf41291ed3add9661fa16ba613c8f04b/src/android/app/src/main/java/com/fanjv/netproxy/core/ui/component/ModulePage.kt)、[GroupedCardItems](https://github.com/Fanju6/NetProxy-Magisk/blob/a7ce375fcf41291ed3add9661fa16ba613c8f04b/src/android/app/src/main/java/com/fanjv/netproxy/core/ui/component/GroupedCardItems.kt)。

可提炼：首屏加载与保留内容的刷新分开；搜索/主列表用稳定行 ID；可见行图标加载与生命周期结合；Snackbar 通过事件身份驱动。

边界：消费事件的时机须按产品投递语义选择，不能由示例保证严格一次显示。低层 DropdownImpl/PopupHost 并非默认菜单方案；优先完整公共菜单组件。

## yumebox

**YumeBox** · `b073c8f273afa6ea92e801e53c4f07b971edb7b4` · 目录标 0.9.4；本地同步 fork + glass。

依赖：[gradle/libs.versions.toml](https://github.com/YumeYucca/YumeBox/blob/b073c8f273afa6ea92e801e53c4f07b971edb7b4/gradle/libs.versions.toml)。

界面证据：[ProfileEditDraft](https://github.com/YumeYucca/YumeBox/blob/b073c8f273afa6ea92e801e53c4f07b971edb7b4/app/src/screen/profiles/ProfileEditDraft.kt)、[ProfilesDialogHost](https://github.com/YumeYucca/YumeBox/blob/b073c8f273afa6ea92e801e53c4f07b971edb7b4/app/src/screen/profiles/ProfilesDialogHost.kt)、[ProfilesSortSheet](https://github.com/YumeYucca/YumeBox/blob/b073c8f273afa6ea92e801e53c4f07b971edb7b4/app/src/screen/profiles/ProfilesSortSheet.kt)、[AccessControlAppList](https://github.com/YumeYucca/YumeBox/blob/b073c8f273afa6ea92e801e53c4f07b971edb7b4/app/src/screen/settings/AccessControlAppList.kt)、[submodule](https://github.com/YumeYucca/YumeBox/blob/b073c8f273afa6ea92e801e53c4f07b971edb7b4/.gitmodules)、[SyncMiuixSources](https://github.com/YumeYucca/YumeBox/blob/b073c8f273afa6ea92e801e53c4f07b971edb7b4/build-logic/miuix-glass/src/SyncMiuixSources.kt)。

可提炼：编辑对象身份决定草稿生命周期；保存门控与差异 patch 分开；排序先改本地顺序、确认再提交；选择集按业务 ID 保存。

边界：真实 UI 从 fork 同步源码并改写部分平台桥接，GlassBottomSheet 不是本 skill 基线公共 API。示例中同动作行/Checkbox 回调、关闭完成回调须防重复，不无条件照搬。

## kernelsu

**KernelSU** · `730f644615969cfd4a84b45d7f62a667b7b95a50` · 官方 0.9.4；另有 Material 分支。

依赖：[manager/gradle/libs.versions.toml](https://github.com/tiann/KernelSU/blob/730f644615969cfd4a84b45d7f62a667b7b95a50/manager/gradle/libs.versions.toml)。

界面证据：[HomeMiuix](https://github.com/tiann/KernelSU/blob/730f644615969cfd4a84b45d7f62a667b7b95a50/manager/app/src/main/java/me/weishu/kernelsu/ui/screen/home/HomeMiuix.kt)、[SettingsMiuix](https://github.com/tiann/KernelSU/blob/730f644615969cfd4a84b45d7f62a667b7b95a50/manager/app/src/main/java/me/weishu/kernelsu/ui/screen/settings/SettingsMiuix.kt)、[SuperEditArrow](https://github.com/tiann/KernelSU/blob/730f644615969cfd4a84b45d7f62a667b7b95a50/manager/app/src/main/java/me/weishu/kernelsu/ui/component/miuix/SuperEditArrow.kt)、[WarningCard](https://github.com/tiann/KernelSU/blob/730f644615969cfd4a84b45d7f62a667b7b95a50/manager/app/src/main/java/me/weishu/kernelsu/ui/component/miuix/WarningCard.kt)。

可提炼：由组件处理点击和长按/按压；弹窗入口按打开生命周期保持反馈；普通设置直接使用受控 Preference；绿/黄/红状态配色见可选状态卡模式。

边界：区分 Material 与 Miuix 实现、静态图标与动作图标；不要对所有表面加 Tilt，也不把 selected 绑到 holdDown。黄红大图标卡是本 skill 的可选扩展，不是上游现成三件套。

## ghostlock

**GhostLock** · `4ebc726b9001d79f3c374c917d51569e310df6bc` · 官方 0.9.4。

依赖：[app/build.gradle.kts](https://github.com/YuKongA/ghostlock-app/blob/4ebc726b9001d79f3c374c917d51569e310df6bc/app/build.gradle.kts)。

界面证据：[GhostlockUI](https://github.com/YuKongA/ghostlock-app/blob/4ebc726b9001d79f3c374c917d51569e310df6bc/app/src/main/kotlin/com/ghostlock/app/ui/GhostlockUI.kt)、[AdvancedUI](https://github.com/YuKongA/ghostlock-app/blob/4ebc726b9001d79f3c374c917d51569e310df6bc/app/src/main/kotlin/com/ghostlock/app/ui/AdvancedUI.kt)。

可提炼：界面状态与动作接口分离；运行中、平台可用、输入有效分别决定按钮状态；执行 Sheet 与日志读取分开；详情/编辑按数据身份路由。

边界：不移植其系统操作与领域规则。navBackStackOf 的内存栈不等于可持久恢复栈；避免重复滚动触觉 modifier，真实持久化使用目标项目的保存策略。

## hyperisland

**HyperIsland** · `764fc0206baa70209b6c849febd5673264786722` · 官方 0.9.4 + 应用 wrapper。

依赖：[android/app/build.gradle.kts](https://github.com/1812z/HyperIsland/blob/764fc0206baa70209b6c849febd5673264786722/android/app/build.gradle.kts)。

界面证据：[ConfigSectionTree](https://github.com/1812z/HyperIsland/blob/764fc0206baa70209b6c849febd5673264786722/android/app/src/main/kotlin/io/github/hyperisland/compose/component/ConfigSectionTree.kt)、[PreferenceComponents](https://github.com/1812z/HyperIsland/blob/764fc0206baa70209b6c849febd5673264786722/android/app/src/main/kotlin/io/github/hyperisland/compose/component/PreferenceComponents.kt)、[AppearanceDialogs](https://github.com/1812z/HyperIsland/blob/764fc0206baa70209b6c849febd5673264786722/android/app/src/main/kotlin/io/github/hyperisland/compose/component/AppearanceDialogs.kt)、[BatchChannelSettingsPage](https://github.com/1812z/HyperIsland/blob/764fc0206baa70209b6c849febd5673264786722/android/app/src/main/kotlin/io/github/hyperisland/compose/page/apps/channel/BatchChannelSettingsPage.kt)。

可提炼：树展开与叶子选择独立；父勾选由后代集合派生；SliderPreference 接数值编辑/重置；批量编辑仅提交显式改动字段。

边界：配置 schema、FlutterPrefsRepository、预览资源加载和自制玻璃导航是应用实现；批量任务要补目标项目的异常恢复/部分失败策略，不以示例流程替代。

## hyperlyric

**HyperLyric** · `21524033bdb1232f2dbda32ba76830b22d55e24b` · 官方 0.9.4 + reorderable。

依赖：[gradle/libs.versions.toml](https://github.com/limczhh/HyperLyric/blob/21524033bdb1232f2dbda32ba76830b22d55e24b/gradle/libs.versions.toml)。

界面证据：[ReorderableCheckboxList](https://github.com/limczhh/HyperLyric/blob/21524033bdb1232f2dbda32ba76830b22d55e24b/app/src/main/java/com/lidesheng/hyperlyric/ui/component/ReorderableCheckboxList.kt)、[ContentLayoutEditorBottomSheet](https://github.com/limczhh/HyperLyric/blob/21524033bdb1232f2dbda32ba76830b22d55e24b/app/src/main/java/com/lidesheng/hyperlyric/ui/page/hooksettings/lyrics/contentlayout/ContentLayoutEditorBottomSheet.kt)、[AppNavigation](https://github.com/limczhh/HyperLyric/blob/21524033bdb1232f2dbda32ba76830b22d55e24b/app/src/main/java/com/lidesheng/hyperlyric/ui/navigation/AppNavigation.kt)、[SuperSwitchPreference](https://github.com/limczhh/HyperLyric/blob/21524033bdb1232f2dbda32ba76830b22d55e24b/app/src/main/java/com/lidesheng/hyperlyric/ui/component/SuperSwitchPreference.kt)。

可提炼：顺序与勾选集分开，确认后合成最终值；稳定 key 与最新 move 回调避免拖动错项；held 可表达正在拖动的暂态，不是业务选中态。

边界：拖拽由 sh.calvin.reorderable 提供，非 Miuix。不要照搬固定 Sheet 高度或偏移修补；退出动画需要宿主保留到完成，不能靠立刻移除 composable 宣称动画已验证。

## hyperstar

**HyperStar** · `8a1362a9598d2cf39ee650ac5d0eb86b80d97eee` · 旧版 0.9.0 + 自建 Preference DSL。

依赖：[gradle/libs.versions.toml](https://github.com/YunZiA/HyperStar/blob/8a1362a9598d2cf39ee650ac5d0eb86b80d97eee/gradle/libs.versions.toml)。

界面证据：[SliderPreferenceImpl](https://github.com/YunZiA/HyperStar/blob/8a1362a9598d2cf39ee650ac5d0eb86b80d97eee/app/src/main/java/com/yunzia/hyperstar/ui/component/preference/impl/SliderPreferenceImpl.kt)、[SwitchFolderPreferenceImpl](https://github.com/YunZiA/HyperStar/blob/8a1362a9598d2cf39ee650ac5d0eb86b80d97eee/app/src/main/java/com/yunzia/hyperstar/ui/component/preference/impl/SwitchFolderPreferenceImpl.kt)、[SearchPreferenceScreen](https://github.com/YunZiA/HyperStar/blob/8a1362a9598d2cf39ee650ac5d0eb86b80d97eee/app/src/main/java/com/yunzia/hyperstar/ui/component/preference/SearchPreferenceScreen.kt)、[PreferenceScope](https://github.com/YunZiA/HyperStar/blob/8a1362a9598d2cf39ee650ac5d0eb86b80d97eee/app/src/main/java/com/yunzia/hyperstar/ui/component/preference/PreferenceScope.kt)。

可提炼：数值显示与精确输入共享范围/精度；重置显式回到默认；依赖可见性从控制值派生；搜索设置与普通列表保留各自滚动上下文。

边界：XScaffold、Pr/SpPreference、miuix-shapes、旧 Navigation3 及设置存储 DSL 不是当前库 API。组合期回写状态与精度转 steps 需改成可靠事件/规范化规则。

## lyricon

**Lyricon** · `c93da0eef8b7aee015d2670b48e9e262fcf72cd1` · 旧版 0.9.3 + custom.miuix。

依赖：[gradle/libs.versions.toml](https://github.com/tomakino/lyricon/blob/c93da0eef8b7aee015d2670b48e9e262fcf72cd1/gradle/libs.versions.toml)。

界面证据：[NumberTextField](https://github.com/tomakino/lyricon/blob/c93da0eef8b7aee015d2670b48e9e262fcf72cd1/app/src/main/kotlin/io/github/proify/lyricon/app/compose/NumberTextField.kt)、[MultiColorEditPaletteDialog](https://github.com/tomakino/lyricon/blob/c93da0eef8b7aee015d2670b48e9e262fcf72cd1/app/src/main/kotlin/io/github/proify/lyricon/app/compose/color/MultiColorEditPaletteDialog.kt)、[PackageSelectionBottomSheet](https://github.com/tomakino/lyricon/blob/c93da0eef8b7aee015d2670b48e9e262fcf72cd1/app/src/main/kotlin/io/github/proify/lyricon/app/activity/lyric/pkg/sheet/PackageSelectionBottomSheet.kt)。

可提炼：数字文本保留光标与合法中间态；颜色集合可增删、选中后编辑；异步加载与查询分类、多选集分离。

边界：NumberTextField.isError、custom.miuix.CheckboxPreference 都是 wrapper，不反推公共签名。不要照搬在 composition 里增删颜色/修正索引；改由事件或初始化处理。即时多选与确认提交按产品选择。

## statusbarlyric

**StatusBarLyric** · `bd2ec998c4a4d667d5e0602923bdd9cfec330a54` · 旧版 0.4.3。

依赖：[gradle/libs.versions.toml](https://github.com/Block-Network/StatusBarLyric/blob/bd2ec998c4a4d667d5e0602923bdd9cfec330a54/gradle/libs.versions.toml)。

界面证据：[HomePage](https://github.com/Block-Network/StatusBarLyric/blob/bd2ec998c4a4d667d5e0602923bdd9cfec330a54/app/src/main/kotlin/statusbar/lyric/ui/page/HomePage.kt)、[LyricPage](https://github.com/Block-Network/StatusBarLyric/blob/bd2ec998c4a4d667d5e0602923bdd9cfec330a54/app/src/main/kotlin/statusbar/lyric/ui/page/LyricPage.kt)、[ChoosePage](https://github.com/Block-Network/StatusBarLyric/blob/bd2ec998c4a4d667d5e0602923bdd9cfec330a54/app/src/main/kotlin/statusbar/lyric/ui/page/ChoosePage.kt)。

可提炼：相关设置集中分组；复杂参数由入口打开编辑面板；选择对象先预览再确认；加载/能力状态限制可用操作。

边界：SuperArrow/SuperSwitch/SuperDropdown/SuperDialog 和旧图标命名只作迁移线索。不要复制长功能说明、全局可变上下文或原始线程/休眠作为通用 UI 实现。

## xiaomihelper

**XiaomiHelper** · `a2eeb724b4d2427825b09d2d75f4fe3b3782500f` · hyperx 子模块引入 0.9.0。

依赖：[gradle/libs.versions.toml](https://github.com/HowieHChen/XiaomiHelper/blob/a2eeb724b4d2427825b09d2d75f4fe3b3782500f/gradle/libs.versions.toml)。

补充依赖：[hyperx-compose a68c174 build](https://github.com/HowieHChen/hyperx-compose/blob/a68c174b1269bc598c01e989fba37816d92cdd28/build.gradle.kts) 固定为 Miuix 0.9.0。

界面证据：[ColorPickerDialog](https://github.com/HowieHChen/XiaomiHelper/blob/a2eeb724b4d2427825b09d2d75f4fe3b3782500f/app/src/main/kotlin/dev/lackluster/mihelper/app/screen/systemui/ColorPickerDialog.kt)、[FontScaleSheet](https://github.com/HowieHChen/XiaomiHelper/blob/a2eeb724b4d2427825b09d2d75f4fe3b3782500f/app/src/main/kotlin/dev/lackluster/mihelper/app/screen/system/FontScaleSheet.kt)、[ElementTab](https://github.com/HowieHChen/XiaomiHelper/blob/a2eeb724b4d2427825b09d2d75f4fe3b3782500f/app/src/main/kotlin/dev/lackluster/mihelper/app/screen/systemui/media/tabs/ElementTab.kt)、[app build](https://github.com/HowieHChen/XiaomiHelper/blob/a2eeb724b4d2427825b09d2d75f4fe3b3782500f/app/build.gradle.kts)。

可提炼：图形颜色与 HEX 共同编辑草稿；alpha 支持明确；显示/布局设置按业务依赖显隐；预览数值与配置值关联。

边界：hyperx 的 SeekBarPreference、itemPreferenceGroup、DropDownPreference 是独立封装；以子模块真实依赖而非主版本目录确认 Miuix 版本。

## nexio

**NexioSchedule** · `8b767ed474c89be15edf9e0c14625971ed3e47c1` · 本地 :miuix 分支；目录含多个版本。

依赖：[gradle/libs.versions.toml](https://github.com/HaoZai000/NexioSchedule/blob/8b767ed474c89be15edf9e0c14625971ed3e47c1/gradle/libs.versions.toml)。

界面证据：[UpdateDialog](https://github.com/HaoZai000/NexioSchedule/blob/8b767ed474c89be15edf9e0c14625971ed3e47c1/ui-shared/src/commonMain/kotlin/com/haooz/chedule/ui/components/UpdateDialog.kt)、[TimeConfigEditScreen](https://github.com/HaoZai000/NexioSchedule/blob/8b767ed474c89be15edf9e0c14625971ed3e47c1/ui-shared/src/commonMain/kotlin/com/haooz/chedule/ui/screens/TimeConfigEditScreen.kt)、[WeekRangeSelectGrid](https://github.com/HaoZai000/NexioSchedule/blob/8b767ed474c89be15edf9e0c14625971ed3e47c1/ui-shared/src/commonMain/kotlin/com/haooz/chedule/ui/components/WeekRangeSelectGrid.kt)、[ui-shared build](https://github.com/HaoZai000/NexioSchedule/blob/8b767ed474c89be15edf9e0c14625971ed3e47c1/ui-shared/build.gradle.kts)。

可提炼：时间草稿与格式化/校验分离；仅展示展开的开关不写业务启用值；网格拖选读最新选择集；更新分准备/下载/安装阶段。

边界：本地 fork 的 native text、liquidGlassBackdrop、CapsuleSlider、WeekRangeSelectGrid 不是官方 Miuix。吸收状态与手势原则，不复制大型单文件或平台专属实现。

## Reading and reuse limits

- 核查 import 和实际构建依赖：同名包、版本目录和 wrapper 名称都可能掩盖本地 fork。不得将旧版的 Super*、旧导航模块或社区玻璃组件冒充当前公共 API。
- 应用选择不自动成为通用要求。对同一模式比较不同项目后，保留状态/布局契约和条件，参数由目标产品决定。
- 保留来源归属，遵循对应仓库许可；本 skill 归纳用法，不分发这些项目的 UI 实现源码。
- 本轮结论是源码与依赖核验。编译、真机帧感、跨窗口手势与无障碍运行检查仍属于目标应用交付验证。
