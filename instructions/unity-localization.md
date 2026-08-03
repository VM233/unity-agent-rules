# Unity 本地化通用细则

## 共享范围与权限边界

- 新增或修改 Unity Localization String Table/Shared Data/locale value、`LocalizedString`、SmartFormat metadata/selector、描述变量、玩家可见名称/描述、富文本或 UI 文本绑定时必须读取本文件。
- 用户当前明确要求和消费项目根 `AGENTS.md` 决定写入与验证权限；涉及 Unity 操作时读取 `.agents/shared-rules/instructions/unity-editor-safety.md`。项目具体 Locale、Policy、例外表、富文本颜色、link/sprite 家族和业务对象分类留在项目专项细则。

## 展示资格与数据权威

- 是否需要本地化以真实玩家可见 consumer 和产品/UI 角色为准，不以类型继承、字段存在、tag、名称、目录、Prefab 层级、图标或工具支持本地化为准。
- 纯系统、聚合、代理、辅助、模板注入、状态记账或抽象配置如果不会被玩家直接查看，必须保持玩家名称/描述为空或禁用，不得创建、复用或保留本地化 key、表项和占位文案。内部对象意外进入 UI 时修正展示资格 producer/consumer，不用补文案把错误暴露合法化。
- 内部配置需要在 Editor、诊断或日志中辨认时使用英文资源名、对象名或稳定 ID；不得为编辑器可读性反向创建玩家本地化。
- 玩家可见动态值来自 Prefab、Property、Ability、配置或其他行为 owner 时，文案通过该 owner 的描述变量/参数 provider 读取权威值；禁止把当前配置复制为百分比、时长、数量等字面量。只有语义恒定且不对应可调数据的叙事数字可直接写入。

## Locale、文案家族与富文本

- 实际玩家可见内容必须在每个受支持 Locale 中提供对应目标语言的完整文本。创建、修改或验收时读取各 Locale 实际值逐项核对，不能只看 Shared Data key、引用、非空或缺失计数。
- 源语言与目标语言完全相同不能单独证明翻译完成；必须核对它确属有意跨语言同文的专名、缩写或符号，并遵守项目本地的例外登记或校验政策，不得用静默复制或占位绕过。
- 修改条目前检查 consumer 最近的同语义文案家族及所有支持语言，复用其数值格式、强调、`<link>`、`<sprite>`、换行、占位符和标签嵌套语义；跨项目写法不同时以当前项目和 consumer 家族为准。
- 各 Locale 必须拥有等价占位符集合和富文本语义，标签成对并正确嵌套；语序、复数、单位和标点按语言分别处理，不要求逐字结构一致。

## 静态、动态文本与条目生命周期

- 静态 UI 文本优先使用项目既有 UXML/`LocalizedString` binding，并保留有意义的 Editor/UI Builder fallback；除非现有 binding 无法表达，不得只为静态本地化新增运行时组件或 `PanelModifier` 赋值。
- Button、Tab、列表项、bind label、slot caption 等运行时生成文本必须响应项目既有语言变更接口/事件，不能只在创建或面板打开时赋值。
- 从脚本迁移静态显示文本时只删除 display assignment；搜索、筛选、排序、Tooltip 或其他行为仍需的本地化查询必须保留。
- 新建 key 前搜索相同语义条目并复用。删除或通用化旧 key 前必须全项目检查 UXML binding、脚本、Prefab、table、string-key 和运行时 consumer；确认无消费者后同步处理 Shared Data 与所有 Locale 值，不得留下孤儿或误删共享条目。
- 含 selector/占位符的条目必须启用匹配 metadata，并从 `LocalizedString`/描述组件核对到参数 provider；每个 selector 都由权威 owner 完整供数。新增 selector、改变 table/entry 引用或重组标签语义时，只有在项目允许的范围内通过实际生成验证；未获授权时明确标记未运行。
