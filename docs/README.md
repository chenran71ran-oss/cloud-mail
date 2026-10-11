# 文档中心

[返回首页](../README.md) · [English overview](../README-en.md)

本目录整理此分支的部署与维护流程。功能介绍以仓库源码和 [上游文档](https://doc.skymail.ink/) 为依据；第三方平台的界面、配额和可用功能可能变化。

## 从哪里开始

| 场景 | 文档 |
| --- | --- |
| 第一次部署 | [部署指南](deployment.md) |
| 不知道某个变量怎么填 | [配置说明](configuration.md) |
| 构建失败、绑定失败或收不到邮件 | [故障排查与 FAQ](troubleshooting.md) |
| 已部署，准备备份或升级 | [维护指南](maintenance.md) |
| 想改代码或文档 | [贡献指南](../CONTRIBUTING.md) |

建议顺序：准备资源 → 配置绑定与密钥 → 部署 → 初始化 → 注册管理员 → 配置 Email Routing → 收信验证 → 可选发信。

## 文档约定

- `example.com`、资源 ID 占位符都是示例，需要替换。
- “网站域名”指 Web 界面地址；“邮箱域名”指邮箱地址中 `@` 后的域名。
- Cloudflare Workers Builds 与 GitHub Actions 是两种部署路径，使用的配置文件和变量不同。
- 截图来自上游，不代表此分支实例的实时状态。
- `doc/` 保存图片资源，`docs/` 保存本分支的文字文档。

## 官方参考

- [上游项目](https://github.com/maillab/cloud-mail)
- [上游使用文档](https://doc.skymail.ink/)
- [Cloudflare Workers](https://developers.cloudflare.com/workers/)
- [Workers Builds 配置](https://developers.cloudflare.com/workers/ci-cd/builds/configuration/)
- [Cloudflare Email Routing](https://developers.cloudflare.com/email-routing/)
- [Resend 域名配置](https://resend.com/docs/dashboard/domains/introduction)
