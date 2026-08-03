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
