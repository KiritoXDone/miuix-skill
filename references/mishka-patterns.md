# Mishka Practice: Evidence and Transfer Boundaries

本轮把 Mishka 的实战问题转化为可按场景选用的指导，不复制整份规范或应用 helper 的实现。

## Pinned evidence

核对日期：2026-11-08。

| 来源 | 固定证据 | 用途 |
|---|---|---|
| 用户指定指南 | [Mishka UI 规范](https://github.com/YuKongA/Mishka/blob/91e17c02d6eadbca6752ed30014660e378dc9c33/docs/ui-guidelines.md) | 布局、安全区、列表、动画与项目例外 |
| Mishka 依赖 | [libs.versions.toml](https://github.com/YuKongA/Mishka/blob/91e17c02d6eadbca6752ed30014660e378dc9c33/gradle/libs.versions.toml) | 此指南对应 Miuix 0.9.4 |
| 官方入口 | [Miuix 文档](https://compose-miuix-ui.github.io/miuix/) / [中文](https://compose-miuix-ui.github.io/miuix/zh_CN/) | 概念、组件发现；滚动网站不作为旧版本签名证明 |
| 库契约 | [Miuix v0.9.4 源码](https://github.com/compose-miuix-ui/miuix/tree/39c40f99844227b853f0049a0933b1f3ae6c00ba) | 以确切源码解释 Scaffold、弹窗、squircle、Dropdown 等行为 |

Mishka 指南中的 `app/...` 链接需要从**仓库根**解析，不是 `docs/app/...`。应用代码在 `app/src/main/kotlin/top/yukonga/mishka/` 下。

## What to transfer

| 指南中的经验 | 可复用决策 | 不能泛化的部分 | 实施参考 |
|---|---|---|---|
| 三种 squircle 用途 | 只为必要的 fill/clip 建图层，按压高亮需裁剪 | 所有 UI 都手工加圆角 | [styling](styling-icons-and-effects.md) |
| 页面 top padding + 尾 spacer | 明确每条边的 inset 所有者 | 有 bottomBar 也丢弃 bottom padding | [page layout](page-layout-and-insets.md) |
| 宽屏 rail 与内容居中 | shell 判断统一、列表命中区保持完整 | 600/800 dp 变成库强制值 | [page layout](page-layout-and-insets.md) |
| 分段卡片 | 大动态组按可见行 lazy 组合 | 两三个静态设置行也强制拆段 | [performance](performance-and-animation.md) |
| 展开收起与布局 morph | 稳定 key、退场保留、单一几何动画所有者 | 一律 chunked(2)、一律禁用 placement | [performance](performance-and-animation.md) |
| Sheet 安全区与长 Dialog | 分清 IME/内容 padding/高度上限，操作按钮可达 | 所有平台硬编码 500 dp 或绝不处理水平 inset | [overlays](overlays-and-windows.md) |
| 应用语义色与 Flow | 集中状态角色、正确生命周期 | 将 `StatusColors` 当成库 token、跨平台盲加 Android 依赖 | [design](design-language.md)、[performance](performance-and-animation.md) |
| `@Immutable` 与集合 | 核验稳定性契约和实际比较成本 | 普通 List 必然不可 skip 的结论 | [performance](performance-and-animation.md) |
| 1 Hz 水印动画 | 连续样本身份、延迟读取、不可见暂停 | 1100 ms、capacity - 2 成为所有图表公式 | [performance](performance-and-animation.md) |

## Application-owned helpers

使用前检查目标项目是否已有等价实现；若无，则按实际任务实现并明确归属，不生成虚构的 Miuix import。

| Mishka 名称 | 归属 / 证据 |
|---|---|
| `groupedCardItems`, `CardItem`, `CardSegment` | [GroupedCardItems.kt](https://github.com/YuKongA/Mishka/blob/91e17c02d6eadbca6752ed30014660e378dc9c33/app/src/main/kotlin/top/yukonga/mishka/ui/component/GroupedCardItems.kt) |
| `WideContentBox`, `rememberIsWideScreen`, `horizontalCutoutPadding`, `sheetContentSafePadding`, `sheetHeightTransition` | [WindowSize.kt](https://github.com/YuKongA/Mishka/blob/91e17c02d6eadbca6752ed30014660e378dc9c33/app/src/main/kotlin/top/yukonga/mishka/ui/util/WindowSize.kt) |
| `AdaptiveTopAppBar` | [AdaptiveTopAppBar.kt](https://github.com/YuKongA/Mishka/blob/91e17c02d6eadbca6752ed30014660e378dc9c33/app/src/main/kotlin/top/yukonga/mishka/ui/component/AdaptiveTopAppBar.kt) |
| `rememberSearchScreenStatus`, `SearchResultStatusEffect` | [SearchScreenState.kt](https://github.com/YuKongA/Mishka/blob/91e17c02d6eadbca6752ed30014660e378dc9c33/app/src/main/kotlin/top/yukonga/mishka/ui/component/SearchScreenState.kt) |
| `BlurredBar`, `rememberBlurBackdrop`, `scrollEndHaptic`, `LocalPlatformDensity`, `StatusColors` | 应用封装/策略；从目标 helper 及调用方核实，不冒充公共库 API |

与这些 helper 不同，`WindowDropdownDialog` 是真实的 Miuix 公共函数，位于 `miuix-preference/.../popup/WindowDropdownPopup.kt`；文件名不等于函数名。按 [Preference 参考](preferences-and-menus.md#standalone-single-choice-dialog) 核对调用。

## Deliberately project-specific

不推广代理/VPN/IP 的业务判断、特定 tab/图标顺序、32 dp 代理图标、35 dp 操作按钮、三按钮排列、选中计数业务规则。一般 UI 可借鉴“筛选不应意外清除选择”与“状态由领域层提供”，但不能从视觉指南推断新项目的业务要求。

本 skill 在既有本地版本上更新；原版本 README 指向 [limczhh/miuix-skill](https://github.com/limczhh/miuix-skill)。保留该来源归属。Mishka 仓库标注 GPL-3.0，Miuix 仓库标注 Apache-2.0；此处引用与归纳不改变任何上游文件的许可，也不随 skill 分发上游应用实现。复用源码时核查其对应许可和归属。
