# Unity UI 制作与审查入口

- 创建、检查或修复 Unity 玩家界面、UI Toolkit/UXML/USS、自定义控件、布局、文本、输入或表现时，读取并使用已安装的 `$unity-game-ui-review` skill。安装入口为 [VM233/unity-game-ui-review-skill](https://github.com/VM233/unity-game-ui-review-skill)。
- 通用 UI 工作流、布局与原生控件、字号与多语言文化格式、设计期预览、手势、安全区、状态和画面验收由该 skill 及其按需 reference 唯一维护；本文件及消费项目 overlay 不复制正文。
- 消费项目只保留实际产品范围、资源路径、控件/模板与绑定事实、项目工具参数和真实例外。需要实现 UI Toolkit 时，按 skill 路由读取其 authoring reference；正式命令参数仍以当前 catalog 为准。
- 写入与验证权限、Editor/窗口和用户数据保全继续执行 `unity-editor-safety.md`；代码、序列化、素材和本地化数据命中各自专项细则。本入口不新增操作或发布权限。
