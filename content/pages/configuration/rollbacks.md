---
pcx_content_type: concept
title: Rollbacks
---

# 回滚

回滚功能可让你立即将项目恢复到之前的生产部署。

任何已成功构建的生产部署都是有效的回滚目标。当项目已回滚到之前的部署时，仍可回滚到比当前版本更新的部署。请注意，预览部署不是有效的回滚目标。

要执行回滚，请转到页面项目中的**部署**。浏览**所有部署**列表，选择所需目标的三个虚线操作菜单。选择**回滚到此部署**，出现一个确认窗口。确认后，项目的生产部署将立即更改。

![可用于回滚的页面项目部署](/images/pages/platform/rollbacks.png)

## 相关资源

- [预览部署](/pages/configuration/preview-deployments/)
- [分支部署控制](/pages/configuration/branch-build-controls/)