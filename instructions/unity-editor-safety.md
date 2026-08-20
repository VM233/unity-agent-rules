# Unity Editor 安全、验证与证据通用细则

## 范围与权限

- 写入 Unity `Assets`、通过 Editor 修改对象、处理 Scene/Prefab/Asset、刷新或编译、查询 Console、进入 Play Mode、测试、构建或产出视觉证据时读取本文件；同时命中的代码、序列化、Localization、Package、CLI/Pipeline 和 UI 规则也要读取。
- 用户当前要求和消费项目根 `AGENTS.md` 决定验证、运行时、视觉、构建与发布权限。本文件只规定安全前置和证据标准；操作层的停止/无结论不应复制成生产代码 fallback。

## Editor 状态与写入批次

- 只有准备写入 `Assets` 或通过 Unity 修改其中对象时，才在连续写入批次首次写入前确认 Editor/Play Mode 状态；Play Mode 或切换中先退出并等待稳定。
- 同一批次不按文件重复查询。进入/退出 Play Mode、切换项目或实例、重启 Editor，或中断使状态失效后，才在下一次写入前重查。
- 读取、搜索、diff、Git，以及修改 `AGENTS.md`、`.agents/`、文档、`Packages`、`ProjectSettings`、`UserSettings`、`Temp`、构建输出或独立 package/plugin 仓库，不得仅为编辑安全启动或查询 Unity。
- Editor 专用按钮、菜单、Inspector/Odin、Toolbar、Tooltip、验证消息、日志和异常使用英文；本地化内容及其预览使用目标语言。

## 编译与其他验证

- 修改进入 Unity 编译的代码或编译契约后，在最后一次代码写入后执行一次当前项目的权威 refresh/import、Domain Reload 与编译，并读取完整结果。该编译由代码修改请求本身授权；不授权 Play Mode、测试、Player/Content Build、运行时、视觉、截图或输入自动化。
- 编译失败属于实现未完成：一次收集相关错误族，修正权威源码并重新编译到 error 为零；同时检查 warning 和 `obsolete`/`deprecated`。编译成功只证明编译契约。
- 不含代码或编译契约变化的改动默认只做源码、引用、目标 diff、`git diff --check` 与 worktree 静态审查。除强制编译外，任何 Unity command、测试、构建、Play Mode、UI Builder/Game View、截图、日志或输入验证都需要用户在当前请求中明确点名。
- 文件位于 `Assets`、触及 UXML/USS/UI Prefab、问题曾在运行时出现、已有场景或 Editor 正在运行，不会自动扩大验证权限。验证未获授权时完成允许的实现并准确列出未验证项。
- 只能在 Player、Addressables/AssetBundle 或其他打包环境确认的 UI 默认交由用户验证；用户只要求构建时不自动启动或操作产物。
- 获授权的验证必须对应本轮修改的 owner、契约、生命周期、产品或相邻风险。运行时失败按 `code-quality.md` 冻结当前 build 与测试语义，保存首个非法状态并完成其余独立且已授权场景后统一归因；不得边测边补丁或把失败后移当成功。

## Scene 与资源写入

- 已加载 Scene 不得由 `apply_patch`、shell 或外部文本工具改写 `.unity`。使用 Unity Scene API/`SerializedObject` 修改并显式保存，保存后不再外部格式化。
- 确需外部修改 Scene 时，先确认无未保存数据并在 Unity 中卸载或切换；不得自动确认 Reload/Save/Discard 对话框。脏 Scene 或模态对话框出现时停止并交由用户决定。
- 删除、重命名、移动、复制、事务或目录 refresh/import 不得包含已加载 Scene；确需处理时先通过 Scene API 保存并卸载。

## Safe Mode

- Safe Mode 下不操控 Editor UI、退出按钮、Play Mode 或依赖项目程序集的 Automation catalog，也不隐藏编译错误。
- 直接读取当前 `Editor.log`、编译输出和权威源码，一次收集错误族并修复；恢复正常 Editor 后再通过官方 Unity CLI 发现 catalog。纯规则、文档、Git 或 package 工作仍不因此查询 Unity。

## 证据与副作用

- 诊断、测试、截图、预览、报告或自动化只有在源状态真实存在且产物证明目标条件时才有效。默认对象、旧帧、空白/黑图、占位文件、伪造指标或工具 success 均不能替代证据。
- 运行时或视觉验证前记录 worktree，退出 Play Mode 后比较。只清理由检查点证明为本轮新增的缓存、序列化副作用和临时产物，保留既有用户改动。
- 临时探针、日志和截图使用唯一可搜索标识并在交付前删除；跨任务能力进入正式 Automation/project tool。查询编译时同时报告 error、warning 与 obsolete/deprecated，不用 suppression 代替修复。
