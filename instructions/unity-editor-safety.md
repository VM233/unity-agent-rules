# Unity Editor 安全、验证与证据通用细则

## 共享范围与权限边界

- 写入 Unity 项目的 `Assets`、通过 Unity/Editor 工具修改对象、处理 Scene/Prefab/Asset、刷新或编译、查询 Console、进入 Play Mode、运行测试/构建或产出视觉证据时必须读取本文件。
- 用户当前明确要求和消费项目根 `AGENTS.md` 决定允许的验证、运行时、视觉、构建和发布范围。本文件规定安全前置与证据标准，不把“应验证”解释为新增授权。
- 同时修改代码、Prefab/序列化、Localization、Package/Plugin、MCP 或 UI Toolkit 时，还必须读取对应共享和项目专项细则。

## Editor 状态与写入批次

- 只有准备写入项目 `Assets` 内的资源或代码，或通过 Unity/Editor 工具修改其中对象时，才执行写入前 Editor/Play Mode 状态确认。一个连续 `Assets` 写入批次只在首次写入前确认一次；若正在 Play Mode 或状态切换中，先退出并等待稳定。
- 同一批次内不得按文件或补丁重复查询。只有实际进入/退出 Play Mode、切换 Unity 实例或项目、重启 Editor，或中断使先前状态失效后，才在下一次 `Assets` 写入前重新确认。
- 读取、搜索、diff、Git 操作，以及修改 `AGENTS.md`、`.agents/`、普通文档、`Packages`、`ProjectSettings`、`UserSettings`、`Temp`、构建输出或独立 package/plugin 仓库时，不得仅为编辑安全查询 Editor 状态，也不得因此触发 Unity/MCP、刷新、编译或 Domain Reload。
- Unity Editor 专用按钮、菜单、Inspector/Odin、Toolbar、Tooltip、验证消息、日志和异常文本统一使用英文；本地化资源、目标语言预览及本地化流程本身可以使用对应语言。

## 小改动默认不验证

- 消费项目的局部小改完成静态检查后直接交付，默认不得主动启动 Unity 或运行验证。文件位于 `Assets`、使用 Unity/Odin Attribute、触及一个 UXML/Prefab 引用，或 Editor 未来会自动导入，都不单独构成升级理由。
- 小改动必须能由源码和目标 diff 静态穷尽影响，不建立或重组运行时状态、生命周期、事件/订阅、数据产品、交互路径或跨对象所有权，不改变字段/方法数据契约、序列化对象图、引用/GUID、Scene/Prefab 拓扑、已有可见布局/样式/交互、Package 或 Build 行为。
- 典型小改包括：文档、规则、注释；Inspector/Odin 展示或选择元数据；把一一对应字面量替换为已有常量/preset；给既有入口增加无额外业务逻辑的薄 Editor 按钮；复用已有权威状态对单个既有 UI 区域做局部显示、隐藏或启用接线，且显示时表现不变。
- 小改动只允许读取源码、搜索引用、检查聚焦 diff、`git diff --check` 和 worktree 状态，以及任务细则明确要求的写入前安全动作。不得主动调用 Unity MCP、`AssetDatabase.Refresh`、导入/编译、Domain Reload、Console/`Editor.log`、测试、构建、Play Mode、UI Builder/Game View、截图、像素或视觉检查。
- 只有静态检查证明改动越过上述边界时，才升级为非平凡改动，并在项目授权范围内执行与实际风险匹配的最小验证。不得以“保险起见”继续升级；用户明确要求不验证时，完成允许的实现与静态审查并准确列出未验证项。
- 用户明确要求验证时，只获得当前请求所列验证的授权；不得据此扩大到无关测试、完整回归、运行时或视觉检查。

## Scene 与资源写入安全

- 已加载 Scene 不得由 `apply_patch`、shell、文本编辑器或其他 Unity 外部路径改写或重新导入其 `.unity` 文件。使用 Unity Scene API、`SerializedObject`、场景/组件工具或等价 Unity 内部路径修改并显式保存；保存后不得再由外部格式化器改写。
- 确需外部修改 Scene 时，先确认没有未保存数据并在 Unity 中卸载/切换，再修改、导入和重开。不得自动确认 Reload/Save/Discard 对话框，或调用会等待用户选择的 API；发现脏 Scene 时应失败关闭并返回明确状态。
- 资源删除、重命名、移动、复制、事务和目录级 refresh/import 不得包含当前已加载 Scene；确需处理时先通过 Scene API 保存并卸载。模态 Reload/Save 已出现时停止操作，交由用户决定。

## Safe Mode

- Unity 处于 Safe Mode 时，不先操控 Editor UI、点击退出按钮、进入 Play Mode、恢复依赖项目程序集的 MCP，或用自动化隐藏错误。Safe Mode 已证明正常运行时路径不可用。
- 直接读取当前 `Editor.log`、编译输出和权威源码，一次收集完整相关错误族，按共享 owner/契约成批修复并等待重新编译。项目 MCP 程序集无法加载时使用进程标题和日志取证；编译恢复到正常 Editor 后再 reconnect/reload MCP。
- Safe Mode 不改变小改动规则：纯文档、规则、Git、Packages 或独立仓库工作不得仅因 Editor 处于 Safe Mode 就查询或操作 Unity。

## 验证证据与副作用

- 诊断、测试、探针、截图、预览、报告或自动化只有在所需源状态真实存在，且产物确实证明目标条件时才能宣称成功。前置状态不可用时必须失败关闭，不得用默认对象、旧帧、空白图、占位文件、伪造指标或乐观响应推进流程。
- 工具返回 success、文件可写出或图片可解码，不等于证据有效。空白/纯黑/过期/被遮挡、误拍 Edit Mode、缺失主体或时序未稳定的产物均无结论；修复生产路径并重新取得有效证据后才能下结论。
- 运行时或视觉验证前记录 worktree 状态，退出 Play Mode 后再比较。只清理由检查点证明为本轮新增的缓存、序列化副作用和临时产物，保留所有既有用户改动。
- 临时 `Debug.LogError`、探针和截图必须有独特可搜索标识、范围最小并在交付前删除；真正跨任务复用的诊断能力应进入通用 MCP 或项目 project tool，不得把一次性诊断长期留在生产链。
- 查询编译结果时同时检查 error、warning 和独立的 `obsolete`/`deprecated` 警告；不得以 error 为零宣称编译干净，也不得用 warning suppression 代替修复。
