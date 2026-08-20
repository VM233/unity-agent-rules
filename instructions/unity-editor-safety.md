# Unity Editor 安全、验证与证据通用细则

## 共享范围与权限边界

- 写入 Unity 项目的 `Assets`、通过 Unity/Editor 工具修改对象、处理 Scene/Prefab/Asset、刷新或编译、查询 Console、进入 Play Mode、运行测试/构建或产出视觉证据时必须读取本文件。
- 用户当前明确要求和消费项目根 `AGENTS.md` 决定允许的验证、运行时、视觉、构建和发布范围。本文件规定安全前置与证据标准，不把“应验证”解释为新增授权。
- 同时修改代码、Prefab/序列化、Localization、Package/Plugin、CLI/Pipeline 或 UI Toolkit 时，还必须读取对应共享和项目专项细则。
- 本文件中的停止操作、等待用户决定和证据无结论属于外部工具授权与证据真实性边界，不是生产实现的 fail-close、防御性代码或 fallback；不得把这些操作规则复制成产品代码的备用路径。生产代码统一执行 `code-quality.md` 的直接契约基线。

## Editor 状态与写入批次

- 只有准备写入项目 `Assets` 内的资源或代码，或通过 Unity/Editor 工具修改其中对象时，才执行写入前 Editor/Play Mode 状态确认。一个连续 `Assets` 写入批次只在首次写入前确认一次；若正在 Play Mode 或状态切换中，先退出并等待稳定。
- 同一批次内不得按文件或补丁重复查询。只有实际进入/退出 Play Mode、切换 Unity 实例或项目、重启 Editor，或中断使先前状态失效后，才在下一次 `Assets` 写入前重新确认。
- 读取、搜索、diff、Git 操作，以及修改 `AGENTS.md`、`.agents/`、普通文档、`Packages`、`ProjectSettings`、`UserSettings`、`Temp`、构建输出或独立 package/plugin 仓库时，不得仅为编辑安全查询 Editor 状态，也不得因此触发 Unity CLI/Pipeline、刷新、编译或 Domain Reload。
- Unity Editor 专用按钮、菜单、Inspector/Odin、Toolbar、Tooltip、验证消息、日志和异常文本统一使用英文；本地化资源、目标语言预览及本地化流程本身可以使用对应语言。

## 代码改动强制编译，其他小改动默认不验证

- 修改 Unity 项目、package 或 plugin 中进入编译的代码或编译契约后，必须在最后一次代码写入后执行一次当前项目的权威编译，并读取该次编译的完整结果。代码修改请求本身已授权这项最窄编译动作，包括为触发该次编译所必需的 refresh/import、Domain Reload 和编译输出读取；不授权 Play Mode、EditMode/PlayMode test、Player/Content Build、运行时、视觉、截图或输入自动化。
- 编译失败属于当前实现未完成，必须一次收集完整相关错误族，修正权威源码后重新编译，直到 error 为零。每次结果同时检查 warning 以及独立的 `obsolete`/`deprecated` 警告并如实报告；不得用 suppression、忽略日志或只检查最早一条错误冒充通过。编译成功只证明编译契约，不证明运行时、序列化、视觉或产品行为。
- 纯文档、规则、注释、非代码资源、Git、Packages pin、ProjectSettings、UserSettings、构建输出或独立仓库的非编译输入不因此触发 Unity。若代码改动无法访问权威编译环境，必须明确标记编译阻塞或未验证，不能把静态检查写成已完成编译。

- 消费项目不含代码或编译契约变化的局部小改完成静态检查后直接交付。除上述强制编译外，除非用户在当前请求中明确点名某项验证，否则不得主动启动 Unity 或运行任何验证。文件位于 `Assets`、使用 Unity/Odin Attribute、触及 UXML/USS/UI Prefab、问题曾在运行时出现、当前已有可复现场景或 Editor 正在运行/暂停，均不构成其他验证授权或升级理由；“修复”“实现”“提交”“推送”只包含代码改动的最窄编译门禁，不包含其他验证授权。
- 小改动必须能由源码和目标 diff 静态穷尽影响，不建立或重组业务运行时状态、数据产品、交互模型或跨对象所有权，不改变字段/方法数据契约、序列化对象图、引用/GUID、Scene/Prefab 拓扑、已有可见布局/样式/交互、Package 或 Build 行为。单个既有 UI Modifier/组件只读取既有权威状态，局部调整刷新触发方式、刷新频率、显示、隐藏或启用条件，且不新增状态 producer、状态写入、输入语义、数据契约或可见表现时，仍属于小改；从已有事件回调改为仅在面板打开期间轮询刷新也不得据此升级。
- 典型小改包括：文档、规则、注释；Inspector/Odin 展示或选择元数据；把一一对应字面量替换为已有常量/preset；给既有入口增加无额外业务逻辑的薄 Editor 按钮；复用已有权威状态对单个既有 UI 区域做局部显示、隐藏、启用或刷新接线，且显示时表现不变。
- 小改动只允许读取源码、搜索引用、检查聚焦 diff、`git diff --check` 和 worktree 状态，以及任务细则明确要求的写入前安全动作。代码改动额外执行上述最窄 refresh/import、Domain Reload、编译及其 Console/`Editor.log` 结果读取；除此之外不得主动调用 Unity CLI/Pipeline command、测试、构建、Play Mode、UI Builder/Game View、截图、像素或视觉检查，消费项目专项也不得把固定审查清单、只读 Automation command 或“静态工具”重新解释成例外。
- 只有静态检查证明改动越过上述边界，且当前用户请求明确授权了对应验证时，才执行与实际风险匹配的最小验证。不得以“保险起见”、运行时缺陷、历史验收阶梯、完成闭环或准备提交推送为由继续升级；验证未获授权时完成允许的实现与静态审查，并准确列出未验证项。
- 仅在 Player Build、Addressables/AssetBundle Content Build 或其他打包后运行环境中才能复现或最终确认的 UI 改动，无论是否属于小改，默认都只做静态实现与审查并交由用户验证；其中若修改代码，仍只执行上述强制编译。除非用户在当前请求中逐项明确要求，不得执行 Player/Content Build、Build And Run、Use Existing Build、启动已打包 Player、Play Mode/UI Builder 替代验证、截图、运行日志检查、输入自动化或其他复现/测试；不得因为 Editor 无法证明打包后行为就自行补测。
- 用户明确要求验证时，只获得当前请求逐项列出的验证授权；强制编译不把授权扩大到无关测试、完整回归、运行时、视觉、构建或打包后检查。此前由用户或 Agent 进入、暂停或保留的 Editor/Player 状态不是这些额外验证的授权。
- 每一项验证必须能追溯到本轮实际修改的 owner、契约、生命周期、数据产品、直接 consumer，或已有证据支持的相邻回归；同属一个功能、Prefab、场景或测试阶梯不构成相关性。项目专项中的固定清单必须按受影响契约条件化，未触及且无相邻风险证据的类别应跳过并说明原因，不能冒充已通过。
- 一项运行时失败只阻断依赖其有效前置状态的后续验证。单个场景在首个非法状态停止后，必须冻结本轮可执行实现、诊断与测试语义；在修改代码、刷新或重新编译前，继续用同一构建完成其余相互独立且处于本轮范围内的场景，并汇总完整缺陷族证据。严禁看到第一个失败就立刻修改下一层、让不同场景实际运行在不同 build 上，或把最早失败边界冒充完整根因；也不得强行继续非法状态或借机运行范围外类别。
- 冻结 build 的每个场景必须保存“失败边界”而非预设“因果结论”：至少包含最后合法状态、首个非法状态、producer/transaction 阶段、publication/pending/adoption/CAS 转换、revision/context、执行产品余量及直接 Console 错误。完成范围内独立矩阵前不得修改生产代码、诊断判据或测试参数；矩阵完成后也必须先统一所有权和状态迁移归因，不能按场景逐个修补。
- 运行更久、失败 tick 后移、一个地图转绿、异常从 consumer 移到 producer、candidate/branch 数增加或某项 aggregate 指标改善，都只说明失败边界改变。除非同一冻结 build 的相关矩阵和事务时间线证明非法状态已无法构造，否则不得把这些现象记为修复成功，也不得据此开始调下一个局部症状。
- 若改动把原子工作变成跨 Fixed Step、异步、分帧、增量或可恢复事务，或改变 scheduler、priority、budget、cadence、publication/adoption 时机，它同时改变正确性生命周期；即使目标名义上是性能，也必须先按受影响 owner 重跑正确性场景，再运行获授权的性能验证。性能改善不能替代状态可达、迁移闭合和执行连续性证据。

## Scene 与资源写入安全

- 已加载 Scene 不得由 `apply_patch`、shell、文本编辑器或其他 Unity 外部路径改写或重新导入其 `.unity` 文件。使用 Unity Scene API、`SerializedObject`、场景/组件工具或等价 Unity 内部路径修改并显式保存；保存后不得再由外部格式化器改写。
- 确需外部修改 Scene 时，先确认没有未保存数据并在 Unity 中卸载/切换，再修改、导入和重开。不得自动确认 Reload/Save/Discard 对话框，或调用会等待用户选择的 API；发现脏 Scene 时停止外部写入并返回明确状态。
- 资源删除、重命名、移动、复制、事务和目录级 refresh/import 不得包含当前已加载 Scene；确需处理时先通过 Scene API 保存并卸载。模态 Reload/Save 已出现时停止操作，交由用户决定。

## Safe Mode

- Unity 处于 Safe Mode 时，不先操控 Editor UI、点击退出按钮、进入 Play Mode、恢复依赖项目程序集的 Automation catalog，或用自动化隐藏错误。Safe Mode 已证明正常运行时路径不可用。
- 直接读取当前 `Editor.log`、编译输出和权威源码，一次收集完整相关错误族，按共享 owner/契约成批修复并等待重新编译。项目 Automation 程序集无法加载时使用进程标题和日志取证；编译恢复到正常 Editor 后再通过官方 Unity CLI 重新发现 catalog。
- Safe Mode 不改变小改动规则：纯文档、规则、Git、Packages 或独立仓库工作不得仅因 Editor 处于 Safe Mode 就查询或操作 Unity。

## 验证证据与副作用

- 诊断、测试、探针、截图、预览、报告或自动化只有在所需源状态真实存在，且产物确实证明目标条件时才能宣称成功。前置状态不可用时将该项标记为无结论并停止，不得用默认对象、旧帧、空白图、占位文件、伪造指标或乐观响应推进流程。
- 工具返回 success、文件可写出或图片可解码，不等于证据有效。空白/纯黑/过期/被遮挡、误拍 Edit Mode、缺失主体或时序未稳定的产物均无结论；修复生产路径并重新取得有效证据后才能下结论。
- 运行时或视觉验证前记录 worktree 状态，退出 Play Mode 后再比较。只清理由检查点证明为本轮新增的缓存、序列化副作用和临时产物，保留所有既有用户改动。
- 临时 `Debug.LogError`、探针和截图必须有独特可搜索标识、范围最小并在交付前删除；真正跨任务复用的诊断能力应进入通用 Automation catalog 或项目 project tool，不得把一次性诊断长期留在生产链。
- 查询编译结果时同时检查 error、warning 和独立的 `obsolete`/`deprecated` 警告；不得以 error 为零宣称编译干净，也不得用 warning suppression 代替修复。
