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

测试 webhook 连通性

![[Pasted image 20251208153251.png]]

Return 200，连通

#### 3. 配置 Jenkins 流水线

首先，我们尝试更新 Gitlab 中的项目代码，测试一下流水线，我更改了一下 README.md 然后 commit

转到 Jenkins

![[Snipaste_2025-12-08_15-37-09.png]]

可以发现 build 失败了，看看终端输出

![[Snipaste_2025-12-08_15-39-04.png]]

报错说 No flow definition, cannot run

因为我并没有配置流水线，因此我们需要配置流水线

##### 1. 编写 Jenkinsfile
