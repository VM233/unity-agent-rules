# Unity Package 与 Plugin 通用细则

## 共享范围与权限边界

- 修改 Unity package/plugin、独立源码仓库、manifest/lockfile、Git revision、package metadata、发布内容或远程更新流程时必须读取本文件。
- 用户当前明确要求和消费项目根 `AGENTS.md` 决定是否授权修改外部仓库、测试、提交、推送、发布和消费项目 pin。本文件定义技术闭环，不自行授予这些权限。
- 涉及 Unity MCP plugin/server、route、schema、响应或 MCP 验证时，还必须读取 `.agents/shared-rules/instructions/unity-mcp.md`；需要 Unity 操作时读取 `.agents/shared-rules/instructions/unity-editor-safety.md`。

## 归属与权威源码

- 先用仓库 remote、manifest URL、安装文档、本地 checkout、公开契约和实际 pin 证明 package/plugin 的 owner、权威源码与目标 revision；不得仅凭命名猜用户控制权，也不得把无上游权限的第三方依赖当成可自动发布项目。
- 持久修复在独立权威仓库完成并推送，再由消费项目的 `Packages/manifest.json` 与 `Packages/packages-lock.json` 同步固定远端 revision。不得把 `Library/PackageCache`、生成物、缓存、静默 embedded override 或消费项目内副本作为最终权威。
- 修改前重新检查消费项目 manifest/lock、真正解析的 package source、目标 Unity 版本及 package manifest 声明的兼容范围。跨仓库执行 Git 操作前确认当前 repo root，避免在消费项目或错误 checkout 提交上游源码。
- Package 代码兼容其 manifest 声明的全部 Unity 版本。API 差异使用版本条件、窄适配层或等价实现，让各版本只编译适用分支；不得用 warning suppression 代替兼容实现。

## 已确认缺陷的闭环

- 功能异常、假成功、缺少必要前置校验、恢复路径不完整，以及会稳定诱导错误调用的工具名、description、typed schema、默认值、状态/错误文案、示例、README、安装、配置、使用和迁移说明，都属于需要归因的问题；存在 workaround 或当前任务仍能推进不构成豁免。
- 触发修复前必须有错误日志、最小复现、公开响应与实现不一致、源码与文档/schema 矛盾或其他权威证据。先区分调用者误用、消费项目错误、宿主/传输问题和 plugin 自身缺陷，不得未经归属就盲改上游。
- 一旦项目规则授权且确认属于可维护 plugin，暂停继续堆 workaround，统一审计同类入口、handler、共享 schema/序列化/错误映射、调用方、测试和文档生成链，在最窄权威 producer/contract 修复报告实例与已确认同源实例。
- 能由 typed schema、前置校验或结构化错误阻止的误用必须修代码契约，不能只补文档。临时绕行只可用于收集证据或避免丢失工作，不能关闭缺陷或替代正式路径。
- 完整闭环按适用范围覆盖：根因实现、风险匹配的聚焦回归、公开 schema/metadata/错误语义、文档与示例、版本/CHANGELOG、上游 commit/push、消费项目 manifest/lock pin、UPM 重新解析、reload/reconnect，以及通过正式公开接口重跑原失败路径。
- 只有源码、仓库访问、凭据、上游控制、安全边界、用户权限或未经授权的 breaking/product change 构成明确阻点时，才可保留未闭环项；交付时说明精确阻点、临时措施和未验证范围。

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
- 发布前确认上游工作树只含本次改动、版本与 CHANGELOG 一致、远端 commit/tag 可达；随后让所有受控消费项目固定到同一发布 revision。仅更新一个消费项目不构成共享迁移完成。
