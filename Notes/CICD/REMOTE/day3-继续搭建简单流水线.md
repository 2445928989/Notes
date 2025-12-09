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

Jenkinsfile 需要 dockerfile，这里我使用了我以前写的一个小项目 ([[day1-部署示例项目到服务器并远程访问]]) 同款

我写了一个 Jenkinsfile 用于配置流水线

```groovy
// Jenkinsfile
pipeline {
    agent any // 使用任何可用的Jenkins代理（就是你的服务器）

    environment {
        // 定义镜像名称，使用你的私有仓库地址和项目名
        IMAGE_NAME = '172.16.62.47:5000/my-first-api:build-${BUILD_NUMBER}'
    }

    stages {
        // 第一阶段：构建Docker镜像
        stage('构建 Docker 镜像') {
            steps {
                script {
                    echo 'building image...'
                    echo '  image name:${IMAGE_NAME}'
                    
                    // 核心命令：使用你项目里现成的 Dockerfile 构建
                    sh 'docker build -t ${IMAGE_NAME} .'
                    
                    echo 'image build ok'
                }
            }
        }
        
    }

    post {
        success {
            echo 'build success!'
        }
        failure {
            echo 'build fail...'
        }
    }
}
```

扔到项目根目录下然后 commit 测试流水线是否工作，发现无法正常工作

![[Snipaste_2025-12-09_11-16-19.png]]

看了一下，说是要配置 pipeline ，让他用 gitlab 项目里的 jenkinsfile

但是这里有一个问题，gitlab 是在学校内网里跑的，而我的服务器没法直接访问学校内网

我尝试使用 vpn 连接到学校内网，但我的云服务器并没有图形化界面，而且 ping 了一下 vpn 服务器发现根本 ping 不通啊

所以只能退而求其次，直接把 jenkinsfile 写在 jenkins 的 pipeline 设置里面

![[Pasted image 20251209172620.png]]

然后我尝试 build，发现报错

![[Pasted image 20251209172641.png]]

原因是在 docker 容器内运行的 jenkins 无法通过 localhost 连接到 k3s 

```bash
sudo cp /etc/rancher/k3s/k3s.yaml /etc/rancher/k3s/k3s.yaml.backup
sudo sed -i "s/127.0.0.1/172.16.62.47/g" /etc/rancher/k3s/k3s.yaml
grep server /etc/rancher/k3s/k3s.yaml
docker restart jenkins
```

再次尝试 build，终于成功

![[Pasted image 20251209173101.png]]

然后我们来测试 docker 是否正常工作

```bash
docker exec -it jenkins /bin/bash
docker pull hello-world
docker tag hello-world 172.16.62.47:5000/test-hello:latest
docker push 172.16.62.47:5000/test-hello:latest
curl http://172.16.62.47:5000/v2/_catalog
```

发现推送不了，于是再次返回更改设置

```json
// etc/docker/daemon.json
{
  "registry-mirrors": [
    "https://docker.m.daocloud.io",
    "https://docker.nju.edu.cn",
    "https://mirror.baidubce.com"
  ],
  "insecure-registries": ["172.16.62.47:5000"]
}
```

在主机和 jenkins 容器里都进行这个更改，再次尝试推送，正常工作

