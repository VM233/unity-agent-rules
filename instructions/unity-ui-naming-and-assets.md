# Unity UI 语义命名与视觉素材通用细则

## 共享范围与权限边界

- 新增、重命名、移动或删除 UI Toolkit/UXML 元素 `name`、USS `#Name` selector、UI Prefab/GameObject 节点、`VisualElementPath`、运行时查询目标，或与这些 UI 概念对应的 Texture、Sprite、SpriteAtlas、Material 等视觉素材时，必须读取本文件。
- 用户当前要求和消费项目根 `AGENTS.md` 决定写入及产品范围；写入 `Assets` 或使用 Unity/Editor 工具时同时读取 `.agents/shared-rules/instructions/unity-editor-safety.md`，并继续执行消费项目的 UI、图片和资源专项细则。任务相关的刷新、读回及视觉验证按共享 Editor 安全细则执行，不另行要求验证授权。
- 项目专属目录、Title Case/casing、Sprite 导入参数、像素规范、业务词汇和页面结构留在消费项目；本文件只定义跨项目的语义命名闭环。

## UI 名称与专用素材必须同步迁移

- UI 语义改名是一个原子迁移，不是只改 UXML、Prefab 或脚本中的单个标识。动手前明确旧语义与新语义，并审计完整链路：UXML `name`、USS selector/class、Prefab/GameObject 节点、`VisualElementPath`、`Q`/查询与生成器、`AttributeOverrides`、序列化字符串，以及该 UI 概念专用的文件、Texture/Sprite/SpriteAtlas/Material 名称和全部引用。
- 只要视觉素材专属于被改名的 UI 概念，或其文件名、Unity Object 名、Sprite 子资源名仍包含旧语义，就必须在同一任务按项目命名规范改成新语义。Normal、Hover/Highlighted、Pressed/Active、Selected/Checked、Disabled 等整组状态素材必须一起处理，不得只改当前显示的一张。
- 真正由多个无关 UI 复用、且名称已经表达中性视觉职责的 canonical 素材保持中性，不得因单个 consumer 改名而业务化。若共享素材名称仍绑定旧业务语义，先审计全部 consumer，再把它迁移为对所有 consumer 都准确的共享语义；不得以“共享”为由保留已经错误或误导的旧名。
- Unity 资源改名或移动必须保留现有 `.meta` GUID；多 Sprite、SpriteAtlas 或其他子资源还必须保留有效 local file ID/引用，并同步内部 Object/Sprite 名。使用 Unity 资源 API 或能证明这些不变量的权威资源工具，不得删除后重导、复制成新 GUID、手写 `.meta`，也不得只交换 consumer 引用来掩盖命名失配。
- 同步更新所有 `project://database` URI/fragment、UXML/USS、Prefab/Scene 序列化引用、Addressable/配置引用及代码查询；删除旧别名、旧文件和只为兼容旧名保留的分支。除非用户当前明确要求兼容，否则不得留下新旧双轨。

## 已授权的设计期预览必须覆盖代表性状态

- 本节本身不授权在生产 UXML 中新增或保留 mock、placeholder 或运行时生成内容。只有用户当前明确要求或消费项目专项规则已经允许，且运行时 owner 会在消费前无条件清空并从当前权威配置完整重建的设计期子树，才可作为 `runtime-replaced` 预览保留；该子树必须紧邻带有用途与清空 owner 的可搜索标记。
- 运行时生成逻辑、序列化配置、template/container 结构、有限语义 class、USS selector、布局样式或对应状态素材发生变化时，必须在同一任务同步维护已授权预览。预览使用固定、可复现但有意多样的样本，覆盖当前配置的每个相关 class/variant 及有意义的组合状态；用户所说的“随机”或“多样”预览指 authoring 中的确定性样本，不得为此引入运行时随机状态。
- 预览 class 与状态只用于 UI Builder 和源码审查，不得被运行时读取为业务数据、默认内容、配置来源或 fallback。交付前同时回读配置 producer、运行时增删 class 的 consumer、USS selector、预览样本和清空/重建入口；只更新其中一处不算完成。

## 冲突与完成门禁

- 消费项目可以用更具体的命名格式、目录与素材状态规则收紧本文件，但不能允许 UI 语义、专用素材文件名和内部资源名彼此失配。用户当前明确指定的产品语义优先；共享素材所有权与中性命名边界仍必须用实际 consumer 证明，不能凭文件所在目录或单一引用猜测。
- 用户明确限制与数据安全边界高于任何“读回”“验收”要求。正常语义迁移按实际引用、内部名和可见状态风险直接完成必要的 Unity 读回与视觉检查，不因未单独点名验证而停止；纯显示文案小改不自动升级到构建或整场 Play Mode。因明确限制或环境阻碍未执行时，报告准确范围和原因。
- 交付前对旧、新语义做双向搜索，并逐项核对“UI 标识 -> selector/路径/查询 -> 专用素材文件 -> 内部 Object/Sprite 名 -> 序列化 consumer”。存在专用素材时，diff 必须同时体现 UI 与素材迁移；未改素材时必须能证明没有专用素材，或所引用素材是名称仍准确的中性 canonical 资源。
- 对实际发生的资源迁移，读回确认 GUID 未变、内部名与新语义一致、local file ID/引用仍解析到同一资源、全部状态素材已迁移且旧资源不存在。静态 YAML、路径替换或单个 consumer 显示正确，均不能单独证明资源迁移完成。
