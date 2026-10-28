# Unity CLI 与 Pipeline 通用细则

## 适用范围与优先级

- 使用或修改官方 Unity CLI、`com.unity.pipeline`、VM Unity Pipeline、Automation catalog、命令 schema、project tool、持久 Job、RuntimeOnly command，诊断连接/绑定/发现/执行故障，或清理旧传输 workaround 时，必须完整读取本文件。
- 上级指令、用户当前明确要求、消费项目根 `AGENTS.md` 和共享 `unity-editor-safety.md` 的权限边界始终优先。任务相关验证按共享 Editor 安全细则执行，不因使用 CLI 再次询问授权；消费项目业务提交与产品发布仍保持独立边界。
- 同时修改 package/plugin、Unity 代码、Prefab/序列化或项目工具时，必须一并读取对应规则；更具体规则只能收紧，不能重新授权 fallback、双轨传输或越权验证。
- 具体调用、发现、持久 Job、故障修复、能力补齐、迁移与验证操作读取已安装的 `$unity-cli-project-review` skill 及其命中参考；安装入口为 [VM233/unity-cli-project-review-skill](https://github.com/VM233/unity-cli-project-review-skill)。本文件保留共享权限、职责与工具契约，操作步骤由 skill 维护，实时参数由正式 catalog 持有。

## 唯一传输与职责边界

- Unity Technologies 官方 Unity CLI 是唯一进程边界、实例发现与项目绑定入口；`com.unity.pipeline` 是唯一 Editor 命令传输。不得新增或保留第二 server、HTTP bridge、stdio adapter、第二端口协议、直接 socket/HTTP 调用或项目脚本传输作为兼容路径。
- `VMUnityPipeline` 拥有发现、项目绑定、调用与持久任务等通用基础设施入口；可用命令与调用协议以当前官方 CLI 和已注册 contract 为准，不固定顶层命令数量或使用静态命令白名单。正式任务返回的轮询、取消和清理入口按其当前 contract 调用；业务 Automation 能力继续通过 catalog 发现和调用，不重复注册整套业务 route。
- `VMUnityAutomation` 拥有通用 Unity command 的 typed contract、handler、统一 schema/error/effect/lifecycle 元数据、请求身份、持久 Job 与执行生命周期；`VMFramework-Pipeline` 只拥有 VMFramework 领域 project tool；消费项目 project tool 只拥有项目专属编排与 output schema。跨项目一致的 GamePrefab 契约审查（包括 `IPrefabProvider.Prefab` 空引用）属于 `VMFramework-Pipeline`，不得在消费项目复制审查器或 fallback。
- RuntimeOnly command 由运行时 Pipeline manager 与项目运行时 service 拥有。Editor catalog 与 Runtime catalog 分离，运行时能力不得污染 Editor 顶层命令表，也不得借运行时入口绕过 Editor 写入和验证权限。
- 定位错误或冗余的权威 owner，并修正其 producer/contract。一个 schema、错误映射、压缩、绑定或生命周期只能有一个 owner，禁止在 facade、Automation、framework 与项目层重复补偿。

## 项目绑定契约

- 每次调用都使用按平台规范化的绝对项目根路径。项目名、Unity product name、仓库名、目录 basename、端口和窗口标题只作展示。同名 checkout 是合法常态。
- 路径正确却因名称、最近连接或手动选择失败，必须修官方/VM Pipeline 的 path binding owner；禁止改项目名、猜端口、切 cwd、选择同名实例或写全局例外绕过。
- 参数、绑定与确认以当前正式 contract 为准，不得从字段名或旧示例推测；不得替用户记录 analytics opt-in/opt-out。
- 变更性 Automation command 必须传精确 `expected_project_path`；dangerous contract 还必须传 contract 声明的显式确认参数。绑定、确认和参数错误在进入 Unity 副作用前失败。

## 发现与响应边界

- 按 skill 进行 bounded discovery，读取少量候选和当前要执行的精确 schema，不无界列完整命令表。发现限制不等于基础设施入口数量限制，不能建立静态白名单。
- 正常响应保持可解析的 compact JSON/NDJSON，结构去重优先于空白压缩；不得把内部完整 Job 状态在多层重复包裹。

## Contract、错误与生命周期

- 每个 Automation/project-tool contract 必须声明精确 input/output schema、稳定 error code、side effects、preconditions、完成证据、transaction 语义和实际 package owner。Dictionary 请求不得靠说明文字代替闭合 schema；typed request/result 由同一类型生成 schema。用户级/跨项目偏好写入必须明确标为全局持久副作用，不能只称改变视图；临时验证资源必须有唯一 owner、能力身份及成功/失败/取消/重载/退出清理契约，不能让改名字成为资源复用策略。
- 只读查询、选择已有状态、临时资源会话和永久偏好维护必须有独立契约；普通验证不得进入永久预设维护路径。具体会话、finally、reload/退出清理和读回流程由 skill 维护，权限与用户状态保全执行 Editor 安全细则。
- domain failure 返回命令结果内的 `ok=false` 与稳定错误码；CLI 连接、参数绑定、Pipeline readiness 和未预期执行异常由官方 envelope 负责。不得把 transport success 当作 domain success。
- 长操作必须返回持久 `jobId`、`jobType`、poll route 与跨 reload 所需 capability token；轮询、取消、清理由 catalog 中对应 Job contract 完成。不能用进程存活、日志尾部或固定 sleep 推断完成。
- 状态和 adoption 必须由权威 producer 提供可读回的完成证据；日志、窗口标题、缓存目录或 manifest 单点变化不能替代实际状态与采用结果。具体检查由 skill 维护。

## Package 与发布闭环

- VM Pipeline、Automation 和 framework package 只在各自权威仓库修改；不得编辑 `Library/PackageCache`，不得嵌入消费项目，也不得使用 `file:`、本地路径、symlink 或 junction。
- 当前已授权任务依赖且已证实由用户维护的 CLI/Pipeline 缺陷或缺失能力，自动进入权威修复或补齐、风险匹配验证与既有发布；当前请求所在且已授权消费项目的 pin/lock 与正式 adoption 同属闭环，不再询问是否修复、补命令、发布或接入。
- 发布权限与边界执行 `unity-packages-and-plugins.md`，操作流程执行 skill。该授权不覆盖其他消费项目、无关业务提交、产品发布、PR、第三方仓库、未授权破坏性操作或无关验证。

## 故障与能力缺口必须直接闭环

- 已授权任务遇到 CLI 问题立即归因并修复，所需 CLI 功能缺失直接补齐正式能力，再继续原任务。不得向用户重复询问要不要修、要不要新增命令或是否手工处理，也不得停在问题登记、建议、待办或“工具暂不支持”。
- 先按当前契约区分调用误用、暂时未就绪、领域拒绝与真实工具缺陷/能力缺口；误用修 caller，暂时中断保留原 Job，缺陷或缺口修唯一 owner 的 producer、注册、typed schema、错误与生命周期及受影响 consumer。不能盲改上游、重复提交变更请求或把正常领域结果当成 CLI 缺陷。
- 不得用 Computer Use、鼠标、键盘、Editor 菜单、直接 HTTP、退役 route、通用 execute-code、项目临时脚本或文件改写替代缺失/有缺陷的一等 CLI contract。只读源码与日志可以归因，不能冒充正式能力验收。
- 修复与旧传输迁移的具体步骤执行 skill；完成后删除已被替代的实现、route、配置和活动文档表面，不保留新旧双轨或临时补偿。
- 只有无法自行取得的必要源码、凭据、上游权限、不可替代环境或未授权的破坏性/产品选择才构成阻点；先完成仍可执行的归因与修复，准确报告精确缺口。进度和交付说明处理结果与证据，不把已获授权的技术处理转成用户选择题；正式修复、发布和 adoption 前不得宣称恢复。

## 验收

- 按 skill 执行项目绑定、正式请求重放、持久任务、资源清理与 adoption 验收；catalog 的有效性、owner、revision 和真实副作用以当前正式结构化结果为证据。本地 typed handler 测试不替代实际 executor/Pipeline 调用，修复或新增能力后必须继续原任务并验证其真实消费链。
- 编译与风险匹配验证执行 `unity-editor-safety.md`，不再要求用户单独点名；纯规则与文档变更只做静态校验。只有完成对应正式调用和读回后，才能报告已修复、已补齐或当前项目已采用。
