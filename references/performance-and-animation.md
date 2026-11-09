# Lists, State, and Animation Performance

用于长列表、分组展开、尺寸变化、连续绘制与后台工作。来源见 [Mishka 实践映射](mishka-patterns.md)；Compose 通用行为以文末官方文档为准。先定位 composition/layout/draw 或数据转换的开销，不将性能配方套到所有页面。

## Lazy boundaries and grouped cards

小而固定的设置组保留 `item { Card { ... } }` 即可。行数大、动态展开或 profiling 显示整组组合成本高时，让每一行成为独立 lazy item，而视觉上仍拼成一张 Card：

- 使用稳定且全局唯一的业务 key；首段仅圆顶角、末段仅圆底角，单行同时圆上下角，中段矩形无内部间隔。
- 圆角段用 `squircleSurface` 裁住子项按压高亮；中段可以直接 background。同步 Card 的容器与内容色，不漏 `LocalContentColor`。
- Preference 自带内边距，避免段容器重复加；只在整组外围应用原有 margin。拆段后复核语义遍历和焦点顺序，不能仅凭外观相同认为行为相同。
- 空组不产生装饰 item；条件行先形成带稳定身份的行模型。静态文本卡、hero/视差组合不为“lazy 化”强行拆分。

`groupedCardItems`、`CardItem`、`CardSegment` 是 Mishka 应用 helper，Miuix 没有这些 API。优先复用目标项目等价实现，否则按需求提供应用自有实现。不要未经说明复制上游源码。

## Stable identity during expansion and morphing

- 需要跨离屏/删除/重建保留的展开状态由屏幕所有者管理，按组 ID 索引；按恢复需求决定 saveable 表示。不要依赖被移除 item 内的状态永久存在。
- 普通插入/重排使用稳定 key 与 `animateItem` placement。若需要连续“一行两列 ↔ 一行一列”morph，行 key 要持续指向同一内容；直接更改分块数量可能让同 key 对应不同节点，无法产生期望的几何过渡。
- 可将同一批项目保留在一个行 Layout 内插值宽度/位置/高度；这是有明确动画需求时的实现选项，不要求所有列表固定 `chunked(2)`。
- 连续收起时先保留退场行，动画完成后移除；按完整尺寸测量并裁剪可见高度，避免把文字压扁。行内空白也应随可见高度消失。
- **placement 的条件不同**：普通插入/重排保留 placement；自定义高度收缩或 morph 已在每帧改变布局时，可暂时关闭 placement，避免两套动画叠加追赶。结束后恢复。不要提炼为“总要开”或“总要关”。
- 快速反向点击先取消/接管旧 job，旧 job 不得清理新动画的状态。目标圆角由目标展开状态驱动，而不是等待退场行清空。
- `remember` 缓存排序、过滤、分块时列出真正输入，包括会影响排序的动态数据；用巨大 `uiState` 作 key 会无谓失效，漏数据版本则会显示旧结果。缓存不能让实际改变的排序依赖停止更新。

## Read state in the phase that needs it

布局/绘制专用的帧级进度透传 `State<T>` 或 `() -> T`：位置在 placement、尺寸在 measure、变换在 `graphicsLayer {}`、绘制在 draw 阶段读。`Modifier.padding(animatedValue)` 之前解包仍发生在 composition；改用可延迟读取的合适 modifier/自定义 layout。

`derivedStateOf` 只有输出变化少于输入时才有降频意义。每帧生成不同的 Float/Dp 再在 composition 中读取，并不会消除每帧重组。影响实际 UI 结构的状态仍应在 composition 消费，不为追求“零重组”破坏正确性。[Compose phases](https://developer.android.com/develop/ui/compose/phases)

## Lifecycle, collections, and callback freshness

Android 屏幕 Flow 通常用 `collectAsStateWithLifecycle()`。共享或其他平台代码先确认 lifecycle owner 和依赖支持；没有该集成时选平台合适的收集方式。上游是否随之停工还取决于 Flow 的共享策略。[State and Jetpack Compose](https://developer.android.com/develop/ui/compose/state)

Strong skipping 下不稳定参数也可能按实例相等跳过重组；不能断言普通 `List` 必然不可 skip。先检查编译器模式、对象复用和 trace。`@Immutable` 仅标注真正不可变对象；持久集合应按数据与比较成本选用，避免为注解而转换巨量数据，或在可变对象上虚假承诺。[Strong skipping](https://developer.android.com/develop/ui/compose/performance/stability/strongskipping)

长生命周期缓存若需要最新回调而不重建，用 `rememberUpdatedState`。集合内容/选中状态仍是正确的缓存依赖；不要仅为减少 lambda 变化把全部 `remember` key 删除。

## Continuous sampling and invisible effects

- 连续图表用单调样本序号区分相同值的新采样，并把进度表达为绝对位置；反复 `snapTo(0)` 再前进可能在新数据与 effect 启动之间闪跳。
- 处理首样本、历史重置、突发到达和跳号；决定插值、裁剪或跳到最新窗口。Mishka 的 1100 ms/1 Hz 与 `capacity - 2` 是其缓存窗口几何，不是通用公式。
- 图表装饰层采用 `matchParentSize()` 等不参与内容测量的方式，避免把邻卡撑高；内容决定 Card 高度。Card 已有圆角裁剪时不叠加同类离屏层，运动绘图的自身矩形边界按需 `clipToBounds()`。
- 不可见的持续帧循环/色阶动画暂停工作，并保留可见后恢复路径。仅跳过绘制不代表动画协程停了。可用 lifecycle 与可见性状态控制 job，或 `snapshotFlow` 等待可见；不要每帧在整屏 composition 读取 alpha。
- Mishka 的 draw 可见性门控依赖该图层仍能观测 alpha；被完全移出 composition 的组件需要外层生命周期负责恢复，不照搬内部唤醒假设。

## Verify the claimed improvement

在代表性长数据、快速展开反转、滚动、横竖屏和后台/前台切换下复现。采用目标项目的 trace、recomposition 统计或帧时间比较；debug 卡顿和稳定版表现要区分。没有测量只能报告结构/源码层面的改进，不能承诺具体帧率或收益比例。
