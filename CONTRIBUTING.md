# 贡献指南 / Contributing

欢迎改进 Cloud Mail 的文档、部署流程和功能。本仓库是 [maillab/cloud-mail](https://github.com/maillab/cloud-mail) 的个人维护分支。

## 选择合适的入口

- 此分支的文档与部署问题：在 [本仓库 Issues](https://github.com/chenran71ran-oss/cloud-mail/issues) 提交。
- 上游通用功能或缺陷：先查看上游是否已有相关讨论，避免重复提交。
- 安全漏洞与凭据泄露：参阅 [SECURITY.md](SECURITY.md)，不要公开敏感细节。

## 提交 Issue

使用仓库提供的 Bug、功能建议或部署求助模板。描述实际行为、预期行为、复现步骤、提交 SHA 与部署方式。日志、截图和邮件内容请脱敏。

功能建议应说明使用场景与希望解决的问题，不只列出一个功能名称。

## 本地准备

现有部署工作流使用 Node.js 24 与 pnpm 11。对齐这些版本后，在仓库根目录安装依赖：

```sh
pnpm --prefix mail-worker install --frozen-lockfile
pnpm --prefix mail-vue install --frozen-lockfile
```

前端构建：

```sh
pnpm --prefix mail-vue run build
```

本地前端与 Worker 开发需要检查对应的 `.env` 和 Wrangler 开发配置，使用独立测试资源。不要直接复用生产数据。

**注意：** 当前 `mail-worker/package.json` 中的 `test` 脚本执行远程 Wrangler 部署，并不是单元测试命令。运行前必须确认目标配置与账户。

## Pull Request

1. 创建独立分支，一次 PR 聚焦一个明确问题。
2. 保持原有代码风格，避免无关格式化。
3. 修改行为时同步相关文档。
4. 填写 PR 模板，描述变更、验证结果和需要注意的迁移。
5. 不提交生成的 `dist`、依赖目录、真实密钥或私人邮件。

文档改动检查相对链接、标题、示例参数和中英文首页一致性。前端改动至少完成构建与相关页面验证；后端改动说明实际验证方式。未执行的检查如实注明。

提交信息建议采用 `docs:`、`fix:`、`feat:` 或 `chore:` 前缀。

## Attribution

Retain the original MIT license and author attribution. This fork maintains its own deployment configuration and documentation; it does not claim authorship of the upstream application.

Issues and pull requests may be written in Chinese or English. Include a clear reproduction, expected behavior, commit SHA, deployment method and sanitized logs. Check [SECURITY.md](SECURITY.md) before reporting sensitive issues.
