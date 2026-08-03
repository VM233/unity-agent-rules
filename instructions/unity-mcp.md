# Unity MCP 通用细则

## 共享范围与冲突顺序

- 本文件的唯一权威源是 GitHub 仓库 `VM233/unity-agent-rules`。消费项目必须通过项目内 Git submodule 固定到明确 commit，并由各自根 `AGENTS.md` 路由到 submodule 内本文件；不得在消费项目再维护正文副本，也不得依赖机器专属绝对路径。
- 修改本文件时必须在权威仓库完成审查、CHANGELOG、commit 与发布，再让所有消费项目更新到同一个 gitlink commit。项目名、项目路径、业务 Wrapper/Prefab、项目专属工作流和项目专属验证权限必须写入各自根 `AGENTS.md` 或专项细则，不得混入本文件。
- submodule 未初始化或目标文件不可读时，消费项目必须先执行 `git submodule update --init --recursive` 并失败关闭；不得跳过共享规则后继续相关任务，也不得从其他本地 checkout 临时读取替代。
- 使用或修改 Unity MCP、增改工具 route/schema、设计响应或工具元数据、诊断 MCP 故障或 workaround，以及发现工具名、description、typed schema、默认值、错误/状态文案、示例或恢复引导可能误导调用时，必须读取本文件。
- 上级指令、用户当前明确要求和当前项目根 `AGENTS.md` 始终优先。本文件只统一 MCP 的技术契约与工作方法，不自行授予测试、Unity 运行时/视觉验证、提交、推送、发布或破坏性操作权限，也不得绕过项目的小改动验证禁令及其他明确禁止。
- 同一任务同时命中 Unity 开发、package/plugin 或其他专项细则时必须全部读取并同时满足。专项规则可按项目事实收紧本文件，但不得在项目侧复制或改写通用 MCP 契约；若真实条款仍无法同时满足，在相关写入或外部操作前列出冲突并请求用户决定。
- 修改 plugin 源码、server 源码、package metadata 或消费项目固定的 Git revision 时，还必须读取当前项目的 `.agents/instructions/unity-packages-and-plugins.md`。

## 权威所有权

- 先定位产生错误或冗余的最窄权威 owner，再修 producer/contract，不得在上下游重复补偿。Node `unity-mcp-server` 负责 MCP protocol、stdio 结果封装、实例发现与绑定、工具表与懒发现、queue polling、reload recovery 及 server metadata；`VMUnityMCP` 负责 Unity HTTP bridge、Unity route/handler、Unity 侧公开响应契约、共享传输压缩及 Unity metadata；`VMFramework-MCP` 负责框架概念；项目 project tool 只负责项目专属编排和声明的 output schema。
- Unity 结果中的语义字段重复应在 VMUnityMCP 的共享传输边界统一处理；MCP JSON 是否缩进、工具发现层级和 host 侧日志裁剪属于 Node server。一个冗余只能有一个 owner，禁止 plugin 与 server 各删一遍或由每个 handler 各自维护同一压缩规则。
- 稳定、跨项目且可定义 typed schema 的能力进入通用 MCP；只属于某项目的 Wrapper、Prefab、配置、业务生命周期或验收事实留在项目 tool/专项细则。不得把项目事实硬编码进通用 plugin，也不得用项目脚本长期代替通用能力缺口。

## 消费项目版本更新验收

- 当任务只是在消费项目更新一个已经发布并具有可信验证记录的 Unity MCP revision/version 时，不重复运行 package regression、EditMode、PlayMode 或 server 全套测试。默认只验证远端 revision、`Packages/manifest.json` 与 `Packages/packages-lock.json` 一致、Package Manager 实际解析版本、消费项目导入/编译/Console，以及当前请求直接依赖的实时 route。
- 只有本轮修改了 plugin/server 源码、目标 revision 缺少可信验证，或消费项目暴露明确的集成兼容风险时，才按下文风险等级补测试。发布、commit、push、改版本号或更新 pin 本身不使测试升级。

## 响应契约与输出精简

- 公开响应默认返回“完成下一步所需的最小充分证据”，不是把内部对象原样倾倒，也不是为追求最短而删除语义。每个语义事实只保留一个 canonical 表达；能从保留字段无损、无歧义推导出的 alias、重复 summary、默认值或完成态 metadata 应省略。
- 类型与测试标识以完整稳定标识为 transport authority。`fullType`、`fullTypeName` 或 `fullName` 已无损包含短名时，省略匹配的 `type`、`component`、`typeName`、`name`、`shortName`、`simpleName` 或 fallback `title`；只有两者完全相等，或完整标识以 `.`/`+` 加短名结尾时，才视为可推导。对象/元素真实名称、display name、自定义 title、语义不同的 type，以及不一致的诊断值必须保留。
- 输入便利性与输出权威性分离。selector 可以继续接受短类型名、完整类型名或其他明确输入别名，但不得因此在输出中同时回显短名和完整名，也不得为旧调用方保留响应 alias、旧 schema 或 wrapper。
- 跨 route 的同类压缩必须实现为共享、可独立验证的纯 transport transform，并在所有适用嵌套对象上统一执行；不得只修报告中的一个 components/window/type handler。command、queue、reload 与持久 Job 的内部原始结果保持完整，跨 HTTP/MCP 边界时只压缩一次，禁止先改内部权威数据或在多层 double compact。
- project tool 已声明并验证 `outputSchema` 时，transport 必须保持该 schema 的必需字段、空容器、count、flag 和嵌套形状；需要精简时应修改 project tool producer 与 output schema，而不是在通用 transport 中静默删字段。JSON Schema 自身也不得被普通响应压缩器改写。
- 审计重复输出时必须覆盖同源字段族，而非只搜同名键：成功 envelope 中多余的 `success=true`；`message` 与 `error` 相同；`summary` 重复父级或 `progress`；`hasX` 可由集合/count 推导；完整未分页集合的 count 可由长度推导；完成页仍携带 `hasMore=false`、空 `nextOffset` 或 `truncated=false`；默认 hierarchy 字段；重复 array 维度/长度；重复 persistence/operation/project-binding metadata。删除只限于证明无损的实例。
- 零结果的主集合必须保留，避免“没有匹配项”与“字段未返回”混淆；空的可选 warning/diagnostic 容器可以省略。分页或裁剪结果必须保留 total、next offset/cursor、truncation 标志和继续获取方式，不能把 page length 当 total，也不能静默截断。
- error、warning、`obsolete`/`deprecated`、retryability、权限/能力 token、后续 polling/cancel/cleanup 所需 ID、实际目标实例信息及状态判别字段不得为了省 token 删除。若 polling 命令成功但观察到的 Job 失败作为数据返回，必须保留足以区分“poll 调用失败”和“被观察 Job 失败”的 discriminator。
- 对非无损的大体积信息使用有边界的默认视图和显式逃生口：分页/limit、`verbose`、`includeSchema(s)`、`includeStackTrace`、`maxStackFrames` 等。精简默认值必须说明缺省语义；请求完整细节时不得丢能力，发生裁剪时必须返回明确 marker。
- MCP 文本结果默认使用 compact JSON，不为机器响应重复缩进；pretty 模式只作为显式的人类调试选项。结构去重优先于空白压缩，不能用不可解析的拼接文本替代 typed JSON。

## 工具发现与公开面

- 优先使用命名明确、typed schema 可见的一等工具。只有具体工具缺失、过期或无法表达操作时才使用 `unity_advanced_tool`，并说明实际内部 route；稳定流程应升级为一等工具。
- 工具名、typed input schema、关键前置条件、副作用和成功判据必须在调用时直接可发现；不得要求 Agent 猜 route、枚举整个实现或读源码后才能正确调用。
- 大型工具目录采用分层懒发现：无参数只返回类别与数量，类别查询返回名称、brief、参数名与分页信息，search 返回小集合，单工具查询才返回完整 schema；只有显式 `includeSchemas` 才批量返回 schema。不得为寻找一个工具把完整 registry/schema 倾倒进上下文。
- 元数据清单必须有 limit/pagination 并翻页到底后才能宣称完整；不得把第一页、default smoke 或 `npm test` 基线误报成完整工具审计或逐工具回归。

## Plugin 源码测试分级

- 完整问题族审查用于闭合 owner、入口、公开契约、共享依赖和 consumer 范围，不等于运行完整 package suite。开始测试前，用一句话记录本逻辑批次改变了什么公开契约、是否经过共享基础设施、采用哪个测试级别及原因；级别按语义风险决定，不按文件名、diff 行数或“改了中央文件”机械决定。
- L0 静态级：只改 `AGENTS.md`、README/CHANGELOG、注释、格式、非可执行示例或不进入公开 metadata 的说明文件时，只检查目标 diff、引用、两份共享规则一致性和文档契约；禁止因此启动 Unity、编译或 package test。description、schema、默认值、错误码、route 清单或示例若会进入公开 metadata，则不属于 L0。
- L1 契约级：只改某个工具的名称、description、typed schema、默认值、结构化错误、公开示例或 registry 条目，且 handler、共享 registry 实现与生命周期语义未变时，只验证权威 metadata/schema 可解析、受影响 route 的公开清单/契约断言及必要编译；禁止因此运行整个测试程序集或完整 package suite。
- L2 单 route 级：只改一个 route/handler 及其私有协作者，未改变共享调度、传输、序列化、注册、Job 或连接生命周期时，运行准确 fixture/filter，覆盖该 route 的成功、关键失败和本次回归场景；确需真实 Unity 边界时再补一次对应 route 的实时 smoke。不得顺带运行同类别全部测试或完整 suite。
- L3 类别/共享纯变换级：改动被同一类别多个 route 共用的组件、一次迁移同类别多个 route，或修改跨类别但可证明无损、无状态、保持内部原始结果与 schema-protected 输出的纯 transport compaction 时，运行必要编译、共享变换的表驱动边界测试、受影响类别测试和少量代表性公开 route/live smoke；不自动扩大到全部 route。对 identifier alias 压缩至少覆盖“可推导时删除”和“display/custom/mismatch 时保留”两类反例。
- L4 全局级：只有改变中央 dispatcher/protocol、非无损全局序列化、error/status envelope 语义、registry/discovery/pagination 的共享实现、持久 Job 调度与取消/清理、queue/reload recovery、连接/session/auth、多 Unity 版本兼容、核心依赖，进行无法隔离的跨类别重构，或 L2/L3 证据已证明跨类别影响时，才运行完整 package suite。共享文件路径本身不是 L4 证据；满足 L3 约束的无损 transport compaction 不因位于中央响应类而升级。用户明确要求全量测试时例外。
- 测试按“静态检查 → 必要编译 → 最窄定向测试 → 类别测试 → 完整 suite”逐级升级；低级证据通过后不得只为保险继续升级。没有现成定向测试时，优先补一条最窄的公开契约回归或使用准确 filter，不能用完整 suite 代替测试定位。
- 每条 regression 必须拥有不可被更低层测试覆盖的独立风险。共享 transport/registry 契约已有表驱动测试后，删除或合并只是在每个 handler 重复断言同一机制的测试；保留 route 特有语义、失败边界和少量代表性集成样本。不得为了测试数量保留旧 alias、旧 schema、过期兼容或重复 fixture。
- VMUnityMCP 的 package-test 默认入口使用精简的 `VMUnityMCP.PackageSmoke`；`VMUnityMCP.FullRegression` 只在 L4 或用户明确要求时显式选择。smoke 必须覆盖关键契约和高风险跨层路径，full regression 仍保留为明确入口；不得把默认 smoke 的通过描述成完整 per-tool regression。
- 把连续实现、测试修正和文档同步视为一个逻辑批次。定向失败后只重跑失败项及修复直接影响的集合；L4 完整 suite 在批次稳定后最多运行一次，之后只有再次修改 L4 共享代码才失效。
- 同一 checkout 中，只要被测源码、测试和相关依赖未变，可以复用本任务内已有通过结果；不得在 plugin repo、发布后 checkout 和消费项目重复运行同一 package suite。发布和 pin 后只做 UPM 解析、消费项目导入/编译/Console 与任务所需的直接 route smoke。
- package regression 应通过公开 route metadata/响应验证公开契约，不为断言方便耦合内部 registry 或实现类型。确需直接验证内部不变量时，显式建立 test boundary 或 `InternalsVisibleTo`；运行前从实际 `.asmdef` 读取程序集名，不从 namespace、旧 schema 或旧 revision 猜测。

## 工具使用与 Plugin 开发

- MCP 的引导性缺陷与功能性缺陷同等审计。工具能经 advanced route、execute-code、直接文件操作或源码阅读绕过，不代表一等工具契约合格；若正常调用者可能据公开 metadata 选择错误 route、参数、生命周期或成功判据，必须修正最窄权威 owner，并同步可发现性、机器校验、结构化错误、示例与文档。
- 一次 Agent 误用是审计可发现性与防误用契约的信号，不自动等于 plugin 缺陷。若公开工具名、description、schema、默认值、前置校验或错误引导允许或稳定诱发该误用，按契约缺陷修复；若公开契约已明确拒绝而调用者无视它，则归为调用者误用，不得借此扩张 plugin。两种结论都必须有调用与契约证据，不能仅在最终回复主观分类。
- `unity_execute_code` 自动导入基础命名空间。项目中反复使用的扩展方法命名空间统一配置在 `Project Settings > Unity MCP > Execute Code > Additional Namespaces`；只有一次性依赖才通过请求的 `usings` 传入，不得在每次调用重复维护同一列表。
- 直接修改 Unity MCP plugin/server 时，在各自权威 checkout 工作，不把 package 转成消费项目 embedded package，不修改 `Library/PackageCache`。发布后让消费项目从远端 revision 重新解析，并用公开正式路径验证；plugin 源码开发所需测试不得反向套用于单纯 pin 更新。
- MCP 宿主启动的 stdio server 进程属于当前宿主连接；不得用 `Stop-Process`、`taskkill`、`kill` 或按 PID 年龄猜测并终止，也不得假定下一次工具调用会自动拉起。修改 server 后用独立 MCP stdio 测试客户端启动临时进程验证；让当前宿主加载新版只能使用宿主提供的 reload/reconnect，或明确告知用户重启宿主。若已出现 `Transport closed`，先审计本任务的进程操作和宿主日志，区分代理误杀、server 自身退出与 Unity bridge 故障。
- 当前任务暴露出 route/schema、状态查询、取消/清理、幂等、持久 Job、结构化响应或证据产品的已确认能力缺口时，不得在项目代码、Editor builder、菜单生成器、一次性迁移脚本、本地状态文件或 Agent 操作中保留长期绕路。按当前项目根规则的授权边界，在对应 plugin checkout 修公开契约与实现、补与风险等级匹配的回归、发布并更新消费项目 pin，再用公开工具完成原任务。
- 只为一次资产物化所需的项目编排可以临时存在，但必须在同一任务完成后删除；只有真正反复发生且拥有独立产品语义的工作流才可保留为 project tool。工具按目标对象、权限、事务和证据产品拆分，禁止把旧 builder 的方法逐个改名成 route，或在工具参数/代码常量中复制已提交 Asset 的玩法配置。
- 合并 MCP 工具前生成完整 route-to-handler 清单，逐项比较目标对象、权限 owner、前置条件、生命周期、副作用、证据产品、schema、调用方和测试。只有契约相同，差异仅为 alias、传输形态、固定参数或立即/确认完成变体时才合并；Editor UI/运行时 UI、场景实例/Prefab Asset、读取证据/修改等不同所有权域必须分开。
- 合并后只保留一个按职责命名的 canonical route，并同步删除旧 route、handler、profile、说明、schema、文档、调用方和重复测试；禁止兼容 alias/wrapper。验证公开 `_meta/tools` 包含全部 canonical route 且不含已删 route，定向测试覆盖保留的参数化变体，再按 L1-L4 决定回归范围，最后 pin 消费项目并实测 canonical route。单 route 合并/删除本身不触发 full suite。
- 修改 Unity MCP plugin 时，不保留旧工具名、请求 alias、schema、响应形态、工作流或 plugin/server 版本兼容；同步更新受控调用方并删除兼容代码。只保留 package manifest 声明的受支持 Unity 版本之间的兼容。

## Queue、Job 与 reload

- queue ticket、持久 Job 和 reload 恢复的内部状态必须保留唯一 identity、capability/access token、状态、阶段与下一步操作所需字段；输出精简不能破坏 polling、cancel、cleanup、manifest restore 或失败归因。
- 遇到 domain reload 后 ticket 丢失，只允许 metadata 明确声明为幂等的读取操作进行一次 fresh submit，并在结果中保留 replay 事实；mutation 必须失败关闭并要求先核对目标状态，禁止自动重放或因 poll 暂时失败而盲目重提。
- status 工具应通过 canonical Job API 返回同一快照，不得同时保留旧 workflow/ticket alias。终态恢复标签一旦成立不得被后续无关文件变化改写；恢复失败必须使用独立、可判别的错误/tag，而不是伪装成普通测试失败。

## 截图与证据产品

- 每种截图产品只保留一个权威 route。同一 Game View 主体若仅传输形态不同，不得并存 first-class、legacy、advanced、file 或 base64 route；调用方、重复 handler、metadata 和测试一起更新/删除。
- 运行时截图在解析相机、建 Texture、写文件或返回图片前执行统一 Play Mode 前置检查；paused Play Mode 可有效，Edit Mode、停止中或切换中返回结构化 `requires_play_mode`，不得返回 PNG、base64、占位图、缓存帧或成功形响应。
- 权威 Game View capture 默认关闭所有已开 Game View 的 Gizmos/Stats，等待净化帧，并在成功、失败、超时路径恢复各自原设置；只有 overlay 本身是证据时才显式 preserve。无法保证关闭或恢复时明确失败，不能把受遮挡图片当有效证据。
- 截图是证据产品，不是附赠文件。route 必须区分 running、paused、stopped、missing-camera、missing-window、timeout、decode failure 与成功；调用方仍要独立确认目标主体、时序和无遮挡状态。

## 故障报告与已知公开契约

- 任何 Unity MCP 问题都必须在下一次用户进度更新和最终交付中明确报告，即使备用 route、CLI 或直接文件操作最终成功。说明受影响能力、具体故障、受影响动作/证据、备用路径、权威 owner、修复与发布/pin 状态及剩余不确定性；不得静默换路后假装原 MCP 成功，也不得把 MCP 故障误报成项目缺陷。
- 若权威读回或修改依赖失败路径，在独立验证前标记为阻塞或无结论。只有源码、权限、凭据、上游控制、用户授权或更高安全边界明确阻挡时，才可保留 workaround；必须报告精确阻点，不得把问题写成已解决。
- Unity MCP 导出 route 为 `asset/export-unitypackage`，公开工具名 `unity_asset_export_unitypackage`；可见工具列表过期时可经 `unity_advanced_tool` 调用。它使用 `AssetDatabase.ExportPackage`，接受 `assetPath` / `path` / `assetPaths`，规范化 `outputPath` / `filePath`，默认包含 dependencies 与 recurse，并返回最终路径和文件大小等 metadata。
