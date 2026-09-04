# Unity Editor 安全、验证与证据通用细则

## 范围与权限

- 写入 Unity `Assets`、通过 Editor 修改对象、处理 Scene/Prefab/Asset、刷新或编译、查询 Console、进入 Play Mode、测试、构建或产出视觉证据时读取本文件；同时命中的代码、序列化、Localization、Package、CLI/Pipeline 和 UI 规则也要读取。
- 用户的实现、修复或内容修改请求包含与该任务直接相关、风险匹配的验证；静态审查、Unity 读回、编译、测试，以及必要的 Play Mode、运行时、视觉、输入和本地构建验证不需要另行授权，也不要求用户再次点名具体工具。诊断或审查请求可执行范围内的非破坏性检查，但不因此授权实现修改。
- 用户明确提出的“不运行”“不构建”或具体工具限制仍须遵守；默认验证权限不包括无关场景、真实用户数据或外部系统的变更，也不代替业务提交、推送、产品发布或破坏性操作的权限。消费项目按本文件选择验证范围，不得再以未单独点名验证为由跳过必要检查或反复询问。
- 本文件同时规定安全前置和证据标准；操作层的停止/无结论不应复制成生产代码 fallback。

## Editor 状态与写入批次

- 只有准备写入 `Assets` 或通过 Unity 修改其中对象时，才在连续写入批次首次写入前确认 Editor/Play Mode 状态；Play Mode 或切换中先退出并等待稳定。
- 同一批次不按文件重复查询。进入/退出 Play Mode、切换项目或实例、重启 Editor，或中断使状态失效后，才在下一次写入前重查。
- 读取、搜索、diff、Git，以及修改 `AGENTS.md`、`.agents/`、文档、`Packages`、`ProjectSettings`、`UserSettings`、`Temp`、构建输出或独立 package/plugin 仓库，不得仅为编辑安全启动或查询 Unity。
- Editor 专用按钮、菜单、Inspector/Odin、Toolbar、Tooltip、验证消息、日志和异常使用英文；本地化内容及其预览使用目标语言。

## 编译与其他验证

- 修改进入 Unity 编译的代码或编译契约后，在最后一次代码写入后执行一次当前项目的权威 refresh/import、Domain Reload 与编译，并读取完整结果；其他验证按同一改动的行为、资源和交互风险选择，不另设授权门槛。
- 编译失败属于实现未完成：一次收集相关错误族，修正权威源码并重新编译到 error 为零；同时检查 warning 和 `obsolete`/`deprecated`。编译成功只证明编译契约。
- 不含代码或编译契约变化的改动从源码、引用、目标 diff、`git diff --check` 与 worktree 静态审查开始。涉及 Item、GamePrefab、本地化或其他 authoring 契约时，直接运行命中规则要求的一等审查工具；工具通过 Unity/CLI 执行不构成再次询问授权的理由。
- 验证按风险逐级选择：先静态与聚焦检查，行为变化或运行时故障再执行真实生产链回归；视觉和交互改动补足对应状态、截图或输入证据。纯文案小改不自动升级为整场战斗，已有场景、打开的 Editor 或发布版本号也不自动触发无关测试或全量 suite。
- Toolbar、场景或运行时测试覆盖的产品流程如果支持重新战斗/重试，且本轮改动涉及跨战斗保留、重建或重新接管的 owner、缓存、订阅、receipt 或可变状态，至少一个受影响场景必须执行“首次战斗 -> 重新战斗/重置 -> 第二次战斗”的完整生命周期。第二次战斗须从重置后的 runtime generation、owner 身份、结果与 Console Error/Exception/Assert 重新取证，并按职责检查不可变产品复用或退役、可变状态清空、订阅/receipt 回收及重新接管；首次运行干净或复用首次证据不能替代重开验证。
- 只能在 Player、Addressables/AssetBundle 或其他打包环境确认的问题，执行必要的本地构建与产物验证，不默认转交用户；构建成功不等于允许运营发布。用户明确限制运行、工具或构建，或环境使验证无法完成时，报告准确的未验证范围和原因。
- 验证必须对应本轮修改的 owner、契约、生命周期、产品或相邻风险。运行时失败按 `code-quality.md` 冻结当前 build 与测试语义，保存首个非法状态并完成其余独立且属于本轮范围的场景后统一归因；不得边测边补丁或把失败后移当成功。

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
