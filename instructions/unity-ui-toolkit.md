# Unity UI Toolkit USS 结构、生成控件与状态级联细则

## 共享范围与权限边界

- 修改 UI Toolkit/UXML/USS、自定义 `VisualElement`、生成子元素蒙皮或伪状态级联时读取本文件；项目布局、素材与控件实现细则仍由消费项目维护。
- 验证范围按共享 Editor 安全细则与本轮 UI 改动风险选择；必要的 authoring 审查、运行时、视觉和输入验证直接执行，不另行要求用户点名工具或授权。

## USS selector block

- USS 源码永久禁止使用逗号分隔的 selector list。无论声明是否完全相同，也无论 selector 是否属于同一控件的普通态、伪状态或组合状态，每个 selector 都必须拥有独立 block；不得为了减少重复而合并。
- 修改已有 grouped selector block 时，必须在同一任务把该 block 的所有 selector 拆成独立 block。该义务不授权批量改写本轮未触及的旧 USS；交付前必须静态扫描本轮新增或修改的 USS，确认其中不存在 selector list。

## 可复用声明的 class owner

- 两个或更多可独立分配的普通 simple-class selector，永久禁止各自重复同一组两个或更多 property/value 声明。共同声明必须由一个语义明确的共享 class 唯一拥有，并把该 class 分配给每个受影响的 UXML 或运行时 consumer；原 selector block 只保留各自真正不同的声明。不得通过复制声明或 grouped selector 回避 owner 提取。
- 伪状态和 relational selector 表达各自的状态或结构契约，不属于上述普通 class declaration bundle；即使声明相同也继续遵守“一 selector 一 block”，不得仅为去重把状态或结构契约抽离到失去 owner 的共享 class。
- 触及已有重复 bundle 时必须在同一任务完成共享 class 提取和全部 consumer 分配。正式 USS 审查器必须把该违例作为不可 suppression 的 error，列出全部相关 selector 与重复声明，使审查结果 `passed=false` 并单独计入 `errorCount`；warning 不满足门禁。

## Absolute overlay 与居中 owner

- `position: absolute` 只拥有脱离普通流、叠放与覆盖职责，不自动拥有坐标职责。固定尺寸 overlay 的最近语义父级已经用 `align-items: center` 与 `justify-content: center` 拥有双轴居中时，子元素保留真实叠放所需的 `position: absolute`，不得再把 `(父尺寸 - 子尺寸) / 2` 写成 `left`/`top` 重复表达同一中心关系。
- 父级对齐、absolute 叠放与边缘锚定是不同契约：贴边 badge/chrome、四边覆盖、popup 定位或有测量依据的光学/接缝修正继续使用其实际 edge/offset；不得把“overlay 可以 absolute”解释成“overlay 可以手算居中坐标”，也不得为了删除重复 `left`/`top` 连带删除仍有叠放职责的 `position: absolute`。当一般 absolute-overlay 许可与本节的居中 owner 规则同时命中时，本节只收紧重复居中坐标，二者其余职责同时有效。
- 通用 USS 审查器应只在父子尺寸与 `left`/`top` 能证明同一手算中心关系时报告，保留真实边缘锚点；有意的测量光学偏移必须提供具体理由的 suppression，不能无原因忽略。

## 生成子元素的状态与主题覆盖

- 给内置或自定义控件的生成部件换肤前，先确认实际生成层级、class、伪状态 owner，以及当前引擎/运行时主题对目标属性的 winning selector；不得从外层控件类型或 UXML 层级猜测。
- 复用生成部件时，状态 selector 必须落在真正持有状态的元素上。例如把 `Toggle` 的 checkmark 当作下拉箭头时，应沿实际子链覆盖 `.custom-toggle > .unity-toggle__input:hover:enabled > .unity-toggle__checkmark`；只写外层 `Toggle:hover` 不能覆盖由内层 input 状态触发、并可能把 checkmark `background-image` 重置为 `none` 的主题规则。
- 对会重写同一生成部件的 `background-image`、tint、opacity、scale、display、尺寸或裁剪，只显式覆盖主题确实命中的 `:hover`、`:active`、`:focus`、`:checked`、`:disabled` 及必要组合，并让 selector specificity 与加载顺序足以赢过该主题规则；不得堆叠未经证据支持的状态组合。
- 运行反例否定当前 selector 时，先删除该失败规则，再按实际层级和 winning selector 修正。可复用控件皮肤及其状态覆盖归控件/variant USS 所有；项目专属素材路径留在消费项目。
