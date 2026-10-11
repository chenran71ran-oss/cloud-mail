# 安全说明 / Security

本仓库是个人维护分支，没有承诺固定的安全响应时间或长期支持窗口。部署者应关注 [上游仓库](https://github.com/maillab/cloud-mail) 的安全更新与第三方依赖变化。

## 报告漏洞

不要在公开 Issue 或 Pull Request 中发布可利用漏洞细节、真实密钥、私人邮件或用户数据。

若本仓库的 [Security 页面](https://github.com/chenran71ran-oss/cloud-mail/security) 显示 **Report a vulnerability**，请使用 GitHub 私密漏洞报告。该入口取决于仓库设置，本文不保证它已启用。

若没有私密入口，可创建只请求建立私密联系渠道的 Issue，不包含漏洞细节。对影响上游的通用问题，请检查上游提供的安全报告渠道。

报告应说明受影响的提交或版本、影响范围、最小复现与修复建议。复现时使用测试账户和测试数据。

## 凭据泄露

若 API Token、JWT 密钥、服务 Token 或密码泄露，应先撤销或轮换对应凭据，再处理公开记录。仅删除文本不能撤销已经泄露的凭据，也不能保证 Git 历史中的副本消失。

当前初始化接口将密钥放在 URL 路径中。避免在公开日志、截图、浏览器历史分享或代理记录中泄露完整地址。

## Deployment responsibility

Keep credentials and message backups private. Use separate development resources, review database migrations before upgrading, and verify enabled registration and sending permissions.

Do not post exploitable vulnerability details or private mailbox data publicly. Use private vulnerability reporting if GitHub exposes it; otherwise request a private contact channel without including sensitive details.
