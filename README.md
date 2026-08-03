# Unity Agent Rules

BattleIdle 与 MarbleBattlers 共用的 Unity Agent 细则权威仓库。消费项目通过 Git submodule 固定到明确 commit；项目专属规则继续保留在各自仓库。

## 消费项目接入

在项目根目录执行：

```powershell
git submodule add https://github.com/VM233/unity-agent-rules.git .agents/shared-rules
```

项目根 `AGENTS.md` 必须保留相对路径路由，例如：

```markdown
- 使用或修改 Unity MCP、设计响应/工具元数据、增改 route/schema：必须完整读取 `.agents/shared-rules/instructions/unity-mcp.md`。
- 若共享规则 submodule 尚未初始化，先执行 `git submodule update --init --recursive`；读取成功前不得继续相关任务。
```

协作者应递归 clone：

```powershell
git clone --recurse-submodules <project-repository-url>
```

已有 checkout 使用：

```powershell
git submodule sync --recursive
git submodule update --init --recursive
```

## 发布与升级

先在本仓库提交、推送并创建语义化版本 tag。然后在每个消费项目固定相同版本：

```powershell
git -C .agents/shared-rules fetch --tags origin
git -C .agents/shared-rules switch --detach <tag-or-commit>
git add .agents/shared-rules
```

父项目记录的是精确 gitlink commit，不跟随远端 `main` 浮动。使用以下命令核对：

```powershell
git ls-tree HEAD .agents/shared-rules
```

## 边界

- 共享：MCP/Plugin 的跨项目技术契约、响应精简、测试分级、工具发现、Job/reload 和证据产品规则。
- 项目本地：业务组件、资源结构、Wrapper/Prefab、项目工作流、本地化、运行时/视觉验收和授权边界。
- 不使用机器专属绝对路径、符号链接、Unity PackageCache 或运行时下载来加载规则。
