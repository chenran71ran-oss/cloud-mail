# 维护指南

[文档中心](README.md) · [贡献指南](../CONTRIBUTING.md)

## 升级前

记录当前提交 SHA、Worker 名称、D1 / KV ID、运行时变量、DNS 与路由设置。备份 D1，并根据附件存储方式备份 KV 或 R2 中的数据；仅备份代码不能恢复邮件。

可参考 [D1 导出文档](https://developers.cloudflare.com/d1/best-practices/import-export-data/)。备份中可能包含邮件、密码哈希与服务凭据，应保存在私有位置。

## 同步上游

在工作目录干净的本地仓库中操作，已有 `upstream` 远程时跳过添加步骤：

```sh
git remote add upstream https://github.com/maillab/cloud-mail.git
git fetch upstream
git switch -c chore/sync-upstream
git merge upstream/main
```

合并时重点审查：

| 文件或配置 | 原因 |
| --- | --- |
| `mail-worker/wrangler.toml` | 维护者的域名与资源绑定必须保持有效 |
| `mail-worker/wrangler-action.toml` | Actions 使用独立配置模板 |
| `mail-vue/.env.release` | API 路径和构建输出目录 |
| 初始化与迁移代码 | 可能修改数据库结构或现有数据 |
| README 与 `docs/` | 保留分支说明并同步功能变化 |
| LICENSE | 保留上游版权与许可 |

解决冲突后构建前端，审查变更并通过 Pull Request 合并。不要使用强制重置主分支的方式覆盖本实例配置。

## 部署后

验证网站、登录、现有邮件、附件、新邮件收取以及已启用的发信功能。不要把初始化接口用作轮询健康检查。

Worker 回滚只回滚代码版本，不能自动撤销已经执行的数据库迁移。回滚前确认旧代码能否兼容当前数据库结构。

## 当前部署自动化

仓库保留现有 GitHub Actions。它包含资源查找或创建、部署、数据库初始化与运行记录清理步骤；如准备启用或调整，先审查 [工作流源码](../.github/workflows/deploy-cloudflare.yml)。

维护实例尽量固定使用一种部署路径，显式指定已有资源 ID。更换资源、域名或密钥时，记录变更并单独验证，不要将它们与大版本升级同时进行。
