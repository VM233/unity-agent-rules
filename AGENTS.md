# Unity Agent Rules 维护规则

## 所有权

- 本仓库只维护跨 Unity 项目共享的规则；消费项目通过 `.agents/shared-rules` Git submodule 固定 commit，并由项目根 `AGENTS.md` 路由。
- 项目路径、业务资源、Prefab/Wrapper、产品行为和项目专属验证留在消费项目。共享正文不得复制到消费项目，也不得依赖机器绝对路径、PackageCache、符号链接或运行时下载。

## 修改与发布

- 修改共享规则前审计所有可发现消费项目的根规则、专项规则和冲突。用户明确要求新增、纠正、优化、删除或沉淀共享规则时，已授权提交、语义化版本 tag、推送本仓库，并更新、提交和推送各消费项目必要的 overlay/gitlink；不授权夹带其他脏改或发布产品。纯规则维护只做静态校验，不因此运行 Unity。
- 同一逻辑变更同步维护 instruction、README 和 CHANGELOG。先区分每条要求约束的对象与阶段；含糊、重复、过时或互相冲突的原条款必须改写或删除，不能只追加相反要求，也不能把上游的信息取舍改成下游的逐项展示清单；同步检查消费端 overlay，不保留新旧双轨。
- 发布顺序：确认远端基线和 diff；提交并推送共享仓库；创建并推送 tag；把全部消费项目固定到同一 commit；提交并推送各消费项目规则文件。消费项目精确 gitlink 处于 clean detached HEAD 是正常状态。
- 规则和文档只做引用、格式、diff 与 gitlink 静态校验，不启动 Unity、编译或测试。

## Git

- 未经用户当前明确要求，不创建、建议或切换额外分支、worktree 或 PR；使用已检出分支，消费项目的单分支约束优先。
- 只暂存本次规则文件。保留用户其他 dirty/untracked 内容；发布共享 commit 后再更新消费项目 gitlink，不把 detached submodule 中的未提交修改当成交付。
