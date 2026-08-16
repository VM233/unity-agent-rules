# 使用方法

## 新项目接入

在项目根目录执行：

```powershell
git submodule add https://github.com/VM233/unity-agent-rules.git .agents/shared-rules
git add .gitmodules .agents/shared-rules
git commit -m "Add shared Unity agent rules"
```

在项目根 `AGENTS.md` 中使用项目内相对路径进行路由：

```markdown
- 任何修改在决定验证范围前：读取 `.agents/shared-rules/instructions/unity-editor-safety.md` 的“代码改动强制编译，其他小改动默认不验证”；纯文档、规则、Git、Packages 或独立仓库任务不得因此查询 Unity。
- 新增、修改、重构或审查代码、工具和脚本：读取 `.agents/shared-rules/instructions/code-quality.md`。
- 写入 Unity `Assets`、使用 Editor、刷新/编译、Play Mode、测试、构建或视觉证据：读取 `.agents/shared-rules/instructions/unity-editor-safety.md`。
- 修改 UI Toolkit/UXML/USS、自定义 `VisualElement`、生成子元素蒙皮或伪状态级联：读取 `.agents/shared-rules/instructions/unity-ui-toolkit.md`。
- 新增、重命名、移动或删除 UI 名称、查询路径或对应视觉素材：读取 `.agents/shared-rules/instructions/unity-ui-naming-and-assets.md`。
- 修改 Prefab、组件职责、配置所有权或 Unity/Odin 序列化：读取 `.agents/shared-rules/instructions/unity-prefabs-and-serialization.md`。
- 修改 Unity package/plugin、manifest/lock、Git revision 或发布内容：读取 `.agents/shared-rules/instructions/unity-packages-and-plugins.md`。
- 修改 Unity Localization、玩家可见文案、描述变量或富文本：读取 `.agents/shared-rules/instructions/unity-localization.md`。
- 使用或修改 Unity MCP、设计响应/工具元数据、增改 route/schema：必须完整读取 `.agents/shared-rules/instructions/unity-mcp.md`。
- 若任务命中的共享文件不存在或不可读，先执行 `git submodule update --init --recursive`；读取成功前不得继续相关任务。
```

## 克隆项目

```powershell
git clone --recurse-submodules <project-repository-url>
```

克隆时未初始化 submodule，或已有 checkout：

```powershell
git submodule sync --recursive
git submodule update --init --recursive
```

## 更新共享规则版本

在消费项目根目录执行：

```powershell
git -C .agents/shared-rules fetch --tags origin
git -C .agents/shared-rules switch --detach <tag-or-commit>
git add .agents/shared-rules
git commit -m "Update shared Unity agent rules"
```

消费项目用精确 gitlink 固定版本时，submodule 处于 clean detached HEAD 是正常状态。只有在 submodule 内直接编辑共享规则时才必须先附着权威分支；不得把未提交的 detached 工作树当成已发布规则。

所有消费项目应固定到同一个 tag 或 commit。核对当前 gitlink：

```powershell
git ls-tree HEAD .agents/shared-rules
```

共享验证权限基线以 `instructions/unity-editor-safety.md` 为准。修改 Unity 项目、package 或 plugin 的代码或编译契约后，代码修改请求本身授权且要求一次最窄权威编译，并在修复错误后重新编译到 error 为零；这不授权 Play Mode、测试、Player/Content Build、运行时、视觉或打包后检查。不含代码变化的局部小改，以及只能在打包后环境确认的 UI 行为，默认只做静态审查并交由用户验证。消费项目不得用固定清单、只读 MCP 或项目专项流程扩大编译以外的验证权限。

共享发布权限基线区分消费项目业务内容与用户维护的独立 package/plugin。消费项目业务改动默认不因实现完成而自动提交、推送或发布；但当前任务一旦修改已证明由用户维护的 package/plugin/Editor extension，就必须自动完成其既有发布流程，并只把受影响消费项目的 pin/lock/安装配置随该发布提交推送，不等待二次确认，也不带入消费项目其他业务改动。

共享依赖基线永久禁止 Unity package/plugin 和以 Unity package 交付的 MCP 扩展及其消费项目使用任何本地文件系统依赖，包括 `file:`、`source: local`、绝对/相对路径、embedded override、符号链接和 junction，即使只用于临时开发或本地验证也不例外。权威包必须先发布，消费项目再固定 registry 版本或带完整 commit SHA 的远程 Git URL；每次包修改、发布或更新前后都要扫描全部可控仓库的 metadata、manifest/lock、UPM 安装配置和文档依赖示例，本地依赖未清零就不得交付或发布。

共享代码质量基线禁止新增或扩写手写 `partial` 类型。既有手写 `partial` 必须先按聚合类型审查并提取当前受影响链上的真实职责；单文件行数或组织门禁不能通过拆成多个 `partial` 文件满足。仅编译器、代码生成器或框架硬契约可构成例外。

每个手写 C# 文件只允许一个顶层类型；属于唯一顶层 owner 的 nested class/enum/struct/interface 等可以保留。每个手写 class（含 `record class`）完整声明不得超过 1500 行，嵌套类型计入外层 class，总体职责已经分裂时必须在达到上限前提取。

写代码、实现功能、修复、重构和审查时完全摒弃 fail-close、防御性代码与 fallback。每项能力只保留一个由权威 producer 保证、consumer 直接执行的路径；契约违例直接暴露并回到上游 owner 修正，随后删除 guard、备用分支及其字段、测试和序列化状态。可推导、重复或只服务 guard/重试/恢复的状态一律删除。

由 Prefab、Scene、UXML、USS、Asset 或 Inspector authoring 决定，且可能按实例、宿主、模板、皮肤或 Variant 变化的接线标识，必须序列化在实际行为 owner 的组件或显式专用配置上，由 Prefab/Asset 提供当前值；不得硬编码为常量、静态值、方法字面量或全局设置。只有类型内部自行创建和消费、对所有实例不可变且不属于外部接线面，或由框架/公开类型契约固定的标识才可使用常量，迁移后不得保留字段与旧常量双权威。

共享复用基线要求不改变 owner、生命周期和处理语义的新情况直接进入既有权威链，禁止按单个 Asset、Prefab、consumer 或内容另建专用组件、Prefab、renderer、updater、serializer 或旁路。只有可证明的领域语义、owner、生命周期、状态、权限、数据形态、交互/渲染或失败契约差异才能建立特判，并在差异结束后重新汇入公共处理。当前受影响链内经 consumer、序列化、继承、扩展契约和独立职责审计确认完全无用的组件及其专用字段、引用、节点、Asset 和测试必须直接删除；“以后也许会用”不构成保留理由，具体未来契约必须能指出 owner、consumer 或 activation 边界。该删除义务不扩大到当前未授权的无关系统，也不越过用户数据和公开契约边界。

共享缺陷治理把“证据不够”视为观测契约缺陷，而不是可以遗留的备注。Console 可提供带稳定事务 ID 的舍入摘要；权威结构化 trace 必须保存 round-trip 数值、单位、坐标系、revision/context、阶段和首个 rejection，并由报告、工具与可视化共同消费。当前范围内一旦有权威证据确认真实 Bug，应先闭合并修复该 Bug，再恢复依赖它的后续工作。

连续几何、概率、搜索和优化必须先完成变量、方程/不等式、事件边界、可行域、同源可视化、目标函数以及算法完备性与复杂度预算，再进入生产。含多轴循环、穷尽搜索、事件/几何枚举、全量扫描或逐项昂贵调用的 production、probe、Gizmo、测试和 Editor tool，在首次可执行写入前必须提交 `Static Cost Ledger`：用冻结输入数值列出每个轴及真实乘积、最坏迭代与昂贵 owner 调用总数、分配峰值和生命周期、线程/同步点、主线程连续预算、缓存 owner/失效条件及明确 pass/fail。任何未知或超预算项直接阻断实现、编译和运行；实现的循环、嵌套、线程、缓存或输入域变化会使账本失效，编译通过、平均耗时、提前退出、异步化或运行实验均不能代替静态审计。

共享兼容性基线默认只实现当前权威契约。除非用户在当前请求中明确点名，否则不保留旧 API、schema、数据、行为、版本或兼容层。唯一常设例外是用户维护并跨 Unity 项目复用的通用 package/plugin/Editor extension 对其权威声明支持的 Unity 版本做版本兼容；该例外不扩展到插件 API、route、server/protocol 或历史数据兼容。

共享 UI 命名基线把 UI 语义改名视为完整资源迁移：UXML/Prefab 名称、selector、查询路径、专用视觉素材文件名与内部 Texture/Sprite/Object 名必须在同一任务同步更新，并保留 GUID、local file ID 和全部引用。只有名称本来就准确且由多个无关 consumer 复用的中性 canonical 素材可以保持不变；不得以共享或引用仍可用为由保留误导性的旧业务名。

共享 UI Toolkit 状态基线要求先确认控件的实际生成层级、伪状态 owner 和引擎主题 winning selector。复用 checkmark、箭头等生成部件时，状态 selector 必须落到真实持有状态的生成元素，并以足够的 specificity 与加载顺序覆盖主题对同一属性的改写；不得只在外层控件写伪状态或堆叠猜测性的状态组合。

共享 USS 结构基线永久禁止逗号分隔的 selector list；普通态、伪状态和组合状态即使声明完全相同，也必须分别拥有独立 selector block。触及已有 grouped block 时必须当场全部拆分，并在交付前确认本轮新增或修改的 USS 中不存在 selector list。

共享 USS 声明 owner 基线禁止两个或更多可独立分配的普通 simple-class selector 重复同一组两个或更多 property/value 声明。共同声明必须抽到一个语义共享 class 并分配给全部 UXML 或运行时 consumer；伪状态与 relational selector 仍保持独立契约。正式审查器必须把违例作为不可 suppression 的 error，返回 `passed=false` 和独立 `errorCount`，不得降级成 warning。

共享玩家文案的词汇权威来自实际 consumer UI 和最近的玩家文案家族。名称、描述、Tooltip、按钮、提示和状态文本优先复用界面已经显示的产品术语；未直接展示的内部 Property、字段、派生比例、转换结果、中间状态、序列化 ID 或算法阶段不得进入玩家文案。没有既有术语时，使用 UI 已有的上位概念描述玩家可观察结果；真正的新产品概念必须先建立统一名称、交互语义、全部 Locale 和实际 consumer。

项目已明确授权且由运行时 owner 无条件清空重建的 `runtime-replaced` UI Builder 预览，必须以固定、可复现而多样的样本覆盖当前有限 semantic class、序列化配置、template/container、USS selector、布局样式、状态素材及有意义的组合状态；修改运行时生成链或任一表现环节时同步维护预览。该契约不自行授权生产 mock，预览也不得成为业务数据、配置来源、默认内容或 fallback。

共享安装迁移基线要求每个 package/plugin/MCP 逻辑入口只留下一个当前权威 checkout 或安装目录。已获授权的安装、升级、切换 revision、迁移或重装必须在创建新路径前盘点旧路径、临时目录、配置和活动进程，在新 revision 生效后于同一事务精确删除已确认被替代且可安全恢复的旧副本，并读回文件系统与引用；不得用版本后缀、备份目录或兼容性长期保留旧安装。若活动 MCP 进程仍从旧路径执行，只能先使用宿主正式 Restart/reconnect，不能强杀进程或直接删除其依赖目录。

共享 MCP 能力缺陷基线禁止用 Computer Use、鼠标、键盘、Editor 菜单、通用执行器、advanced route、直接 HTTP、CLI、项目脚本或文件改写代替缺失、过期或未在当前宿主注册的一等 typed tool。归因必须闭合权威 revision、安装与活动进程、catalog/激活、`tools/list_changed`、宿主 registry、direct typed invocation 和 Unity route/schema；独立标准客户端通过只能隔离 server/plugin，不能证明目标宿主已经加载新版。若目标宿主缺少正式 reconnect，必须报告需要用户重启的阻点，不能把绕行结果写成修复完成。Editor 状态工具还必须直接提供 play、pause 和 transition 的权威判别事实，不能从 UI 推断。

当前已授权任务确实依赖用户维护 MCP 缺失或有缺陷的一等能力时，能力缺陷规则与 package/plugin 自动发布规则组成一个明确的窄范围实现事务：直接修权威源码、做风险匹配的聚焦验证、执行既有发布流程、更新直接消费 pin/lock 并通过宿主正式路径激活，不因跨仓库或一般发布权限分离再次询问用户。该优先级不授权消费项目其他业务提交、产品发布、PR、第三方仓库、破坏性清理、breaking/product change，也不扩展 Play Mode、运行时、视觉或构建验证。

共享缺陷治理禁止“发现首个失败后立即修改下一层”。单个非法场景应在首个非法状态结束，但同一 build 上其他独立且已授权的场景仍须完成；在整轮证据汇总前冻结可执行实现、诊断和测试语义。各场景只产出失败边界，不得直接命名根因；完整矩阵完成后统一审查 owner、producer、状态迁移与 publication/adoption/CAS，再进行一次 coherent 修复。

已经取得具体输入、暂停帧、不可变产品或精确事务记录时，首个诊断动作必须让这一条 exact witness 沿相同生产函数逐阶段回放，形成可关联的结构化输入、输出、revision/context 与首个 rejection；完成前不得先做全 candidate/全链枚举或参数实验。单 witness 只负责定位失败边界，不能替代同一冻结 build 的独立场景、完整状态矩阵、owner/传播链闭合和竞争解释排除。

多状态或时序缺陷必须证明 predecessor/current/successor 产品及 absent、planning、ready/scheduled、pending、adopted/active、retired/cancelled 的可达性、唯一 writer、revision/context、deadline、取消与退出迁移。Comparator、排序、tie-breaker、优先级、阈值、horizon、budget、candidate/beam 数、cadence、延迟，以及新增 cursor/flag/mode/cache/pending/type，默认都不是因果修复；运行更久、失败更晚、异常换位置或访问更多候选只说明失败边界移动。被反例否决的实验必须先从生产 diff 撤回，不得与下一假设叠加。

有限或声称穷尽的搜索中，排序只拥有性能与确定性，不能拥有安全或活性。若 sibling 顺序改变最终成败，先修复进度保留、deadline/runway、publication gate 或错误的失败作用域。把原子计算改成跨 tick、异步、分帧、增量或可恢复事务，会新增正确性生命周期；必须重开完整状态证明，并在用户已授权范围内先重跑受影响正确性矩阵，不能以性能改善替代契约证据或扩大验证权限。

## 发布共享规则版本

用户明确要求新增、纠正、优化、删除或沉淀共享规则时，该请求已经授权完成整个规则迁移事务：提交并推送本仓库、创建并推送语义化版本 tag、更新所有可发现消费项目的 gitlink 与必要本地 overlay，并分别提交推送这些迁移文件。无需再次请求“是否提交/推送”；但不得暂存消费项目的其他脏改，也不得借此运行 Unity 验证、发布产品内容或创建 PR。

在本仓库完成修改后执行：

```powershell
git add <changed-files>
git commit -m "<change-summary>"
git tag -a v<major>.<minor>.<patch> -m "v<major>.<minor>.<patch>"
git push origin main
git push origin v<major>.<minor>.<patch>
```

发布后，按“更新共享规则版本”步骤更新每个消费项目的 gitlink。
