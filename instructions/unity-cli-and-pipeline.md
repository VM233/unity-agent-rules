# Unity CLI 与 Pipeline 通用细则

## 适用范围与优先级

- 使用或修改官方 Unity CLI、`com.unity.pipeline`、VM Unity Pipeline、Automation catalog、命令 schema、project tool、持久 Job、RuntimeOnly command，诊断连接/绑定/发现/执行故障，或清理旧传输 workaround 时，必须完整读取本文件。
- 上级指令、用户当前明确要求、消费项目根 `AGENTS.md` 和共享 `unity-editor-safety.md` 的权限边界始终优先。本文件不自行授权 Play Mode、测试、构建、视觉验证、消费项目业务提交或产品发布。
- 同时修改 package/plugin、Unity 代码、Prefab/序列化或项目工具时，必须一并读取对应规则；更具体规则只能收紧，不能重新授权 fallback、双轨传输或越权验证。

## 唯一传输与职责边界

- Unity Technologies 官方 Unity CLI 是唯一进程边界、实例发现与项目绑定入口；`com.unity.pipeline` 是唯一 Editor 命令传输。不得新增或保留第二 server、HTTP bridge、stdio adapter、第二端口协议、直接 socket/HTTP 调用或项目脚本传输作为兼容路径。
- `VMUnityPipeline` 只拥有五个稳定顶层命令：`vm_catalog_status`、`vm_catalog_list`、`vm_catalog_get`、`vm_editor_state`、`vm_automation_call`。不得把数百个 Automation route 注册成同量顶层 CLI command。
- `VMUnityAutomation` 拥有通用 Unity command 的 typed contract、handler、统一 schema/error/effect/lifecycle 元数据、请求身份、持久 Job 与执行生命周期；`VMFramework-Pipeline` 只拥有 VMFramework 领域 project tool；消费项目 project tool 只拥有项目专属编排与 output schema。
- RuntimeOnly command 由运行时 Pipeline manager 与项目运行时 service 拥有。Editor catalog 与 Runtime catalog 分离，运行时能力不得污染 Editor 顶层命令表，也不得借运行时入口绕过 Editor 写入和验证权限。
- 定位错误或冗余的权威 owner，并修正其 producer/contract。一个 schema、错误映射、压缩、绑定或生命周期只能有一个 owner，禁止在 facade、Automation、framework 与项目层重复补偿。

## 项目绑定与调用语法

- 每次调用都使用按平台规范化的绝对项目根路径。可设置 `UNITY_PROJECT_PATH`，也可把 `--project-path <absolute-path>` 放在实际声明它的官方子命令之后，例如 `unity status --project-path <absolute-path>` 或 `unity command --project-path <absolute-path> ...`；它不是可放在 `unity` 与子命令之间的全局参数。项目名、Unity product name、仓库名、目录 basename、端口和窗口标题只作展示。同名 checkout 是合法常态。
- 路径正确却因名称、最近连接或手动选择失败，必须修官方/VM Pipeline 的 path binding owner；禁止改项目名、猜端口、切 cwd、选择同名实例或写全局例外绕过。
- 官方 CLI 参数分三层：真正的全局参数位于首个子命令前；`status`、`command`、`list` 等官方子命令自己的 option 位于该子命令后、位置参数前；已注册 Editor command 的参数才位于独立的 `--` 后。完整形态例如 `unity --json command --project-path <absolute-path> vm_automation_call -- --arguments_json <json> --expected_project_path <absolute-path>`。参数名严格使用当前 `--help` 或 catalog 公布的拼写，不得由 C# 字段名、过期示例或其他命令的 option 推测。
- 非交互自动化可设置 `UNITY_NO_CONSENT_PROMPT=1`，但不得替用户记录 analytics opt-in/opt-out。不得把首次同意提示的选择混入项目配置。
- 变更性 Automation command 必须传精确 `expected_project_path`；dangerous contract 还必须传 contract 声明的显式确认参数。绑定、确认和参数错误在进入 Unity 副作用前失败。

## 渐进发现与 Token 边界

- 正常工作不得无界列出完整官方命令或 Automation catalog。先用官方 `unity command` 的小 `query`/`tag`/`limit` 查候选，再用 `vm_catalog_list` 读取最多一个小页，最后只对一个精确命令调用 `vm_catalog_get`。
- `vm_catalog_list` 默认 10、上限 50；完整 input/output schema 只由 `vm_catalog_get` 返回。Automation catalog 可以包含数百个 contract，但顶层 CLI 列表必须保持小而稳定。
- 重复调用使用一个 `unity --non-interactive --no-banner shell --protocol ndjson` 会话；每行一个带相关 ID 的请求，每行读取一个响应。不得为每个小调用重新支付进程启动和全量发现成本。
- 正常响应使用 compact JSON/NDJSON。结构去重优先于空白压缩；不得输出不可解析拼接文本，也不得把内部完整 Job 状态在多层重复包裹。

## Contract、错误与生命周期

- 每个 Automation/project-tool contract 必须声明精确 input/output schema、稳定 error code、side effects、preconditions、完成证据、transaction 语义和实际 package owner。Dictionary 请求不得靠说明文字代替闭合 schema；typed request/result 由同一类型生成 schema。
- domain failure 返回命令结果内的 `ok=false` 与稳定错误码；CLI 连接、参数绑定、Pipeline readiness 和未预期执行异常由官方 envelope 负责。不得把 transport success 当作 domain success。
- 长操作必须返回持久 `jobId`、`jobType`、poll route 与跨 reload 所需 capability token；轮询、取消、清理由 catalog 中对应 Job contract 完成。不能用进程存活、日志尾部或固定 sleep 推断完成。
- package resolve/update 的成功必须同时证明 manifest、lock、已解析 package path/hash、Asset refresh、干净编译、assembly reload 与目标 revision；只看到 PackageCache 目录或 manifest 变化不算 adoption。
- Editor 状态必须直接提供 play、pause、transition、compiling、updating、scene 和绝对 project path，不能从 UI、窗口标题或最近日志推断。

## Package 与发布闭环

- VM Pipeline、Automation 和 framework package 只在各自权威仓库修改；不得编辑 `Library/PackageCache`，不得嵌入消费项目，也不得使用 `file:`、本地路径、symlink 或 junction。
- package 修改后先在权威仓库更新版本、CHANGELOG 和文档，提交并推送不可变 revision；再让所有直接消费 package 更新最低兼容版本，最后让消费项目以完整远端 SHA 更新 manifest/lock。
- 当前任务依赖且已证实属于用户维护 package 的 CLI/Pipeline 缺陷，自动进入权威修复、风险匹配验证、既有发布、直接消费 pin/lock 和正式 adoption 闭环，不等待二次发布授权；该例外不授权消费项目无关业务提交、产品发布、PR、第三方仓库或额外运行时/视觉验证。
- 更新已发布且有可信验证记录的 revision 时，不重复 package 全套测试。默认验证远端 revision、manifest/lock/registered package 一致、消费项目权威编译，以及当前请求直接依赖的正式命令路径。

## 缺陷、迁移与禁止 fallback

- CLI command 未注册、catalog/schema 过期、命令描述会稳定误导、绑定错误、Job 假完成、RuntimeOnly 泄漏到 Editor，或官方 CLI 与实际 package revision 不一致，都是一等能力缺陷；立即停止依赖该非法前置状态的动作，修权威 owner 并完成发布/pin/adoption。
- 不得用 Computer Use、鼠标、键盘、Editor 菜单、直接 HTTP、退役 route、通用 execute-code、项目临时脚本或文件改写替代缺失/有缺陷的一等 CLI contract。只读源码与日志可以归因，不能冒充正式能力验收。
- 从旧自建传输迁移到 CLI 时必须建立完整旧 route 决策台账，逐项选择 `custom_cli`、`merge_into` 或 `delete_redundant`，并验证新 catalog、consumer rewrite 与零消费者证明。完成后删除旧 package、server、配置、进程入口、route/schema、文档示例与兼容 alias；不得长期双轨。
- 完成迁移后必须删除旧传输仓库、离线迁移台账及当前 changelog/README/Agent 规则中的旧名称；不得用历史证据名义保留活动或可发现的旧实现表面。
- 任何 CLI/Pipeline 问题都必须在下一次进度更新和最终交付中明确报告；在权威修复、发布和 adoption 前，相关证据标记为失败或无结论，不能换通道后宣称完成。

## 验收

- 先用 `unity status` 证明唯一实例与绝对项目路径，再用五命令 facade 做 bounded discovery；确认顶层命令数量没有因 Automation catalog 膨胀。
- 对 catalog 验证总 contract 数、package owner 分布、invalid contract 列表、revision 与分页上限；只拉取少量样本及本轮直接依赖 contract 的完整 schema。
- 代码或编译契约改动必须按 `unity-editor-safety.md` 通过最后一次官方 package resolve/refresh/clean compile，error 为零并单独检查 warning/obsolete。Play Mode、测试、构建与视觉证据仍需用户当前明确授权。
- 验收后扫描 manifest/lock、package metadata、项目/用户配置、宿主配置、活动进程、README 与 Agent 规则，确认旧 package/server/route/config 为零，且所有依赖均为 registry 版本或完整远端 Git SHA。
