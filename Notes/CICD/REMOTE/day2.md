清理一下思路，我们要做的事情其实是借助学校的 GitLab 平台，完成一条自动化流水线

这条流水线的核心是：**当代码被推送到学校的GitLab时，自动触发我云服务器上的Jenkins，由Jenkins 完成 Docker 镜像的构建和推送，并最终部署到 K8s 集群中**

```mermaid
flowchart TD
    A[开发者] --> B[推送代码git push]
    B --> C[学校GitLab平台<br>指令中心]
    C -- Webhook 触发 --> D[我的云服务器: Jenkins<br>自动化引擎]
    
    D -- 1.拉取代码 --> C
    D -- 2.执行构建脚本 --> E[我的云服务器: Docker<br>打包工具]
    E -- 3.构建镜像 --> F[镜像仓库<br>如Docker Hub]
    D -- 4.调用kubectl --> G[我的云服务器: K8s集群<br>部署平台]
    G -- 5.拉取镜像 --> F
    G -- 6.运行新版本 --> H[应用成功部署]
```
# 1.配环境

## 1.Docker

```bash
sudo apt install docker.io
```

配置镜像源，详见 [[day1-docker#2.测试是否工作]]
## 2. Kuberetes（使用 k3s）

```bash
sudo curl -sfL https://rancher-mirror.rancher.cn/k3s/k3s-install.sh | INSTALL_K3S_MIRROR=cn sh -s - server \
    --system-default-registry "registry.cn-hangzhou.aliyuncs.com" \
    --write-kubeconfig ~/.kube/config \
    --write-kubeconfig-mode 666 \
    --disable traefik
```

验证

```bash
sudo systemctl status k3s
#检查集群节点状态
sudo k3s kubectl get nodes
#查看组件是否健康
sudo k3s kubectl get componentstatuses
```

## 3. Jenkins

使用 docker 来安装 Jenkins
```bash
# 创建 Jenkins 数据卷，用于持久化配置
docker volume create jenkins_data

# 运行 Jenkins 容器
docker run -d \
  --name jenkins \
  --restart=always \
  -u root \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_data:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /usr/local/bin/k3s:/usr/local/bin/k3s \
  jenkins/jenkins:lts-jdk17

#查看密码
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

访问 `http://<IP>:8080` 来访问 Jenkins (注意要设置云服务器的防火墙使 8080 端口准入)

如图是正在下载社区推荐插件

![[Pasted image 20251201153700.png]]

配置 Jenkins 镜像源 `https://mirrors.huaweicloud.com/jenkins/updates/update-center.json`

