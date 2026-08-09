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

共享代码质量基线禁止新增或扩写手写 `partial` 类型。既有手写 `partial` 必须先按聚合类型审查并提取当前受影响链上的真实职责；单文件行数或组织门禁不能通过拆成多个 `partial` 文件满足。仅编译器、代码生成器或框架硬契约可构成例外。

每个手写 C# 文件只允许一个顶层类型；属于唯一顶层 owner 的 nested class/enum/struct/interface 等可以保留。每个手写 class（含 `record class`）完整声明不得超过 1500 行，嵌套类型计入外层 class，总体职责已经分裂时必须在达到上限前提取。

写代码、实现功能、修复、重构和审查时完全摒弃 fail-close、防御性代码与 fallback。每项能力只保留一个由权威 producer 保证、consumer 直接执行的路径；契约违例直接暴露并回到上游 owner 修正，随后删除 guard、备用分支及其字段、测试和序列化状态。可推导、重复或只服务 guard/重试/恢复的状态一律删除。

共享兼容性基线默认只实现当前权威契约。除非用户在当前请求中明确点名，否则不保留旧 API、schema、数据、行为、版本或兼容层。唯一常设例外是用户维护并跨 Unity 项目复用的通用 package/plugin/Editor extension 对其权威声明支持的 Unity 版本做版本兼容；该例外不扩展到插件 API、route、server/protocol 或历史数据兼容。

共享安装迁移基线要求每个 package/plugin/MCP 逻辑入口只留下一个当前权威 checkout 或安装目录。已获授权的安装、升级、切换 revision、迁移或重装必须在创建新路径前盘点旧路径、临时目录、配置和活动进程，在新 revision 生效后于同一事务精确删除已确认被替代且可安全恢复的旧副本，并读回文件系统与引用；不得用版本后缀、备份目录或兼容性长期保留旧安装。若活动 MCP 进程仍从旧路径执行，只能先使用宿主正式 Restart/reconnect，不能强杀进程或直接删除其依赖目录。

共享缺陷治理禁止“发现首个失败后立即修改下一层”。单个非法场景应在首个非法状态结束，但同一 build 上其他独立且已授权的场景仍须完成；在整轮证据汇总前冻结可执行实现、诊断和测试语义。各场景只产出失败边界，不得直接命名根因；完整矩阵完成后统一审查 owner、producer、状态迁移与 publication/adoption/CAS，再进行一次 coherent 修复。

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
