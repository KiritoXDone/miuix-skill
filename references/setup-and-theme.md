# Project Setup and Theme

Use this reference to decide who owns the application theme and page/popup host. After ownership is resolved, continue with [integrated usage patterns](usage-patterns.md) for page composition and [design language](design-language.md) for visual decisions.

## Theme and Host Ownership

| Code context | Theme rule | Scaffold rule |
|---|---|---|
| Existing Miuix application screen/component | Inspect and reuse the application's existing `MiuixTheme`; do not nest another theme merely to make a snippet self-contained | Reuse the intended page/root Scaffold; add a nested Scaffold only for a real page boundary or deliberately scoped popup host |
| Standalone runnable `App`/complete example | Create a remembered `ThemeController` from the example's theme inputs and provide one root `MiuixTheme` | Add Scaffold when the example needs bars, insets, snackbar, floating controls, or Overlay components |
| Isolated component snippet | State that the caller must place it below `MiuixTheme`; do not invent application theme ownership | State any host requirement; wrap locally only when the snippet must be independently runnable |

When theme inputs such as color mode, key color, palette style, or color specification can change, include them as `remember` keys as demonstrated by `example/shared/.../ui/Theme.kt`.

## Density ownership across navigation

当首页正常、二级/更深页面整体偏大时，先定位有效布局密度及尺寸来源。视觉上的“DPI 不一致”是待核实症状，不能仅凭截图确定原因，也不能先逐页缩小字体/间距来掩盖它。

### One application scale policy

- 没有应用级缩放需求时，所有页面沿用平台 `LocalDensity`，不为“统一 DPI”新增固定密度。已有应用缩放时，在共同应用根处理一次，让 `MiuixTheme`、**整个 `NavDisplay`**、首页、二级页面及同树全局弹层处于同一策略下；仅在 `entry<Home>` 或首页内部包 provider 会遗漏其他路由。
- 在自定义 provider **外**取得宿主的基础 density，再结合唯一的应用缩放值计算有效 density。子页面继承结果，不重乘缩放、不恢复平台密度、不自行缓存另一套比例。返回、深链进入与恢复页面也必须经过同一根入口。
- 应用缩放是同一策略，并不意味着不同设备/显示器必须使用相同的 density 数字。平台 density、窗口或设置变化时，按当前宿主重新派生；不要把首次读取值放入无依赖的 `remember` 后永久冻结。
- 区分布局 `density`、用户字体 `fontScale`、主题文字样式与纯绘制 `graphicsLayer` 缩放。应用整体缩放通常保留宿主 fontScale；未经明确产品要求，不用 `fontScale = 1f` 覆盖无障碍设置。px/dp/sp 换算使用当前 Density API，不手工重复乘倍率。
- `WindowDialog`、`WindowBottomSheet` 或另一个 Activity/窗口，检查其实际 CompositionLocal 传递和宿主密度。已经继承有效 density 的不要再包一次；另建 composition 的用该宿主基础密度应用同一缩放策略。不能根据 `Window` 名称就断言其一定继承或一定丢失缩放。

已有全局缩放的层级示意（不是要新增这些业务 wrapper）：

```text
平台宿主 / 基础密度 + 应用缩放设置
└─ 统一的有效 LocalDensity（仅应用一次缩放）
   └─ MiuixTheme
      ├─ NavDisplay
      │  ├─ 首页 entry → 页面 Scaffold
      │  └─ 二级/更深 entry → 页面 Scaffold
      └─ 同树全局弹层入口
```

这是基于 [CompositionLocal 的祖先作用域](https://developer.android.com/develop/ui/compose/compositionlocal)与 [Compose 缩放策略](https://developer.android.com/develop/ui/compose/accessibility/scalable-content)的应用集成规则。已核对的 Miuix `NavDisplay.kt` 读取环境 `LocalDensity` 用于导航布局/转场，不负责提供应用自定义缩放。

### Diagnose before changing dimensions

在同一设备、窗口配置、应用缩放和系统字体设置下，记录首页与出问题页面入口的 `LocalDensity.density`、`fontScale`，追踪最近的 provider，并比较同一组件角色的 dp 尺寸、文字 token 和测量后的 px：

| 观察 | 优先检查 |
|---|---|
| 文字、图标、控件、间距同比例变化 | LocalDensity 作用域、重复倍率、整页绘制变换 |
| 仅文字变大 | fontScale、TextStyles/局部文字样式、手写 sp；不要改整页 density |
| density/fontScale 相同，局部控件仍偏大 | 页面是否使用不同 Defaults、组件类型、最小高度、内容内边距或主题覆盖 |
| 仅底部空白/末项区域变大 | Scaffold padding、IME/navigation inset 和尾部 Spacer 是否重复，见 [安全区指南](page-layout-and-insets.md) |

修复后覆盖首页 → 二级 → 更深页面 → 返回、直接深链/恢复、弹窗/Sheet、系统显示大小与大字体、应用缩放切换。对同角色控件检查尺寸一致性，同时保留页面本身合理的标题层级差异。

## Quick Start

**Compose Multiplatform project** (most common — Android, iOS, Desktop, Web):

```kotlin
// settings.gradle.kts — ensure mavenCentral() is in dependencyResolutionManagement.repositories
// module build.gradle.kts
kotlin {
    sourceSets {
        commonMain.dependencies {
            implementation("top.yukonga.miuix.kmp:miuix-ui:0.9.4")
            implementation("top.yukonga.miuix.kmp:miuix-preference:0.9.4") // optional
            implementation("top.yukonga.miuix.kmp:miuix-icons:0.9.4") // optional
            implementation("top.yukonga.miuix.kmp:miuix-blur:0.9.4") // optional; Android target requires API 33
            implementation("top.yukonga.miuix.kmp:miuix-nav:0.9.4") // optional
        }
    }
}
```

**Pure Android project:**

```kotlin
// module build.gradle.kts — ensure mavenCentral() is configured
android {
    defaultConfig {
        minSdk = 24
    }
}

dependencies {
    implementation("top.yukonga.miuix.kmp:miuix-ui-android:0.9.4")
    implementation("top.yukonga.miuix.kmp:miuix-preference-android:0.9.4") // optional
    implementation("top.yukonga.miuix.kmp:miuix-icons-android:0.9.4") // optional
    implementation("top.yukonga.miuix.kmp:miuix-nav-android:0.9.4") // optional
    // miuix-blur-android itself requires minSdk 33; add it only when that target is acceptable.
}
```

`miuix-ui`, `miuix-preference`, `miuix-icons`, and `miuix-nav` follow v0.9.4's Android baseline of `minSdk = 24`; `miuix-blur-android` is a separate API-33 target. Keep the dependency artifact aligned with the source set: use the common artifact in `commonMain` and the `-android` artifact for a pure Android module. Verify the target project's Compose/Kotlin/AGP compatibility before changing its build versions.

## MiuixTheme Setup

Use `ThemeController` for runtime system dark mode and dynamic-color behavior. Do not choose `lightColorScheme()` merely for convenience, because that fixes the app to a light scheme; use a fixed `lightColorScheme()`/`darkColorScheme()` pair only for an intentional fixed palette, a light-only product, or deterministic preview/test content.

```kotlin
@Composable
fun App() {
    val controller = remember { ThemeController(ColorSchemeMode.System) }
    MiuixTheme(controller = controller) {
        Scaffold(
            topBar = { /* TopAppBar, NavigationBar, etc. */ },
            bottomBar = { /* NavigationBar, TabRow, etc. */ },
            floatingActionButton = { /* FloatingActionButton */ },
            floatingToolbar = { /* FloatingToolbar */ },
            floatingToolbarPosition = ToolbarPosition.BottomCenter,
        ) { innerPadding ->
            // Apply innerPadding to the content root or scrolling content.
        }
    }
}
```

`ColorSchemeMode` options: `System` / `Light` / `Dark` / `MonetSystem` / `MonetLight` / `MonetDark`.

> **Version pinning**: Current file paths and component mappings reflect tag `v0.9.4` at commit `39c40f99`. This is a stable release verified on 2026-11-08. For other versions, resolve the exact dependency tag/commit using [Source verification](source-verification.md).

## Android Studio Preview

Version 0.9.4 repository does not provide a first-party `@Preview` sample, so treat Preview as target-tooling integration rather than a guaranteed Miuix runtime environment.

- Use the target source set's normal Preview annotation/dependency and render a page/component with fake deterministic state and no real services.
- Wrap preview content in one `MiuixTheme` with a deterministic Light or Dark `ThemeController`; avoid System/Monet inputs when the preview host cannot supply real platform theme data.
- Add `Scaffold` when previewing an Overlay component because it needs the popup host. Window popups, system back, focus/IME, animation timing, dynamic color, and platform windows still require runtime validation.
- Preview cannot prove shader or blur behavior. Keep the unsupported fallback renderable and validate those effects on an API/platform that supports them.

## Continue With

- For application root, page shell, Scaffold padding, and state patterns, read [UI composition patterns](usage-patterns.md).
- For semantic colors, typography, grouping, and component Defaults, read [Design language and Defaults](design-language.md).
- For exact `MiuixTheme`, `ThemeController`, and Scaffold source paths, use [Source verification](source-verification.md).
- Before returning code, satisfy the [Code Delivery Contract](../SKILL.md#code-delivery-contract).
