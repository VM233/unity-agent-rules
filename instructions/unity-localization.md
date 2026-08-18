# Unity 本地化通用细则

## 共享范围与权限边界

- 新增或修改 Unity Localization String Table/Shared Data/locale value、`LocalizedString`、SmartFormat metadata/selector、描述变量、玩家可见名称/描述、富文本或 UI 文本绑定时必须读取本文件。
- 用户当前明确要求和消费项目根 `AGENTS.md` 决定写入与验证权限；涉及 Unity 操作时读取 `.agents/shared-rules/instructions/unity-editor-safety.md`。项目具体 Locale、Policy、例外表、富文本颜色、link/sprite 家族和业务对象分类留在项目专项细则。

## 展示资格与数据权威

- 是否需要本地化以真实玩家可见 consumer 和产品/UI 角色为准，不以类型继承、字段存在、tag、名称、目录、Prefab 层级、图标或工具支持本地化为准。
- 纯系统、聚合、代理、辅助、模板注入、状态记账或抽象配置如果不会被玩家直接查看，必须保持玩家名称/描述为空或禁用，不得创建、复用或保留本地化 key、表项和占位文案。内部对象意外进入 UI 时修正展示资格 producer/consumer，不用补文案把错误暴露合法化。
- 内部配置需要在 Editor、诊断或日志中辨认时使用英文资源名、对象名或稳定 ID；不得为编辑器可读性反向创建玩家本地化。
- 玩家可见动态值来自 Prefab、Property、Ability、配置或其他行为 owner 时，文案通过该 owner 的描述变量/参数 provider 读取权威值；禁止把当前配置复制为百分比、时长、数量等字面量。只有语义恒定且不对应可调数据的叙事数字可直接写入。
- 玩家可见名称、描述、Tooltip、按钮、提示和状态文本的词汇权威是实际 consumer UI 及其最近的玩家文案家族。撰写前必须读取当前界面真实显示的名称与 Localization 条目，优先原样复用玩家已经能看到并识别的产品术语；不得根据代码符号、配置类型或内部数据结构自行翻译出另一套同义名称。
- 实际 consumer 已经唯一明确的主体、归属、对象类型和作用范围属于文案上下文，不得在描述中用“该能力的拥有者”“此物品”“该角色”等前缀重复陈述；描述只保留对玩家决策新增的效果、条件、目标、范围、持续时间和例外。只有省略后会产生多个可能主体或目标，或改变实际作用范围时，才显式写出主体或归属。例如能力详情写“所有投射物自动追踪敌人”，不写“该能力的拥有者发射的所有投射物自动追踪敌人”；影响友方全体的效果仍须明确“友方”范围。
- 未在玩家 UI 中直接出现的 Property、字段、枚举、派生比例、转换结果、转换器、缓存、中间状态、序列化 ID、算法阶段或其他实现概念不得泄露到玩家文案。没有既有玩家术语时，用 UI 已有的上位概念描述玩家可观察的效果；确需引入新产品概念时，先建立统一的玩家名称、交互语义、全部 Locale 条目和实际 consumer，再在其他文案中引用。

## Locale、文案家族与富文本

- 实际玩家可见内容必须在每个受支持 Locale 中提供对应目标语言的完整文本。创建、修改或验收时读取各 Locale 实际值逐项核对，不能只看 Shared Data key、引用、非空或缺失计数。
- 源语言与目标语言完全相同不能单独证明翻译完成；必须核对它确属有意跨语言同文的专名、缩写或符号，并遵守项目本地的例外登记或校验政策，不得用静默复制或占位绕过。
- 修改条目前检查 consumer 最近的同语义文案家族及所有支持语言，复用其数值格式、强调、`<link>`、`<sprite>`、换行、占位符和标签嵌套语义；跨项目写法不同时以当前项目和 consumer 家族为准。
- 各 Locale 必须拥有等价占位符集合和富文本语义，标签成对并正确嵌套；语序、复数、单位和标点按语言分别处理，不要求逐字结构一致。
- 各 Locale 还必须保持等价的信息必要性：某一语言因语法允许省略 consumer 已明确的上下文时，其他语言不得因逐字翻译重新补回冗余主体、归属或对象类型；需要显式消歧的范围信息则不能在任一语言中省略。

## 静态、动态文本与条目生命周期

- 静态 UI 文本只使用项目既有 UXML/`LocalizedString` binding 作为运行时权威，不得同时保留手写 Editor/UI Builder fallback。确需设计期压力文本时使用不进入运行时 Asset 的 Editor-only 预览并在交付前清除；除非现有 binding 无法表达，不得只为静态本地化新增运行时组件或 `PanelModifier` 赋值。
- Button、Tab、列表项、bind label、slot caption 等运行时生成文本必须响应项目既有语言变更接口/事件，不能只在创建或面板打开时赋值。
- 从脚本迁移静态显示文本时只删除 display assignment；搜索、筛选、排序、Tooltip 或其他行为仍需的本地化查询必须保留。
- 新建 key 前搜索相同语义条目并复用。删除或通用化旧 key 前必须全项目检查 UXML binding、脚本、Prefab、table、string-key 和运行时 consumer；确认无消费者后同步处理 Shared Data 与所有 Locale 值，不得留下孤儿或误删共享条目。
- 含 selector/占位符的条目必须启用匹配 metadata，并从 `LocalizedString`/描述组件核对到参数 provider；每个 selector 都由权威 owner 完整供数。新增 selector、改变 table/entry 引用或重组标签语义时，只有在项目允许的范围内通过实际生成验证；未获授权时明确标记未运行。
- SmartFormat selector 的 root 名称、大小写、连字符、分隔符和后续成员链必须与实际被选中的描述路径所注册的变量完全一致；Shared Data key、Locale 非空、Smart metadata 存在或“看起来同义”都不能证明参数链可格式化。审查必须从当前 consumer 选择的 table/entry 与 description type 出发，逐 Locale 解析格式串，并把每个 root 精确映射到该路径真实存在的参数 producer，禁止只扫描全 Prefab 任意同名字段或另一描述分支。
- 项目的一等静态 authoring 审查应使用与运行时相同的变量类型或等价 typed probe 实际格式化每个受支持 Locale，以同时暴露缺失 selector、类型/成员链和 formatter 错误；该静态审查不扩大 Play Mode 权限。只有当前任务已授权运行时验证时才执行真实 UI/描述生成，并在未授权时明确报告未运行，不能用手工目测或只检查文本非空替代静态格式契约。
