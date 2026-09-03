# Unity Prefab、组件与序列化通用细则

## 共享范围与权限边界

- 检查或修改 Game Prefab、组件宿主、职责边界、组件复用、配置所有权、Unity/Odin 序列化、Prefab/Scene 接线或反序列化读回时必须读取本文件。
- 用户当前要求和消费项目根 `AGENTS.md` 决定写入及验证权限；涉及 `Assets` 或 Unity 读回时同时读取 `.agents/shared-rules/instructions/unity-editor-safety.md`，涉及代码时读取 `.agents/shared-rules/instructions/code-quality.md`。
- 项目具体 Wrapper、Prefab 模板路径、Common Prefab、GameTag、Owner 链、克隆标签和产品工作流留在项目专项细则。

## Prefab 层级与组件职责

- 编辑 Prefab 前先检查现有层级和同类实例。Prefab root 只承载身份、生命周期、框架入口及真正属于整体的职责；不得因引用方便把业务、控制、同步、渲染、碰撞、容器、触发器或动画组件堆在 root。
- 先把组件归类为基础设施、数据流、控制/交互、属性/容器、渲染、物理/Collider、动画、Trigger 或功能辅助，再放入语义匹配的既有职责节点；无匹配节点时创建职责明确的英文子节点。
- 优先复用项目已有的 `Controls`、`Properties`、`Renderers`、`Colliders`、`Containers`、`Triggers`、`Animations` 等职责容器；这些容器默认只负责分组，组件应放在匹配子节点，只有管理整个容器时才直接挂载。
- 新增组件或接线前搜索同职责脚本、Prefab 配置和通用管线。能组合或小幅泛化复用时，迁移全部调用方并删除重复状态、逻辑、订阅和接线；不得保留新旧两套路径。
- 交付前检查受影响 Prefab/Variant 的完整层级，确认组件没有污染 root、结构节点或无关职责节点，并同步更新所有 producer、consumer 和序列化配置。

## 配置与数据所有权

- 易迭代的距离、速度、时长、权重、概率、阈值、数量和策略应序列化在实际行为 owner 的 Prefab/配置上，并允许同类 Variant 在需要时独立覆写；常量和全局设置只表达真实不变量。
- 跨组件协作通过语义事件、窄接口、属性或权威数据产品完成；consumer 不得搜索、强转或订阅具体 producer 的内部实现，也不得根据目录、名称、层级或其他职责域状态推断资格。
- 已提交 Prefab、配置和本地化 Asset 是生产数据权威。只为初始化、迁移、修复或批量物化而创建的一次性 builder/upsert/菜单代码，必须在同一任务完成迁移、按授权读回并删除；不得保留可用代码常量整体重建或覆盖生产 Asset 的双重权威。
- 真正稳定、可复用且可定义 typed schema 的通用 Unity/框架操作进入对应 Automation catalog 与 Pipeline command；反复发生且拥有独立产品语义的项目编排可成为 project tool。工具不得复制已提交 Asset 中的玩法配置，也不得按旧 builder 方法机械拆 command。

## 序列化契约

- 向 Unity 可序列化类新增字段或自动属性前，检查完整基类链和接口并复用同名同责成员。即使 C# 允许隐藏，也禁止重声明同名继承自动属性，避免重复 backing field 触发 `The same field name is serialized multiple times`。
- 合法域只由行为/配置 owner 的类型与权威数据定义一次。Inspector/Odin 的 `Range`、`Min`、选择器 Attribute 或 Shader range 只能把该域暴露给作者；不得再用 `OnValidate`、运行时 clamp、默认替换或 consumer guard 修正非法序列化值。非法值应在权威导入、构建或契约测试边界直接暴露并修正生产数据。
- 合法值来自 Project Settings、Prefab/配置注册表、Preset 或其他权威集合时，使用语义匹配的强类型引用或选择器 Attribute/Drawer，使 authoring producer 只能生成合法值；不得暴露裸字符串/整数后再由各 consumer 判空、校验或复制候选列表。
- Unity/Odin 正常保存产生的字段/条目顺序、空白、缩进、默认空字段、无语义空值和不改变对象图的 managed-reference ID 变化，只做一次快速语义判断；确认不改变引用、有效值、类型、Prefab override、组件/层级或反序列化对象图后保持结果，不得为缩小 diff 反复 trim、restore、重存、格式化或重建。
- YAML 看似合理不能证明 Unity 对象引用和序列化类型有效。任务实际改变对象图、引用/GUID/local file ID、字段契约或已有证据指向反序列化故障时，通过 Unity 实际反序列化读回，不另行询问验证授权；受用户明确限制或环境阻碍未读回时准确说明原因，不得把静态检查写成运行时证明。

## GamePrefab 最终审查门

- 任务只要新增、删除或修改 VMFramework GamePrefab、其 Wrapper、Prefab 引用或注册关系，就在全部写入、导入、编译及已授权读回完成后，通过官方 Unity CLI 的 bounded catalog 查询 `validate-game-prefabs`，取得当前精确 contract 后保持全局默认审查范围调用。当前 command 是 `vm_pt_vmf_validate_game_prefabs`，其 route 是 `vmframework/validate-game-prefabs`；route 不得当作 `vm_catalog_get` 或 `vm_automation_call` 的 command。不得用项目专用脚本、局部 selector、Inspector 外观或启动成功替代。
- 最终检查必须同时扫描所有可发现 Wrapper，并把其中每个精确 GamePrefab 配置对象与 `GlobalSettingCollector -> IGamePrefabsProvider` 的运行时注册图比对；只统计磁盘 Wrapper、只验证 Prefab 引用或只按相同 ID 推断可达都不构成注册证明。新增 Wrapper 必须接入实际运行时 provider owner，不能因 Asset 存在而视为已注册。
- 这一步是交付、提交或推送前最后一个 GamePrefab 内容验证；后续若再改变任何 GamePrefab 内容、Wrapper、provider 或引用，原结果立即失效并必须重跑。只有外层传输成功、内层 owner `ok=true`/`status=completed`，且结果 `passed=true`、`errorCount=0`、`missingPrefabCount=0`、`unregisteredGamePrefabCount=0`、`registeredGamePrefabCount=gamePrefabCount` 才算通过；字段缺失、返回被截断、只查看日志或旧版检查器均不是通过证据。
- 若消费项目尚未提供该命令，先在 `VMFramework-Pipeline` 权威 package 完成发布并采用远端不可变 revision；不得把缺失能力下沉为项目审查器或保留双轨。
