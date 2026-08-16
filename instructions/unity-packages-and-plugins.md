# Unity Package 与 Plugin 通用细则

## 共享范围与权限边界

- 修改 Unity package/plugin、独立源码仓库、manifest/lockfile、Git revision、package metadata、发布内容或远程更新流程时必须读取本文件。
- 普通消费项目的业务实现、修复、编译通过或完整闭环，不自动授权提交、推送或产品发布；消费项目根 `AGENTS.md` 与用户当前要求继续决定这些项目业务改动的发布权限。
- 一旦权威证据确认 package/plugin/Editor extension 由用户维护，且当前任务已经要求或按适用规则进入其权威内容修改，该修改本身即构成窄范围自动发布授权。无论原因是功能、修复、重构、文档、配置、metadata 或发布内容，都必须在同一任务按风险完成验证与文档/版本审阅，提交并推送权威仓库当前分支，执行该仓库既有的 tag、registry 或其他正式发布机制，并更新、提交、推送所有受本次发布影响且可控的消费端 pin/lock/安装配置；不得等待用户再次说 `push` 或确认发布。
- 共享 MCP 细则确认“当前已授权任务实际依赖用户维护 plugin/server 所缺失或有缺陷的一等能力”时，已经属于上条所说的“按适用规则进入权威内容修改”；不得再用一般的实现/验证/发布权限分离、跨仓库边界或消费项目未重复写入授权为由暂停请求。此组合只授权共享 MCP 细则列出的最小修复、聚焦验证、既有发布、直接消费 pin/lock 与正式激活链，其他权限仍由下条边界约束。
- 上述自动授权只覆盖该 package/plugin 的权威发布及消费项目中与该发布直接相关的 pin/lock/安装配置，不授权提交消费项目其他业务改动、发布消费产品、创建 PR、修改无关仓库、扩大验证范围或新增仓库原本不存在的发布渠道。源码、凭据、上游控制、远端推进、重叠脏改或更高安全边界无法安全闭合时，报告精确阻点。
- 涉及 Unity MCP plugin/server、route、schema、响应或 MCP 验证时，还必须读取 `.agents/shared-rules/instructions/unity-mcp.md`；需要 Unity 操作时读取 `.agents/shared-rules/instructions/unity-editor-safety.md`。

## 归属、权威源码与唯一兼容例外

- 用户当前对所有权的明确声明，或仓库 remote、manifest URL、安装文档、本地 checkout、公开契约和实际 pin 的一致证据，用于证明 package/plugin 的 owner、权威源码与目标 revision；不得仅凭命名猜用户控制权，也不得把无上游权限的第三方依赖当成可自动发布项目。
- package/plugin 的持久改动在独立权威仓库完成并推送，再由消费项目的 `Packages/manifest.json` 与 `Packages/packages-lock.json` 同步固定远端 revision。不得把 `Library/PackageCache`、生成物、缓存、静默 embedded override 或消费项目内副本作为最终权威。
- 修改前重新检查消费项目 manifest/lock、真正解析的 package source 与目标 Unity 版本。只有命中下述唯一常设例外时，才继续审查 package manifest、README/Documentation、CI 矩阵和受控消费项目共同证明的 Unity 支持范围；没有权威证据的版本不得猜测为受支持。跨仓库执行 Git 操作前确认当前 repo root，避免在消费项目或错误 checkout 提交上游源码。
- 唯一常设兼容例外是：由用户维护并作为通用能力面向多个 Unity 项目复用的 package/plugin/Editor extension，包括符合该归属的 Unity MCP package，必须兼容其权威声明支持的 Unity 版本。Unity API、编译器或 Editor 行为差异使用版本条件或等价实现，让各受支持版本只编译和执行该版本的唯一权威分支；这不是运行时 fallback，不得先试新 API 再退旧 API，也不得为版本差异复制运行时状态。不得用 warning suppression 代替版本兼容实现。
- 上述例外只覆盖 Unity 版本差异，不覆盖旧 plugin API、route、schema、响应、配置、序列化数据、工作流、server/protocol、package revision 或历史行为。项目专属代码、只服务单一项目的 package/tool、第三方依赖及其他兼容性仍执行共享代码质量细则的默认关闭规则，除非用户在当前请求中明确点名兼容范围。

## 禁止本地依赖

- Unity package 依赖声明（包括 package/plugin 与以 Unity package 交付的 MCP 扩展）及其消费项目永久禁止写入或提交任何本地文件系统依赖；该禁止同样适用于“临时开发”、“本地验证”和未提交工作树。`Packages/manifest.json`、`Packages/packages-lock.json`、`package.json`、UPM registry/安装依赖配置、README/Documentation 中的 UPM 依赖示例均不得出现 `file:`、`source: local`、绝对或相对文件系统路径、embedded package/override、符号链接或 junction 依赖。
- package/plugin 只在独立权威仓库中开发，先按本文件发布远程版本，再让消费项目依赖 registry 版本或带完整不可变 commit SHA 的远程 Git URL；不得使用本地 checkout 作为依赖边界。manifest 与 lock 必须读回为同一远程 revision 和非 `local` source。
- 每次修改、发布或更新 package/plugin 或 Unity MCP package 前后，必须静态扫描权威仓库及全部可控消费项目的 package metadata、manifest/lock、UPM 安装配置和文档依赖示例。任何本地依赖命中都说明发布/更新未完成；必须先迁移到已发布远程 revision 并删除本地路径与中间状态，不得以本机可解析或尚未提交为由保留。

## 安装、升级与遗留目录清理

- 安装、升级、切换 revision、迁移或重装是否包含删除权限，仍由用户当前要求和消费项目根 `AGENTS.md` 决定。已获授权的迁移开始前必须枚举会被替换或由本轮创建的源码 checkout、解压安装目录、版本目录、临时 clone/worktree、生成物、精确 cache snapshot、宿主 `command`/`args`/`cwd` 与实际运行路径，并明确唯一权威源码、安装路径和 revision；若无权清理将产生的并行目录，必须在创建它之前报告阻点，不得先制造遗留再交付。
- 新 revision 已完成配置或固定，且宿主或消费项目已证明解析到权威路径后，同一迁移事务必须删除权限范围内由本轮创建的临时目录，以及已确认被替代、工作树干净或内容已分类且可恢复的旧安装、旧 checkout、生成物和精确过期 cache snapshot；随后同时读回文件系统、配置、活动进程或 package resolver，证明旧路径不存在且不再被引用。每个逻辑安装入口只保留一个当前权威副本；不得以版本后缀、备份目录、“以后回滚”或兼容性为由长期并存旧副本，除非用户当前明确要求保留某个具体副本。
- 删除前必须解析并核对精确绝对路径及其预期父目录，确认目标不是盘符、用户目录、仓库根或其他宽泛根目录，也不是当前配置、当前 package 解析或活动进程仍在使用的路径；先检查 Git dirty/untracked 与非仓库独有内容。删除只使用已核对的 literal 精确目标，不使用 glob、未解析变量或跨 shell 拼接递归目标，并在删除后读回目标确实不存在。已有 dirty/untracked、用户主动维护的独立 checkout 或无法证明归属的目录不视为可直接删除的“遗留”，必须保留并报告精确冲突。
- 活动进程仍从旧路径执行时，先通过宿主正式提供的 reload、restart 或 reconnect 切换到新路径并再次核对进程；不得用 `Stop-Process`、`taskkill`、`kill` 或 PID 年龄猜测强杀宿主进程，也不得在旧进程仍依赖目录时直接删除。宿主没有可调用的正式重连能力时，准确报告“旧进程仍占用”的清理阻点，要求用户重启对应宿主；旧目录实际删除并读回前不得宣称迁移与清理完成。

## 已确认缺陷的闭环

- 功能异常、假成功、契约违例被 guard/default/重试掩盖、存在 fallback/workaround，以及会稳定诱导错误调用的工具名、description、typed schema、默认值、状态/错误文案、示例、README、安装、配置、使用和迁移说明，都属于需要归因的问题；当前任务表面仍能推进不构成豁免。
- 触发修复前必须有错误日志、最小复现、公开响应与实现不一致、源码与文档/schema 矛盾或其他权威证据。先区分调用者误用、消费项目错误、宿主/传输问题和 plugin 自身缺陷，不得未经归属就盲改上游。
- 一旦项目规则授权且确认属于可维护 plugin，停止使用并删除 workaround/fallback，统一审计同类入口、handler、共享 schema/序列化/错误映射、调用方、测试和文档生成链，在最窄权威 producer/contract 修复报告实例与已确认同源实例。
- 能由 typed schema、权威 producer 或结构化错误契约消除的误用必须修代码契约，不能只补文档，也不得为取证或继续任务写入临时绕行。只读诊断可以收集证据，但不能进入产品、工具、项目状态或交付物。
- 完整闭环按适用范围覆盖：根因实现、风险匹配的聚焦回归、公开 schema/metadata/错误语义、文档与示例、版本/CHANGELOG、上游 commit/push、消费项目 manifest/lock pin、UPM 重新解析、reload/reconnect，以及通过正式公开接口重跑原失败路径。
- 只有源码、仓库访问、凭据、上游控制、安全边界、用户权限或未经授权的 breaking/product change 构成明确阻点时，才可保留未闭环项；交付时说明精确阻点和未验证范围，不实现或保留临时措施、备用路径或防御性替代。

## Package 文件与消费更新

- 提交 Unity Git package 前，确认所有 Unity 可见文件都有同级 `.meta`；排除 `.git`、`.gitignore`、`.gitattributes` 和已有 `.meta`。同时扫描 `folderAsset: yes` 的目录 `.meta`，不得依赖 Git 无法保存的空目录。
- 修改 package metadata、manifest 或 lockfile 时重新打开精确 package block，只做定向编辑，并核对 manifest 与 lock 指向同一 revision；不得依赖旧工具响应、旧端口或旧解析缓存。
- 消费项目更新到新 revision 后，只验证 UPM 实际解析、导入、编译/Console 及当前任务直接依赖的集成路径；已在同一源码与依赖状态下通过的 package 测试不得在上游、发布 checkout 和消费项目重复运行。
- 运行 Unity package test 前从测试 `.asmdef` 读取准确程序集 `name`；不得从 namespace、package ID、旧 schema 或旧 revision 推断。持续 `waiting-for-assembly` 时立即核对已解析 asmdef，不轮询到超时。
- 测试从静态检查、必要编译、最窄 fixture/filter 和集成 smoke 逐级升级。只有共享基础设施、不可隔离的跨类别契约或现有证据证明广泛影响时才运行完整 suite；发布、改版本号或更新 pin 本身不触发全量回归。

## 对外文档与发布

- 修改用户发布 package/plugin 的源码、配置、metadata 或发布内容时，在同一任务审阅并维护 README、`Documentation~`、CHANGELOG、安装/配置/使用/迁移说明、示例和 package metadata；若无需修改，保持文件不变但交付时说明已检查。
- 文档维护不仅是追加说明：删除、合并或改写过时、重复、失真或可能误导的内容。注册表、manifest、生成 metadata 或 schema 已提供权威信息时，文档说明权威来源/生成方式，不再手工维护易漂移的完整副本。
- 任务使用 `update_plan` 时，把文档一致性审阅列为实现和主要技术验证之后、最终发布之前的独立步骤；后续验证改变公开契约时，发布前再次同步文档。
- 发布前确认上游工作树只含本次改动、版本与 CHANGELOG 一致，且仓库既有发布机制要求的 commit/tag/registry 产物可达；不得为了形式统一给只有 Git revision 发布方式的仓库凭空增加 tag 或 registry。随后让所有受本次发布影响且可控的消费项目固定到同一发布 revision；遗漏已确认受影响的消费项目不构成发布闭环。
