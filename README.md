<p align="center">
  <img src="doc/demo/logo.png" width="88" alt="Cloud Mail 标志">
</p>

<h1 align="center">Cloud Mail</h1>

<p align="center">让自己的域名，拥有自己的邮箱。</p>
<p align="center">运行于 Cloudflare Workers 的自托管邮箱，提供现代 Web 界面、域名收信与可配置的发信服务。</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-MIT-22c55e" alt="MIT 许可证"></a>
  <a href="https://developers.cloudflare.com/workers/"><img src="https://img.shields.io/badge/Cloudflare-Workers-f38020" alt="Cloudflare Workers"></a>
  <a href="https://vuejs.org/"><img src="https://img.shields.io/badge/Vue-3-42b883" alt="Vue 3"></a>
  <a href="https://hono.dev/"><img src="https://img.shields.io/badge/API-Hono-e36002" alt="Hono API"></a>
</p>

<p align="center">
  简体中文 · <a href="README-en.md">English</a>
  <br>
  <a href="docs/deployment.md">开始部署</a> ·
  <a href="docs/README.md">文档中心</a> ·
  <a href="docs/troubleshooting.md">常见问题</a> ·
  <a href="CONTRIBUTING.md">参与贡献</a>
</p>

> 本仓库是 [maillab/cloud-mail](https://github.com/maillab/cloud-mail) 的个人维护分支，由 [chenran71ran-oss](https://github.com/chenran71ran-oss/cloud-mail) 维护部署配置与文档。核心功能来自上游，原作者署名与 MIT 许可证完整保留。

## 为什么选择 Cloud Mail

用自己的域名创建邮箱，通过浏览器管理邮件，无需自行维护传统邮件服务器。前端与 API 部署到同一个 Worker，邮件经 Cloudflare Email Routing 进入应用，数据存储在 D1 与 KV 中。

适合个人域名邮箱、小型团队和自托管学习场景。使用前请了解 Cloudflare 与发信服务的配额、收费和域名要求。

## 功能一览

| 能力 | 说明 |
| --- | --- |
| 域名邮箱 | 多邮箱管理、收件、搜索、收藏与附件处理 |
| 邮件发送 | 集成 Resend；发信需要单独验证域名与配置服务 |
| 管理后台 | 用户管理、角色权限、邮箱数量和发信额度控制 |
| 响应式界面 | Vue 3 与 Element Plus，适配桌面和移动浏览器 |
| 通知与转发 | Telegram 通知、邮件转发等可选集成 |
| 自动化能力 | 开放 API、Workers AI 验证码识别、数据统计 |
| 网站设置 | 标题、登录背景与 Turnstile 人机验证 |

可选功能需要相应绑定、服务凭据和后台开关。收信成功并不代表发信、AI 或通知已经启用。

## 界面预览

以下为上游提供的界面截图，具体页面随版本与配置变化。

| 界面预览 1 | 界面预览 2 |
| :---: | :---: |
| ![Cloud Mail 界面预览 1](doc/demo/demo1.png) | ![Cloud Mail 界面预览 2](doc/demo/demo2.png) |

<details>
<summary>查看更多截图</summary>

| 界面预览 3 | 界面预览 4 |
| :---: | :---: |
| ![Cloud Mail 界面预览 3](doc/demo/demo3.png) | ![Cloud Mail 界面预览 4](doc/demo/demo4.png) |

</details>

## 开始使用

1. 准备托管在 Cloudflare 的域名，以及 Workers、D1 和 KV。
2. 按 [部署指南](docs/deployment.md) 配置资源绑定、运行时密钥与构建命令。
3. 初始化数据库并注册管理员，在 Email Routing 中将收件规则指向 Worker。
4. 从外部邮箱发送测试邮件，确认收件后再配置发信等可选功能。

**网站域名与邮箱域名可以不同**：例如网站使用 `mail.example.com`，邮箱使用 `name@example.com`。

本分支的 [wrangler.toml](mail-worker/wrangler.toml) 包含维护者自己的部署资源。部署到其他账户时，必须替换域名、管理员地址、D1 ID 与 KV ID。密钥通过 Cloudflare Secrets 配置。

## 文档导航

| 你想做什么 | 阅读入口 |
| --- | --- |
| 了解运行架构与功能边界 | [架构说明](docs/architecture.md) |
| 从零部署 | [部署指南](docs/deployment.md) |
| 了解绑定、域名与密钥 | [配置说明](docs/configuration.md) |
| 解决部署或收信错误 | [故障排查与 FAQ](docs/troubleshooting.md) |
| 检查是否真正完成上线 | [验收清单](docs/acceptance.md) |
| 同步上游与维护实例 | [维护指南](docs/maintenance.md) |
| 提交问题或代码 | [贡献指南](CONTRIBUTING.md) |
| 报告安全问题 | [安全说明](SECURITY.md) |

完整功能说明与集成教程可参考 [上游文档](https://doc.skymail.ink/)；上游演示站为 [skymail.ink](https://skymail.ink)。演示站由上游运营，请勿用于存放私人邮件。

## 技术与目录

| 路径 | 内容 |
| --- | --- |
| [mail-worker/](mail-worker/) | Hono API、邮件处理、Drizzle 数据访问与 Wrangler 配置 |
| [mail-vue/](mail-vue/) | Vue 3、Vite、Element Plus 与前端页面 |
| [docs/](docs/) | 本分支的部署、配置、排错与维护文档 |
| [doc/](doc/) | 上游截图和图片资源 |
| [.github/](.github/) | 部署工作流、Issue 与 Pull Request 模板 |

运行平台为 Cloudflare Workers；D1 保存结构化数据，KV 用于缓存及默认附件存储，R2 可按需要配置。发信与收信是独立链路，详见 [配置说明](docs/configuration.md)。

## 贡献与支持

欢迎通过本仓库的 [Issues](https://github.com/chenran71ran-oss/cloud-mail/issues) 报告分支问题，或按 [贡献指南](CONTRIBUTING.md) 提交改进。涉及上游通用功能时，请同时关注 [上游仓库](https://github.com/maillab/cloud-mail)。

感谢上游作者和贡献者。上游社区与支持入口见 [上游文档](https://doc.skymail.ink/)。

## 许可证

采用 [MIT License](LICENSE)。原始版权声明为 `Copyright (c) 2025 aslost`，分发时请保留许可证与版权声明。
