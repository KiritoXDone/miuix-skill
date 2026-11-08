# Miuix Skill

面向 Codex 等支持 `SKILL.md` 的编程助手，用于构建、优化和审查 Miuix / HyperOS 风格的 Compose Multiplatform 界面。

在现有 miuix skill 基础上，结合 [Mishka UI 规范](https://github.com/YuKongA/Mishka/blob/91e17c02d6eadbca6752ed30014660e378dc9c33/docs/ui-guidelines.md)、[Miuix 官方文档](https://compose-miuix-ui.github.io/miuix/)与对应版本源码完善。Miuix 是独立开源组件库，不是小米官方 SDK。

## 本版重点

- 精简入口与触发描述，按任务加载组件、布局、状态和性能参考。
- 明确 Scaffold、底栏、IME、横屏缺口和 BottomSheet 的安全区归属。
- 补充分段卡片、lazy key、展开/收起、布局 morph、帧级状态读取与不可见动画暂停。
- 补全长对话框、原生单选弹窗、squircle 渲染选择与 blur 降级。
- 区分 Miuix 公共 API、官方 Example 模式与 Mishka 专用 helper，不强制复制应用尺寸和业务规则。
- 修正 strong skipping 与普通集合的关系，避免虚假的 `@Immutable` 承诺。
- 以 **v0.9.4 / `39c40f99`** 为维护基线，版本证据统一记录在源码核验指南。

## 安装

Codex 全局安装到一个尚不存在的目录：

```bash
git clone https://github.com/KiritoXDone/miuix-skill.git ~/.codex/skills/miuix
```

Claude Code 可安装到 `~/.claude/skills/miuix`。其他支持该格式的工具，将仓库放在其技能发现目录，确保根部有 `SKILL.md`。

如果已经安装旧版，应更新原目录，避免两个目录同时声明 `name: miuix`。通过本仓库 Git clone 安装后，在对应目录运行 `git pull --ff-only`；已有非 Git 安装先备份再替换。必要时重新打开使用该 skill 的会话。

## 使用示例

```text
使用 $miuix 改造现有设置页，保留状态逻辑，检查组件选型、分组和安全区。
```

```text
使用 $miuix 排查长列表展开卡顿；先确认 lazy item 与动画状态的读取阶段。
```

```text
使用 $miuix 实现带键盘输入的 WindowBottomSheet，适配横屏和小窗口。
```

技能按目标项目的实际依赖版本工作，不会为匹配参考版本自动升级项目。没有编译、设备或 trace 环境时，会区分源码核对与尚未运行的验证。

## 参考结构

| 主题 | 入口 |
|---|---|
| 技能路由、边界、交付要求 | [SKILL.md](SKILL.md) |
| 实战来源与移植边界 | [mishka-patterns](references/mishka-patterns.md) |
| 页面、安全区、搜索、自适应 | [page-layout-and-insets](references/page-layout-and-insets.md) |
| 列表、状态、动画、性能 | [performance-and-animation](references/performance-and-animation.md) |
| 页面组合 / 现有 UI 审查 | [usage-patterns](references/usage-patterns.md) / [ui-review-workflow](references/ui-review-workflow.md) |
| 设计语言 / 颜色角色 | [design-language](references/design-language.md) / [color-lookup](references/color-lookup.md) |
| 基础组件 / 设置与菜单 | [component-selection](references/component-selection.md) / [preferences-and-menus](references/preferences-and-menus.md) |
| 弹窗 / 图标与效果 | [overlays-and-windows](references/overlays-and-windows.md) / [styling-icons-and-effects](references/styling-icons-and-effects.md) |
| 主题依赖 / 导航 | [setup-and-theme](references/setup-and-theme.md) / [miuix-nav](references/miuix-nav.md) |
| 版本基线与证据定位 | [source-verification](references/source-verification.md) |

## 来源与维护

原有本地 skill 的 README 指向 [limczhh/miuix-skill](https://github.com/limczhh/miuix-skill)，本仓库保留并改进其组件与源码参考。Mishka 的应用经验以固定提交引用，未随仓库附带其 helper 实现；来源、版本和归属见 [mishka-patterns](references/mishka-patterns.md)。

最后核对：2026-11-08。API 证据采用固定 tag/commit，官方滚动文档用于发现与概念说明。维护时先核对版本差异，再调整相关参考；不要只批量替换版本号。贡献涉及 API 或性能结论时，请附准确来源和相应验证。
