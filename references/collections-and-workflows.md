# Collections and Workflows

用于列表、搜索、多选、排序、树形配置、导入和多阶段任务。公共组件选择见 [component-usage](component-usage.md)；项目证据集中在 [community-projects](community-projects.md)。这里的状态模型是按产品选择的组合方法，不是 Miuix 自带 ViewModel、业务模型或服务。

## State axes for a data screen

| 状态轴 | 可选表示与责任 | UI 组合 |
|---|---|---|
| 初次加载 | 尚无有效数据；加载/失败/空结果分开 | ProgressIndicator、短空态、必要重试动作 |
| 刷新 | 保留当前数据 + 正在刷新标记 + 本次结果 | PullToRefresh，列表不因刷新变成全屏 spinner |
| 查询/过滤 | 原始数据 + query/filter + 派生可见集合 | SearchBar/InputField 或表单；结果与查询身份匹配 |
| 业务选择 | 稳定 ID 集合或一个选中 ID | CheckboxPreference/RadioButtonPreference/Dropdown |
| 顺序 | 稳定 ID 的有序序列；必要时单独编辑草稿 | LazyColumn + 可选外部拖拽能力 |
| 单项任务 | ID → 当前阶段/进度/错误 | 对应行的按钮、ProgressIndicator、状态标签 |
| 页面事件 | 操作/事件身份 + 短反馈 | SnackbarHostState，由事件/协程调用 |

先明确各轴之间的关系，再选组件。一个 isLoading 不应同时决定刷新、禁用所有按钮、清空列表和关闭弹窗。依据 [NetProxy](community-projects.md#netproxy)、[Payload](community-projects.md#payload)、[Updater](community-projects.md#updater) 的组合提炼。

## Filtered selection and counts

原始数据、显示集合与选择集合分开。列表 key 用业务 ID，不能使用排序后索引；显示名称相同不代表同一个选项。

- 筛选/搜索/隐藏一类项目只改变显示集合，除非产品明确规定，否则不清除隐藏项的选择。
- “全选”明确作用于当前结果还是全量数据；标题计数的分子分母使用同一作用域。批量动作要让用户辨认实际范围，避免搜索后仍悄悄作用于无关项。
- 点击行与尾部 Checkbox 若表达同一个动作，只保留一个应用级动作入口；优先原生 CheckboxPreference，或由整行接管并让子 Checkbox 只展示状态。不同动作的父子区域则分别定义命中/焦点。
- 异步图标/缩略图通过稳定身份与缓存更新；不在每次重组遍历加载所有资源，不让某个图标失败阻断行的可操作信息。
- 空列表、空搜索结果和加载失败分别建模，提示只说用户当前需要知道的状态。

实践参考：[YumeBox](community-projects.md#yumebox)、[Lyricon](community-projects.md#lyricon)、[NetProxy](community-projects.md#netproxy)。

## Hierarchical selection and three-state Checkbox

用于配置树、分类项目或父级批量选择。展开状态与叶子选择集合独立；父级状态由有效后代叶子派生：无选中 Off，全选 On，部分选中 Indeterminate。空分组默认 Off 且不执行无效选择，不因空集合的 all 判定变成 On。

v0.9.4 的裸 `Checkbox` 公共签名是 **state: ToggleableState + onClick**，不是 checked/onCheckedChange；后者属于 CheckboxPreference。组件不会自动维护业务选择集合。下例只展示三态入口，需要祖先 MiuixTheme 与外部的组名/行语义，集合状态由调用方持有：

```kotlin
import androidx.compose.runtime.Composable
import androidx.compose.ui.state.ToggleableState
import top.yukonga.miuix.kmp.basic.Checkbox

@Composable
fun GroupSelection(
    leafIds: Set<String>,
    selectedIds: Set<String>,
    onSelectedIdsChange: (Set<String>) -> Unit,
) {
    val count = leafIds.count { it in selectedIds }
    val state = when {
        count == 0 -> ToggleableState.Off
        count == leafIds.size -> ToggleableState.On
        else -> ToggleableState.Indeterminate
    }
    Checkbox(
        state = state,
        enabled = leafIds.isNotEmpty(),
        onClick = {
            onSelectedIdsChange(
                if (state == ToggleableState.On) selectedIds - leafIds
                else selectedIds + leafIds,
            )
        },
    )
}
```

行点击可以负责展开，Checkbox 负责选择，这是两个不同动作；不要把 checkbox 点击再次转发成展开。深树按展开状态生成可见行，使用稳定节点 ID；大数据下缓存后代关系与选中计数，不每行递归扫描整棵树。隐藏/禁用叶子是否参与父选择与计数要统一。

参考 [HyperIsland](community-projects.md#hyperisland) 的叶子集合模式；其动画、缩进数值和配置 schema 不作为公共要求。明确的层级路径可用 BreadcrumbBar；任意树不是 BreadcrumbBar 自带的数据模型。

## Reorder while preserving selection

顺序和勾选状态是两个独立维度。拖拽只改变顺序，勾选只改变选择集；确认时按草稿顺序过滤选中 ID。需要“至少一项”时在提交边界检查，不以拖动中间态写回持久层。

- 拖拽库、手柄与命中策略由应用选择；Miuix 提供行、表面、状态反馈，未提供社区同名 ReorderableItem。
- 稳定 item key 贯穿源列表、lazy item、拖动状态和持久 ID。from/to 索引只对当前草稿有效；回调读最新列表，不能闭包捕获首次渲染快照。
- 拖动手柄与行点击/长按分工明确；若整行长按拖动，就不要再给同一长按分配另一业务动作。提供键盘/其他可用的移动方式以满足目标平台交互。
- 需要拖动态反馈时可使用 held，但要在结束/取消时释放；它不同于 checked。原生表面负责高亮裁剪，避免拖动层和行层同时放大/变色。
- 对外部更新定义策略：当前草稿合并、推迟刷新或提示冲突；不能在拖动中无条件用服务器列表覆盖。固定高度、禁止滚动或强制关闭 placement 都不是通用结论。

[HyperLyric](community-projects.md#hyperlyric) 的排序勾选与 [YumeBox](community-projects.md#yumebox) 的排序 Sheet 提供参考；后者 GlassBottomSheet 属于 fork，换成目标可用宿主。

## Range selection and custom grids

日程、矩阵或范围选择可能没有一一对应的 Miuix 控件，可用 Foundation 布局、输入和语义配合主题 token 实现。需要滑选时定义锚点、当前命中、手势开始时的选择基线及不可选项目，计算暂态预览集合；按产品策略在手势结束提交。

pointerInput 的生命周期 key 不应因每个选中项变化而重启当前手势；用适当的状态读取获得最新选择、禁用项和回调。命中坐标与实际布局/density 一致，重新布局后更新 bounds，保留触摸取消路径。不要把矩形网格命中计算直接当作圆角视觉裁剪。

这类组件由应用拥有；不虚构 Miuix WeekRangeSelectGrid/Calendar API。参考 [Nexio](community-projects.md#nexio) 的手势快照问题，仍需目标平台测试。

## Async actions, progress, and capabilities

典型状态可分为未准备、可操作、执行中、成功和失败；真实产品可以有更多阶段。每次操作带稳定身份，让迟到结果不会更新已经切换的对象。界面把动作交给服务/任务所有者，不在 composition 主体发起 I/O。

| 情况 | 处理 |
|---|---|
| 尚无总工作量 | 无限/不确定进度，不显示伪造百分比 |
| 可靠的已完成/总量 | 防止除零与无穷值，归一化为 0..1；单项与总体进度分别有依据 |
| 执行中重复点击 | 禁用或幂等处理同一提交；其他独立操作按依赖判断 |
| 暂无平台能力/权限 | 用实际 capability 状态决定可用性；必要时短提示与授权/恢复动作 |
| 用户关闭 Sheet | 明确是取消、隐藏或后台继续；与任务完成不是同一事件 |
| 部分失败 | 保留成功部分和失败范围；重试只覆盖目标失败项，是否可安全重试由应用服务判断 |
| 返回/旋转/重新打开 | 从状态所有者恢复显示，不靠保留一份旧 spinner Boolean |

[GhostLock](community-projects.md#ghostlock)、[InstallerX](community-projects.md#installerx)、[Nexio](community-projects.md#nexio)、[Payload](community-projects.md#payload) 提供 UI 分层/阶段示例；不将其 root、安装器、网络等系统行为转成 Miuix 默认操作。

## Import, batch apply, and confirmation

用 TextField 或平台文件选择获取输入后，先解析/校验成可预览数据，再提交。输入草稿、解析结果、选中目标和最终动作分开；失败不静默替换成默认数据。

批量修改使用明确的 patch 与目标快照；确认界面只保留必要后果和范围，按钮使用短动作名。长操作的提交、取消、重试由任务层负责，弹窗只是输入/反馈载体。多字段未编辑值不能当作清空；配置树用于选择应用范围时，父子状态与上述叶子集合契约一致。

不可逆操作需要足够辨认对象的文字，但不填需求说明。参考 [HyperIsland](community-projects.md#hyperisland) 的批量 patch、[GhostLock](community-projects.md#ghostlock) 的配置详情/确认和 [Updater](community-projects.md#updater) 的输入事件。

## Event feedback and log surfaces

Snackbar 是事件反馈，不是页面状态。一个稳定 host state 接入 Scaffold；根据事件 ID/操作身份触发 showSnackbar，避免相同文字的新事件被吞掉，也避免重组重复入队。消费发生在入队、展示或用户动作后由应用投递要求决定；不要从一个 LaunchedEffect 示例推断严格一次语义。持续错误保留页面内可重试状态。

日志/详情是持续内容：使用稳定记录 ID，限制无界增长和昂贵格式化，用户离开底部后不强制跟随追加；返回底部/恢复跟随是明确行为。复制/导出事件不要阻塞滚动或在重组时重复执行。技术细节只有最终用户实际需要排障时才展示，不能把开发跟踪文本当作默认页面说明。

参考 [NetProxy](community-projects.md#netproxy) 的事件 Snackbar 与 [GhostLock](community-projects.md#ghostlock) 的执行日志宿主。

## Validation matrix

针对实际变化验证：筛选后保留选择、全选作用域、空分组三态、重排后身份、拖动取消/外部刷新、批量部分失败、旧请求迟到、任务后台返回、重复短消息、旋转/宽窄切换、输入法与弹层返回。性能优化用代表性数据与 trace 说明，不用读源码替代运行结论。
