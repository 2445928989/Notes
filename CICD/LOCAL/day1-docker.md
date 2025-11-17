
# 1.本地安装docker

使用wsl，在本地运行

```bash
sudo apt install docker.io
```

验证运行

```bash
docker version
```

不报错就是安装好了

# 2.测试是否工作

测试docker是否工作，在本地运行

```bash
sudo docker run hello-world // 此处不sudo没权限
```

如果你看到类似如下内容

```bash
docker: Error response from daemon: Get "https://registry-1.docker.io/v2/": context deadline exceeded
```

那就是网不行，你得去配镜像

具体地，向`/etc/docker/daemon.json`(如果没有，创建之)中写入以下内容
```json
{
    "registry-mirrors": [
        "https://registry.docker-cn.com",
        "https://docker.m.daocloud.io",
        "https://hub-mirror.c.163.com"
    ]
}
```

然后重启docker

```bash
sudo systemctl daemon-reload 
sudo systemctl restart docker
```

如果正常的话，你运行上面写的`sudo docker run hello-world`命令会看到

```bash
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

这就是跑成功了

# 3.容器化

这是我在本地做的，用于学习docker怎么容器化

