# 配置说明

[文档中心](README.md) · [部署指南](deployment.md)

## 资源绑定

| 绑定名 | 服务 | 作用 |
| --- | --- | --- |
| `db` | D1 | 用户、邮箱、邮件元数据、角色及系统设置 |
| `kv` | KV | 缓存与默认附件存储 |
| `assets` | Workers Assets | 托管前端构建产物 `dist` |
| `ai` | Workers AI | 可选验证码识别等功能 |
| `r2` | R2 | 可选附件存储，需要启用相应配置 |
| `email` | Cloudflare Email sending binding | 可选发信路径，需账户具备相应能力 |

不要把数据库名称当成绑定名。删除并重建同名资源后，ID 会变化，代码配置也必须更新。保留有效旧资源通常比重建更安全。

## 运行时变量

直接使用 `wrangler.toml` 部署时：

| 名称 | 示例 | 配置位置 |
| --- | --- | --- |
| `domain` | `["example.com"]` | TOML `[vars]` 数组 |
| `admin` | `admin@example.com` | TOML `[vars]` 字符串 |
| `jwt_secret` | 随机密钥 | Cloudflare 运行时 Secret |
| `ai_model` | 参考原配置中的模型 | 可选运行时变量 |
| `analysis_cache` | 参考原配置 | 可选统计缓存设置 |

`domain` 是邮箱域名；网站域名在 Worker 自定义域名中配置。`jwt_secret` 区分大小写，初始化时必须与运行时值一致。更换密钥会影响现有登录会话。

## 两种部署路径的区别

| 项目 | Workers Builds / 本地 Wrangler | GitHub Actions |
| --- | --- | --- |
| 配置文件 | `mail-worker/wrangler.toml` | `mail-worker/wrangler-action.toml` |
| 域名配置 | TOML 的小写 `domain` | GitHub 的大写 `DOMAIN` |
| 管理员 | TOML 的 `admin` | GitHub 的 `ADMIN` |
| 身份密钥 | Worker 运行时 `jwt_secret` Secret | GitHub 的 `JWT_SECRET` |
| 资源 ID | TOML 绑定配置 | `D1_DATABASE_ID`、`KV_NAMESPACE_ID` |
| 初始化 | 按部署指南手动执行 | 当前工作流包含初始化步骤 |

GitHub Actions 的其他主要参数：

| 参数 | 说明 |
| --- | --- |
| `CLOUDFLARE_API_TOKEN` | Cloudflare 部署令牌，放 GitHub Secrets |
| `CLOUDFLARE_ACCOUNT_ID` | 部署目标账户 ID |
| `NAME` | Worker 名称，默认 `cloud-mail` |
| `CUSTOM_DOMAIN` | 网站域名，不含 `https://` |
| `DOMAIN` | JSON 数组字符串，例如 `["example.com"]` |
| `ADMIN` | 管理员邮箱地址 |
| `JWT_SECRET` | 身份密钥，放 GitHub Secrets |
| `D1_DATABASE_ID` | 已有 D1 的 ID，建议显式设置 |
| `KV_NAMESPACE_ID` | 已有 KV 的 ID，建议显式设置 |
| `R2_BUCKET_NAME` | 可选 R2 存储桶 |
| `EMAIL` | 当前 Actions 的可选 Cloudflare 发信绑定参数；非空即添加绑定，需确认账户支持 |

上游本轮将 Actions 参数从 `CF_EMAIL` 改为 `EMAIL`。使用 Resend 时保持 `EMAIL` 未设置；不要填写字符串 `false`，因为当前工作流按“是否非空”判断。该变化不影响直接使用 `wrangler.toml` 的 Workers Builds 路径。

完整可选项与替换规则以 [工作流源码](../.github/workflows/deploy-cloudflare.yml) 为准。不要把 GitHub 部署令牌当成应用登录密码。

## 发信与收信

- **收信**：Email Routing → Worker → 应用邮箱。需要域名 DNS、收件规则和应用账户。
- **发信**：单独配置 Resend 等发送服务，完成发信域名验证并在系统设置中启用。服务凭据不要公开。
- **Cloudflare 发信**：源码包含可选 `email` 绑定。能否使用、允许发送的范围和账户限制，请以 [Cloudflare Email 文档](https://developers.cloudflare.com/email-routing/) 及账户实际能力为准。
- **附件**：基础路径可用 KV；R2 与其他存储集成应按上游文档配置，不能只创建存储桶就认为功能已启用。

接收验证码、向外发送邮件、通知转发分别验证。第三方注册平台是否接受自定义域名邮箱由其规则决定。

## 服务选择与切换

站外发信存在 `email` 绑定时，当前源码优先使用 Cloudflare；没有该绑定时才选择 Resend。调用失败不会自动切换到另一个服务。全站内收件人的邮件走站内处理，因此站内互发不能作为外部发信验收。

附件存储按 **完整 S3 配置 → R2 绑定 → KV** 的优先级选择。更换存储后端不会自动迁移已有附件。详细源码对应关系见 [架构说明](architecture.md)，上线检查见 [验收清单](acceptance.md)。

## 私有配置

本分支保存了维护者自己的域名与资源 ID，便于其持续部署。这些值不是通用模板。对外复制时替换它们，不要复用其他账户资源。

API Token、JWT 密钥、邮箱密码、Resend Token、Turnstile Secret 和通知凭据应保持私密。工单中的日志也需要脱敏。

## 站内邮件转发绑定

自 2026-10-27 同步起，`wrangler.toml` 中的 `forward` 是独立的 `send_email` 绑定，用于将站内邮件转发到已验证的目标邮箱。它不替代 Resend 或 `email` 发信渠道。还需在应用中开启转发并设置规则，详见 [更新说明](upstream-sync-2026-10-27.md)。
