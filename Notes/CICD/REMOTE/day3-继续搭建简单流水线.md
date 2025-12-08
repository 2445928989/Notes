接上文 [[day2-尝试搭建简单的流水线]]
### 3. 配置 Webhook

#### 1. 在 Jenkins 中创建一个 pipeline 任务

如图，注意选择项目类型为 Pipeline 流水线

![[Pasted image 20251204161630.png]]

#### 2 .配置任务并获取 Webhook URL

在 Jenkins 中配置任务，当 gitlab 更新时自动重新构建

![[Pasted image 20251204163416.png]]

生成一个 secret token

![[Pasted image 20251208152502.png]]

在 GitLab 里设置 webhook 的 URL 和 secret token

![[Pasted image 20251208152646.png]]

注意配置触发事件

![[Pasted image 20251204165048.png]]

关掉 SSL 认证

![[Pasted image 20251208152658.png]]