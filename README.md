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
- 任何修改在决定验证范围前：读取 `.agents/shared-rules/instructions/unity-editor-safety.md` 的“小改动默认不验证”；纯文档、规则、Git、Packages 或独立仓库任务不得因此查询 Unity。
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

所有消费项目应固定到同一个 tag 或 commit。核对当前 gitlink：

```powershell
git ls-tree HEAD .agents/shared-rules
```

共享验证权限基线以 `instructions/unity-editor-safety.md` 为准。“修复”“实现”“提交”“推送”不包含验证授权；局部小改，以及只能在 Player/Addressables/AssetBundle 等打包后环境确认的 UI 改动，默认只做源码、引用、聚焦 diff 与 worktree 静态审查并交由用户验证。只有用户在当前请求中逐项明确要求，才能执行对应的 Unity/MCP、编译、构建、Play Mode、视觉或打包后检查；消费项目不得用固定清单、只读 MCP 或项目专项流程扩大这项权限。

共享代码质量基线禁止新增或扩写手写 `partial` 类型。既有手写 `partial` 必须先按聚合类型审查并提取当前受影响链上的真实职责；单文件行数或组织门禁不能通过拆成多个 `partial` 文件满足。仅编译器、代码生成器或框架硬契约可构成例外。

每个手写 C# 文件只允许一个顶层类型；属于唯一顶层 owner 的 nested class/enum/struct/interface 等可以保留。每个手写 class（含 `record class`）完整声明不得超过 1500 行，嵌套类型计入外层 class，总体职责已经分裂时必须在达到上限前提取。

共享兼容性基线默认只实现当前权威契约。除非用户在当前请求中明确点名，否则不保留旧 API、schema、数据、行为、版本或兼容层。唯一常设例外是用户维护并跨 Unity 项目复用的通用 package/plugin/Editor extension 对其权威声明支持的 Unity 版本做版本兼容；该例外不扩展到插件 API、route、server/protocol 或历史数据兼容。

共享缺陷治理禁止“发现首个失败后立即修改下一层”。单个非法场景应 fail fast，但同一 build 上其他独立且已授权的场景仍须完成；在整轮证据汇总前冻结可执行实现、诊断和测试语义。多状态或时序缺陷还必须区分 candidate、实例、分支、事务与全局结论的证据作用域，并为合法状态迁移和非法组合 guard 建立可核对证明。完整取证不授权强行继续非法状态、增加 fallback 或运行范围外测试。

## 发布共享规则版本

在本仓库完成修改后执行：

```powershell
git add <changed-files>
git commit -m "<change-summary>"
git tag -a v<major>.<minor>.<patch> -m "v<major>.<minor>.<patch>"
git push origin main
git push origin v<major>.<minor>.<patch>
```

发布后，按“更新共享规则版本”步骤更新每个消费项目的 gitlink。
