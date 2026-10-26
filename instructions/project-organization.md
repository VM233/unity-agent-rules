# 项目目录组织通用细则

- 脚本、资源或目录组织工作读取已安装的 `$unity-cli-project-review` skill，具体默认策略、配置示例与分类流程见其 `references/folder-organization.md`。安装入口为 [VM233/unity-cli-project-review-skill](https://github.com/VM233/unity-cli-project-review-skill)。
- 通用审查唯一 owner 为 VMUnityAutomation 的 `asset/review-folder-organization`，通过当前正式 CLI catalog 发现；项目现有验证入口组合其 typed result，不复制扫描器。默认覆盖 Assets 根层数量，以及存在的脚本、Resources/GameResources 等资源范围；检测目录、递归边界、阈值与排除由工具默认策略或项目配置持有。
- 外部插件、供应商资源及其附带代码/Resources 保留发布布局。Assets 根层数量盘点不授权进入或重排外部目录；自有范围内存在第三方子树时，按来源证据配置排除，不能仅凭目录名字猜所有权。
- 日常新增或调整命中范围的脚本/资源后执行目录审查，对超限目录审查职责和资源分类并记录依据；不将数量告警当作自动搬运命令，不靠数字分箱、抬高阈值或排除自有目录消除问题。资源迁移继续执行原生身份、引用、加载和生成契约。
- 配置错误、范围外请求、不完整扫描和预算溢出必须暴露；报告注明配置来源、实际扫描范围、完整计数与截断状态。零告警不替代代码、美术、行为或视觉验收。本规则不新增后台监控、跨项目修改或发布权限。
