# 部署指南

[文档中心](README.md) · [配置说明](configuration.md) · [故障排查](troubleshooting.md)

本文以 **Cloudflare Workers Builds 连接 GitHub** 为主要路径。已正常运行的实例无需重新创建数据库；版本升级涉及新字段时，需先备份并按 [升级说明](upstream-sync-2026-10-24.md) 执行相应迁移。

## 目录

- [准备资源](#准备资源)
- [配置 Worker](#配置-worker)
- [构建与部署](#构建与部署)
- [初始化与管理员](#初始化与管理员)
- [接入收信](#接入收信)
- [验证结果](#验证结果)
- [其他部署方式](#其他部署方式)

## 准备资源

准备 Cloudflare 账户、已托管 DNS 的邮箱域名，以及有推送权限的 GitHub 仓库。

1. 创建 D1 数据库，记录数据库名称与 ID。
2. 创建 KV 命名空间，记录 ID。
3. 在仓库中编辑 [mail-worker/wrangler.toml](../mail-worker/wrangler.toml)，将 D1、KV、邮箱域名与管理员地址替换为自己的资源。
4. 如需 R2，再创建存储桶并配置；基础部署先使用 D1 与 KV。

资源名称、资源 ID、代码绑定名是三件不同的事。绑定名必须保持为 `db` 和 `kv`。本仓库的现有 ID 属于维护者实例，不可直接用于其他账户。

## 配置 Worker

以下是需要替换的配置片段，**不是完整配置文件**。保留原文件中的入口、静态资源、AI、定时任务等其他设置。

```toml
[[d1_databases]]
binding = "db"
database_name = "your-database-name"
database_id = "YOUR_D1_DATABASE_ID"

[[kv_namespaces]]
binding = "kv"
id = "YOUR_KV_NAMESPACE_ID"

[vars]
domain = ["example.com"]
admin = "admin@example.com"
```

部署目标名称应与 `name = "cloud-mail"` 一致；如果改名，Cloudflare 目标 Worker 也需要对应。

在 Worker 的运行时“变量和密钥 / Variables and Secrets”中添加：

| 类型 | 名称 | 用途 |
| --- | --- | --- |
| Secret | `jwt_secret` | 身份签名与数据库初始化验证 |

使用随机、足够长的字符串，例如本地生成 32 字节十六进制值。不要把真实密钥写入 TOML、README、Issue 或截图。运行时密钥与构建环境变量是不同的配置位置。

## 构建与部署

在 Cloudflare 中连接此 GitHub 仓库，选择 `main` 分支并配置：

| 项目 | 值 |
| --- | --- |
| 根目录 / Root directory | `mail-worker` |
| 构建命令 / Build command | `pnpm --prefix ../mail-vue install --frozen-lockfile && pnpm --prefix ../mail-vue run build` |
| 部署命令 / Deploy command | `pnpm run deploy` |

前端构建输出到 `mail-worker/dist`，Worker 的 Assets 配置从这里读取。明确填写构建命令；Workers Builds 对 Wrangler 自定义构建的处理请以 [官方配置文档](https://developers.cloudflare.com/workers/ci-cd/builds/configuration/) 为准。

检查构建环境的 Node.js 与 pnpm 版本。仓库现有 GitHub Actions 使用 Node.js 24 与 pnpm 11，可将其作为对齐基准。

部署完成后：

1. 确认最新部署处于成功状态，而不仅是依赖安装成功。
2. 检查运行时绑定：`db` 指向预期 D1，`kv` 指向预期 KV。
3. 为 Worker 添加网站自定义域名，例如 `mail.example.com`。
4. 打开网站，确认前端和 API 可访问。

## 初始化与管理员

首次部署需要执行数据库初始化。当前源码入口为：

```text
https://mail.example.com/api/init/<jwt_secret>
```

用自己设置的运行时密钥替换占位符，成功响应为 `success`。此接口会执行建表和迁移，不能当作普通健康检查；已有实例升级前先备份。

当前接口把密钥放在 URL 中。仅在自己控制的环境中使用，不要分享完整 URL，并注意浏览器历史及代理日志可能记录它。

随后在网站注册 `admin` 配置指定的邮箱，例如 `admin@example.com`，设置自己的登录密码。登录密码与 `jwt_secret` 是不同用途。若注册被后台策略限制，按上游文档核查注册设置。

## 接入收信

网站能够打开，只说明 Web 服务已运行；收信还需要独立配置。

1. 在邮箱域名 `example.com` 的 Cloudflare 控制台打开 **Email Routing**。
2. 按向导启用路由并配置所需 DNS 记录，避免与已有邮件服务的 MX 记录冲突。
3. 将指定地址的路由，或 **Catch-all** 规则，设置为发送到 `cloud-mail` Worker。
4. 在 Cloud Mail 中创建要接收邮件的邮箱地址。
5. 检查应用的收信开关、用户状态与邮箱权限。

Catch-all 负责把邮件送入 Worker，**不会自动创建应用里的用户或邮箱账户**。网站使用 `mail.example.com` 不代表邮箱必须使用这个子域名。

## 验证结果

| 检查 | 通过标准 |
| --- | --- |
| 构建 | Worker 部署成功，网站正常显示 |
| 初始化 | 接口响应 `success`，管理员可以注册并登录 |
| 路由 | 指定地址或 Catch-all 已启用，目标是正确 Worker |
| 收件 | 外部邮箱发送的测试邮件出现在目标收件箱 |
| 附件 | 使用测试附件确认保存与下载正常 |
| 发件 | 配置发信服务后，单独确认外部收件人收到邮件 |

建议先完成收信，再逐项启用 Resend、R2、AI、Turnstile 或通知功能，便于定位错误。

## 其他部署方式

### 本地 Wrangler

先配置正确的资源与密钥，再从仓库根目录执行：

```sh
pnpm --prefix mail-worker install --frozen-lockfile
pnpm --prefix mail-vue install --frozen-lockfile
cd mail-worker
pnpm wrangler login
pnpm wrangler secret put jwt_secret
pnpm run deploy
```

本地 Wrangler 使用 `wrangler.toml` 的 `[build]` 命令构建前端。登录和部署会操作所选 Cloudflare 账户，请先确认目标。

### GitHub Actions

现有 [部署工作流](../.github/workflows/deploy-cloudflare.yml) 使用 `wrangler-action.toml`，读取 GitHub Secrets / Variables。变量映射见 [配置说明](configuration.md)。

该路径包含资源查找或创建以及数据库初始化。已有实例应显式填写 D1 与 KV ID，防止按名称选到其他资源。不要让两种部署路径使用不同绑定轮流覆盖同一个 Worker。
