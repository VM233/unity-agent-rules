# Unity UI Toolkit USS 结构、生成控件与状态级联细则

## 共享范围与权限边界

- 修改 UI Toolkit/UXML/USS、自定义 `VisualElement`、生成子元素蒙皮或伪状态级联时读取本文件；项目布局、素材与控件实现细则仍由消费项目维护。
- 验证范围按共享 Editor 安全细则与本轮 UI 改动风险选择；必要的 authoring 审查、运行时、视觉和输入验证直接执行，不另行要求用户点名工具或授权。

## 全部手游 UI 的安全区域

- 手机安全区是所有 UI 的布局与输入约束，不限 toast 或某个页面。主菜单、加载/错误页、存档、角色创建、设置、HUD、导航、弹窗、下拉层、Tooltip、长按详情、教程、通知和运行时生成内容，都必须避开灵动岛、刘海、挖孔、圆角、状态栏、系统导航及底部手势区；关键画面、可读文字和完整命中区域均须可见、可达。只有不承载关键内容和输入的背景、遮罩与装饰可延伸至整屏边缘。
- 从当前系统/宿主的四边实际安全区推导可用区域，正确转换屏幕、panel/Canvas 与父容器坐标及缩放。统一复用安全区 owner，覆盖独立文档和动态覆盖层；嵌套面板与已经由宿主留白的画布不得重复扣减。禁止以固定顶部偏移、百分比空白、设备型号特判或把重要 UI 移进另一遮挡区代替安全区适配。
- 横竖屏、左右横屏、窗口/分辨率、系统栏及安全区变化后重新布局；关闭重开、换根与退役须闭合监听/调度，不能沿用旧方向的 inset。过渡的起点、中间帧、终点及拖动/滑动中的显示和命中同样受安全区约束，不能只让静止终态避开遮挡；直接手势和安全几何更新保持即时。
- 原生和手机 H5/小游戏分别核对实际宿主。网页开启 viewport-fit=cover 或全屏铺边时，须消费浏览器/宿主真实的四边安全区，并覆盖 Unity 画布出现前的下载、加载与错误 UI；不能以 Editor 或浏览器返回全屏矩形证明真机无遮挡。完整保留宿主已提供的安全留白，避免重复 inset。
- 制作与验收逐项覆盖全部实际 host、设计期预览、支持语言、竖屏及左右横屏，包含有/无切口、底部手势条、缩放和安全区变化、长内容/滚动末端、弹层打开与退役、连续切换和手势过程。对比可见区域、文字、按钮完整命中区域及过渡帧，证明没有漏接或重复留白；静态图、编译、几何报告与 Simulator 分别标明证据边界，未经真机验证不得声称已通过真机验收。
- 依据：[Apple Layout](https://developer.apple.com/design/human-interface-guidelines/layout)、[Unity Screen.safeArea](https://docs.unity3d.com/6000.0/Documentation/ScriptReference/Screen-safeArea.html)、[WebKit safe areas](https://webkit.org/blog/7929/designing-websites-for-iphone-x/)。系统安全区用于避免遮挡，界面自身的正常边距仍由布局 owner 表达。

## 手游视图手势

- 手游可旋转预览直接单指拖动旋转，可缩放视图直接双指捏合缩放；地图等视图保留平移及其实际任务语义，不设置冗余旋转或放大缩小按钮。审查所有适用入口并复用实际输入、捕获与生命周期 owner，不能只删除截图点名的控件。
- 删除入口时同步清理失去 consumer 的绑定、路径、序列化字段、模板、设计期预览、文案与专属资源；保留共享资源、真实独立诊断用途和改变游戏对象状态的独立操作。

## 浮动窗口与桌面指针

- 浮动面板复用已有窗口放置与拖动 owner；打开时按完整可见 presentation bounds 放入单个显示器工作区，拖动手柄使用公共指针捕获组件。不得为单一页面复制拖动回调、坐标状态或原生输入名单。
- 透明桌面窗口的原生命中由当前实际呈现的 UI Toolkit panel、原生 picking 与鼠标 capture 决定，覆盖非唯一实例、弹出内容和关闭动画。viewport carrier 不承担可点击区域；实际 UI 与世界画面的交互区域分别由其 owner 提供，不维护第二份 UI 矩形白名单。
- 正常内容审查必须组合窗口交互审查，覆盖角色声明、放置 owner、presentation 路径、可命中手柄与共享拖动接线，并提供能拒绝缺失 owner/手柄的反例。输入代码变更还要验证重开、隐藏、root 重建、取消/丢失捕获及释放后的状态；静态配置审查不能证明原生点击没有穿透到其他进程。
- 涉及原生透明窗口时，用真实 Player 验证 UI 上的点击接收方及 UI 外的预期穿透，保留正式 CLI 输入/系统状态与游戏结果证据。内部事件派发、截图或一次几何检查不能代替该验收；本地构建与验证不自动授权上传包体。

## UXML 与 USS 审查入口

- 修改 USS 后，通过官方 Unity CLI/Pipeline facade 调用 `uitoolkit/audit-uss-styles`；修改 UXML 后调用 `uitoolkit/audit-uxml-layout`。`paths` 必须是本轮实际修改的精确文件列表，`roots` 与 `runtimeSourceRoots` 必须覆盖这些文件的完整 authoring/consumer 图，并设置 `runSelfTests=true`；静态样式、布局、selector、声明所有权和字面内容检查项只由 route 维护，不在 Agent 规则中复制。
- 只有对应响应同时满足 `passed=true`、`truncated=false`、`errors` 为空且 `selfTests.passed=true`，才算自动审查通过。结构化 issue、suppression 契约与 rule ID 以 route 当前 schema 和结果为唯一权威；生成控件层级、运行时主题、实际视觉和输入行为仍按下节与消费项目细则验证。

## 即时结果与悬浮反馈

- 食用后的饱食度/水分增量、获得/消耗、成功/失败等短时操作结果复用项目已有 toast/通知 owner，显示在上方安全区域的独立覆盖层；可以为不同结果组合内容，不另造容器、计时、动画、队列或手势生命周期。
- 此类反馈不得参与正文布局，也不得插入占位、预留高度、修改正文 padding/margin、滚动位置或推挤标题和按钮。不能为避免遮挡改用布局占位；应由共用悬浮反馈 owner 处理位置、优先级、替换与退役。
- 操作前执行条件和真实持续状态仍由对应页面展示，同一即时结果只保留一个反馈出口。以真实操作比较反馈前、中、后的正文几何与滚动位置，并验证连续操作、关闭/重开、滑动取消、优先级接管及各语言/视口；覆盖层外的输入继续可用。

## UI 状态过渡

- 凡有 UI 切换，制作与审查都必须明确检查是否有过渡；除必须立即反馈的交互外，一律快速自然过渡，禁止生硬瞬变。范围涵盖全部平台的导航、显隐、页签、内容、选择表现、预览取景和返回，不限聚焦或手游。按压、选中确认、输入回显和直接手势等即时反馈不得被动画延迟。
- 整页导航和局部显隐、选择、内容替换都属于过渡审查。先从实际 producer、调用关系、序列化配置和 UIDocument host 推导可达状态与边；状态表、关系图、全量/局部审查及可视化消费同一产品。直接显示、层级和影响显隐的 class 变化必须可追踪，新增未覆盖变化保留为失败缺口，不能靠手工登记完整性。
- 复用统一状态与表现 owner，以当前实际显示值作为重定向起点，保证快速连点、反转、取消、拖动/缩放介入及关闭重开不会跳回旧起点，选中反馈与真实目标一致。纯展示过渡使用渲染帧非缩放时间；关闭、换根和退役成对结束调度、等待、回调及捕获。
- 实际文档宿主须包含 authored 内容及运行时覆盖层。区分初次展示前初始化与已显示内容变化；相关标题、内容、动作和输入身份由一个 publication owner 一起发布。退场内容在其交接边界前保留真实画面，不能先清空再依靠父节点淡入掩盖局部跳变。
- 每种过渡沿真实控件留下当前源/build、语言、视口、原状态、自然中间渲染帧、准确目标和终态证据；检查输入、焦点/捕获、返回、重入、反转、取消、root 更换及退役。关闭与释放须成对结束动画、异步等待、订阅及图片等资源。工具回执、时长、phase 数值和终态截图不代替画面验收。
- 源码覆盖、宿主接线、运行时、画面及输入分别给出结论和精确缺口。局部审查纳入相邻 producer、consumer、调用及实际 host，只能签署该范围。失败按代码质量细则冻结首个非法状态；已有反例与停止条件须跨工具、方法变体和交接保留。

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
