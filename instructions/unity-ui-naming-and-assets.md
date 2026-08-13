# Unity UI 语义命名与视觉素材通用细则

## 共享范围与权限边界

- 新增、重命名、移动或删除 UI Toolkit/UXML 元素 `name`、USS `#Name` selector、UI Prefab/GameObject 节点、`VisualElementPath`、运行时查询目标，或与这些 UI 概念对应的 Texture、Sprite、SpriteAtlas、Material 等视觉素材时，必须读取本文件。
- 用户当前要求和消费项目根 `AGENTS.md` 决定写入、Unity 操作与验证权限；写入 `Assets` 或使用 Unity/Editor 工具时同时读取 `.agents/shared-rules/instructions/unity-editor-safety.md`，并继续执行消费项目的 UI、图片和资源专项细则。本文件不新增刷新、编译、Play Mode、构建、视觉检查或输入自动化权限。
- 项目专属目录、Title Case/casing、Sprite 导入参数、像素规范、业务词汇和页面结构留在消费项目；本文件只定义跨项目的语义命名闭环。

## UI 名称与专用素材必须同步迁移

- UI 语义改名是一个原子迁移，不是只改 UXML、Prefab 或脚本中的单个标识。动手前明确旧语义与新语义，并审计完整链路：UXML `name`、USS selector/class、Prefab/GameObject 节点、`VisualElementPath`、`Q`/查询与生成器、`AttributeOverrides`、序列化字符串，以及该 UI 概念专用的文件、Texture/Sprite/SpriteAtlas/Material 名称和全部引用。
- 只要视觉素材专属于被改名的 UI 概念，或其文件名、Unity Object 名、Sprite 子资源名仍包含旧语义，就必须在同一任务按项目命名规范改成新语义。Normal、Hover/Highlighted、Pressed/Active、Selected/Checked、Disabled 等整组状态素材必须一起处理，不得只改当前显示的一张。
- 真正由多个无关 UI 复用、且名称已经表达中性视觉职责的 canonical 素材保持中性，不得因单个 consumer 改名而业务化。若共享素材名称仍绑定旧业务语义，先审计全部 consumer，再把它迁移为对所有 consumer 都准确的共享语义；不得以“共享”为由保留已经错误或误导的旧名。
- Unity 资源改名或移动必须保留现有 `.meta` GUID；多 Sprite、SpriteAtlas 或其他子资源还必须保留有效 local file ID/引用，并同步内部 Object/Sprite 名。使用 Unity 资源 API 或能证明这些不变量的权威资源工具，不得删除后重导、复制成新 GUID、手写 `.meta`，也不得只交换 consumer 引用来掩盖命名失配。
- 同步更新所有 `project://database` URI/fragment、UXML/USS、Prefab/Scene 序列化引用、Addressable/配置引用及代码查询；删除旧别名、旧文件和只为兼容旧名保留的分支。除非用户当前明确要求兼容，否则不得留下新旧双轨。

## 冲突与完成门禁

- 消费项目可以用更具体的命名格式、目录与素材状态规则收紧本文件，但不能允许 UI 语义、专用素材文件名和内部资源名彼此失配。用户当前明确指定的产品语义优先；共享素材所有权与中性命名边界仍必须用实际 consumer 证明，不能凭文件所在目录或单一引用猜测。
- 安全与验证权限上限高于任何“读回”“验收”要求。未获相应 Unity/视觉验证授权时，只完成允许的实现与静态检查，并明确标记未执行项；不得为了证明改名而自行启动 Unity、构建、Play Mode 或截图。
- 交付前对旧、新语义做双向搜索，并逐项核对“UI 标识 -> selector/路径/查询 -> 专用素材文件 -> 内部 Object/Sprite 名 -> 序列化 consumer”。存在专用素材时，diff 必须同时体现 UI 与素材迁移；未改素材时必须能证明没有专用素材，或所引用素材是名称仍准确的中性 canonical 资源。
- 在当前授权允许资源读回时，确认 GUID 未变、内部名与新语义一致、local file ID/引用仍解析到同一资源、全部状态素材已迁移且旧资源不存在。静态 YAML、路径替换或单个 consumer 显示正确，均不能单独证明资源迁移完成。
