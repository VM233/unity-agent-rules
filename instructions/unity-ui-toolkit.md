# Unity UI Toolkit USS 结构、生成控件与状态级联细则

## 共享范围与权限边界

- 修改 UI Toolkit/UXML/USS、自定义 `VisualElement`、生成子元素蒙皮或伪状态级联时读取本文件；项目布局、素材与控件实现细则仍由消费项目维护。
- 验证范围按共享 Editor 安全细则与本轮 UI 改动风险选择；必要的 authoring 审查、运行时、视觉和输入验证直接执行，不另行要求用户点名工具或授权。

## UXML 与 USS 自动审查

- 修改 USS 后，通过官方 Unity CLI/Pipeline facade 调用 `uitoolkit/audit-uss-styles`；修改 UXML 后调用 `uitoolkit/audit-uxml-layout`。`paths` 必须是本轮实际修改的精确文件列表，`roots` 与 `runtimeSourceRoots` 必须覆盖这些文件的完整 authoring/consumer 图，并设置 `runSelfTests=true`；静态样式、布局、selector、声明所有权和字面内容检查项只由 route 维护，不在 Agent 规则中复制。
- 只有对应响应同时满足 `passed=true`、`truncated=false`、`errors` 为空且 `selfTests.passed=true`，才算自动审查通过。结构化 issue、suppression 契约与 rule ID 以 route 当前 schema 和结果为唯一权威；生成控件层级、运行时主题、实际视觉和输入行为仍按下节与消费项目细则验证。

## 文本 authoring 所有权

- 每个 authored `Label` 都必须在 UXML 中声明可审查的文本 owner。需要随 Locale 变化的固定玩家文案使用 `UnityEngine.Localization.LocalizedString` 的 `text` binding；所有语言完全一致的固定符号或文字可直接 author。禁止保留空 `Label`，再由 `PanelModifier`、open callback 或语言切换 callback 补固定本地化文案。
- 价格、计数、运行时对象名称等真实动态值也必须在 UXML 中 author 一个能代表最终形态的非空设计期值，并紧邻该元素声明 route 公布的 reasoned runtime-text marker，写明具体 owner、替换时机与数据职责；运行时 owner 必须在页面首次展示前覆盖该值。marker 只声明运行时所有权，不能压掉空预览错误。固定文案必须使用 Localization binding，不得借 marker 规避 binding。交付前在 UI Builder 实际 host 中确认全部可见文本和图标都有预览；Play Mode 中出现不能替代这项证据。
- `Label` 只承担文本；货币、物品、属性等图标必须由独立 `VisualElement` 承担并与文本节点组合。禁止把图标写入 `Label` 的 `background-image`，再用 padding 或 background position 拼成图文控件；该结构由 UXML 自动审查直接报错。

## 运行时生成区域的设计期预览门禁

- 修改含运行时生成可见内容的页面、UXML、USS、template 或 generator 前，逐一盘点实际 host UXML 中的生成容器，并核对用户参考、同页面或同家族页面、现有 `ui-builder-preview` 标记以及负责清空和重建的运行时 owner。记录 host、容器、代表样本与清空 owner。用户明确要求预览、参考设计展示了该区域的内容，或同页面/同家族已经建立对应预览契约时，设计期预览属于本轮必做范围，禁止因运行时会生成内容而省略。
- 必做预览必须以固定、可复现的代表样本存在于 UI Builder 实际打开的 host UXML 中，并能直接看出本轮布局与状态。空容器、自闭合容器、只在 leaf template 或另一个页面放样本、只展示 Play Mode/Game View 截图，均不能证明该 host 拥有设计期预览；预览缺失、空白或未覆盖本轮新增状态时必须判定未完成。
- 生产 UXML 中的预览只可采用 `unity-ui-naming-and-assets.md` 定义的已授权 `runtime-replaced` 契约。交付前同时验证 UI Builder 中的非空预览，以及运行时 owner 在首次生成前无条件清空预览、从权威配置完整重建且最终没有重复项。生成逻辑、配置、template/container、selector、布局或状态素材变化时，必须在同一任务同步预览。

## 生成子元素的状态与主题覆盖

- 给内置或自定义控件的生成部件换肤前，先确认实际生成层级、class、伪状态 owner，以及当前引擎/运行时主题对目标属性的 winning selector；不得从外层控件类型或 UXML 层级猜测。
- 复用生成部件时，状态 selector 必须落在真正持有状态的元素上。例如把 `Toggle` 的 checkmark 当作下拉箭头时，应沿实际子链覆盖 `.custom-toggle > .unity-toggle__input:hover:enabled > .unity-toggle__checkmark`；只写外层 `Toggle:hover` 不能覆盖由内层 input 状态触发、并可能把 checkmark `background-image` 重置为 `none` 的主题规则。
- 对会重写同一生成部件的 `background-image`、tint、opacity、scale、display、尺寸或裁剪，只显式覆盖主题确实命中的 `:hover`、`:active`、`:focus`、`:checked`、`:disabled` 及必要组合，并让 selector specificity 与加载顺序足以赢过该主题规则；不得堆叠未经证据支持的状态组合。
- 运行反例否定当前 selector 时，先删除该失败规则，再按实际层级和 winning selector 修正。可复用控件皮肤及其状态覆盖归控件/variant USS 所有；项目专属素材路径留在消费项目。
