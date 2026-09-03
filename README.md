# Unity Agent Rules

跨 Unity 项目共享的 Agent 规则。项目通过 Git submodule 固定版本，根 `AGENTS.md` 只保存项目事实与任务路由。

## 接入项目

```powershell
git submodule add https://github.com/VM233/unity-agent-rules.git .agents/shared-rules
git add .gitmodules .agents/shared-rules
git commit -m "Add shared Unity agent rules"
```

克隆或补初始化：

```powershell
git clone --recurse-submodules <project-repository-url>
git submodule sync --recursive
git submodule update --init --recursive
```

## 根规则路由模板

消费项目根 `AGENTS.md` 使用项目内相对路径，只加载当前任务命中的细则：

```markdown
- 所有 Unity 修改先按 `.agents/shared-rules/instructions/unity-editor-safety.md` 分类。
- 非平凡代码、工具和脚本：`.agents/shared-rules/instructions/code-quality.md`。
- Prefab、组件职责和序列化：`.agents/shared-rules/instructions/unity-prefabs-and-serialization.md`。
- UI Toolkit/UXML/USS：`.agents/shared-rules/instructions/unity-ui-toolkit.md`；名称或素材迁移再读 `.agents/shared-rules/instructions/unity-ui-naming-and-assets.md`。
- Localization 与玩家文案：`.agents/shared-rules/instructions/unity-localization.md`。
- Package/plugin、manifest/lock 与发布：`.agents/shared-rules/instructions/unity-packages-and-plugins.md`。
- 官方 Unity CLI、Pipeline、Automation catalog 与旧传输迁移：`.agents/shared-rules/instructions/unity-cli-and-pipeline.md`。
- Git 分支、提交、推送与 PR：`.agents/shared-rules/instructions/git-workflow.md`。
- 共享文件缺失时先执行 `git submodule update --init --recursive`，读取成功前不继续相关任务。
```

## 核心约定

- 用户请求和消费项目规则决定功能、验证、Git、发布与破坏性操作权限；共享规则负责工程质量和工具安全，不要求代码改动行数或文件数最少。
- 实现围绕唯一 owner、producer、不可变产品、生命周期和 consumer 闭合。契约违例修 producer 并直接暴露，禁止 guard、fallback、默认替代、重试和新旧双轨。
- 实现、修复或内容修改包含直接相关、风险匹配的验证，不需要用户单独授权或点名工具。Unity 代码或编译契约变化必须完成权威编译并处理全部 error；其他改动先做静态及领域审查，再按行为、视觉、交互或打包风险补足验证。用户明确限制、数据安全、业务提交与产品发布边界保持有效。
- 玩家文案写入前必须建立机制事实表与逐 Locale 术语证据表；内部 ID、英文类名和其他 Locale 只能用于定位，不能直接生成玩家术语。已有注册对象且当前 UI 可达其 Tooltip/link 时，该对象独占内部机制文案，外层描述只引用它并保留自身新增的触发、目标、施加参数与例外，禁止复制内部效果；没有可引用产品或可达说明时才用普通语言描述可观察效果。
- 功能契约与 UI 信息取舍分开：保留完整实现和边界测试，默认描述只展示正常游玩决策需要的信息；技术上支持叠加不代表必须解释多份叠加或显示每个内部参数，已展示的动态值仍从行为 owner 读取。
- 所有中文玩家描述、详情与 Tooltip 均不以句号收尾，覆盖段落、列表项、富文本和动态拼接的最终可见结尾；写入及交付都执行本地化规则的全范围检查，不限物品类别或 key 后缀。
- 每个手写 C# 文件一个顶层类型，手写 class 不超过 1500 行，禁止新增或扩写手写 `partial`；生成器或框架硬契约例外必须可证明。
- Unity package 只从 registry 或完整远端 Git SHA 消费，永久禁止 `file:`、本地路径、embedded override、symlink 和 junction。用户维护 package 被修改时按既有流程发布，并同步直接消费 pin/lock。
- VMFramework GamePrefab 变更在所有写入、编译和读回之后、交付或 Git 发布之前，最后通过官方 bounded catalog 查询并调用精确 `validate-game-prefabs` contract；结果必须证明所有 Wrapper 配置均可从运行时 GeneralSetting provider 图到达，且零错误、零缺失 Prefab、零未注册配置。任何后续 GamePrefab、Wrapper、provider 或引用改动都会使结果失效。
- 官方 Unity CLI 与 `com.unity.pipeline` 是唯一传输；调用时区分全局参数、官方子命令 option 与 `--` 后的 Editor command 参数，并通过 bounded catalog discovery 获取 VM facade 的项目能力，不保留第二 server、HTTP 或脚本旁路。
- UI Toolkit 的布局关系由语义 owner 表达；absolute overlay 只因叠放而脱流，父级已拥有双轴居中时不得再用 `left`/`top` 手算同一中心，边缘锚定与有证据的光学偏移除外。
- 兼容性默认关闭；唯一常设例外是用户维护的跨项目通用 package/plugin 对其声明支持的 Unity 版本进行编译期兼容。
- 未经用户当前明确要求，不创建额外分支、worktree 或 PR；保留其他 dirty/untracked 内容，只暂存当前授权范围。
- 用户明确要求 push 时，完成边界在远端而不在本地：同步远端、语义解决可确定冲突、完成必要验证并继续推送，最后读回远端目标引用与本地发布 commit 一致；普通 push 仍不等于 `push all`。

各领域完整契约以 `instructions/` 对应文件为准；README 不复制细节清单。

## 更新消费版本

```powershell
git -C .agents/shared-rules fetch --tags origin
git -C .agents/shared-rules switch --detach <tag-or-commit>
git add .agents/shared-rules
git commit -m "Update shared Unity agent rules"
```

精确 gitlink 使用 clean detached HEAD 是正常状态。所有消费项目应固定同一发布 commit，可用下列命令核对：

```powershell
git ls-tree HEAD .agents/shared-rules
```

## 发布共享规则

用户明确要求新增、纠正、优化、删除或沉淀共享规则时，已授权完成共享仓库提交/tag/推送及全部消费项目必要 overlay/gitlink 更新；不得夹带消费项目其他改动或创建 PR。纯规则和文档更新只需静态校验，不因此运行 Unity。

```powershell
git add <changed-files>
git commit -m "<change-summary>"
git tag -a v<major>.<minor>.<patch> -m "v<major>.<minor>.<patch>"
git push origin main
git push origin v<major>.<minor>.<patch>
```
