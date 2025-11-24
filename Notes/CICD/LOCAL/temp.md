
# Docker 极速精通路线（ACM选手特别版）

既然你是ACM选手，我相信你的学习能力和逻辑思维都很强。这个路线聚焦核心，2-3天就能掌握Docker精髓！

## 🚀 极速学习计划（2-3天）

### Day 1：核心概念速通（4-5小时）

**上午：理论基础（1小时）**
- Docker本质：进程隔离 + 文件系统打包
- 核心三要素：镜像(模板)、容器(进程)、仓库(存储)
- 与虚拟机对比：轻量级、快速启动、资源占用少

**下午：实操速通（3-4小时）**

```bash
# 1. 安装验证（15分钟）
docker --version
docker run hello-world

# 2. 基础命令矩阵（重点掌握）
# 镜像生命周期
docker pull ubuntu:20.04
docker images
docker rmi <image_id>

# 容器生命周期  
docker run -it ubuntu:20.04 /bin/bash  # 交互式
docker run -d nginx                    # 后台运行
docker ps
docker stop <container_id>
docker rm <container_id>

# 3. 端口映射和文件挂载（核心！）
docker run -d -p 8080:80 nginx
docker run -v $(pwd):/app ubuntu:20.04
```

### Day 2：Dockerfile深度掌握（4-5小时）

**重点：理解镜像构建的层次化原理**

```dockerfile
# 最小化Dockerfile示例 - 理解每层含义
FROM python:3.9-slim  # 基础层

WORKDIR /app          # 元数据层

COPY requirements.txt .  # 新文件层
RUN pip install -r requirements.txt  # 新执行层

COPY . .              # 应用代码层

CMD ["python", "app.py"]  # 启动元数据
```

**实践任务（按难度递增）：**

1. **基础镜像**：创建能运行Python脚本的镜像
```dockerfile
FROM python:3.9
COPY solve.py .
CMD ["python", "solve.py"]
```

2. **ACM环境镜像**：创建包含gcc、python、java的竞赛环境
```dockerfile
FROM ubuntu:20.04
RUN apt-get update && apt-get install -y \
    g++ python3 openjdk-11-jdk
```

3. **多阶段构建**（优化技巧）
```dockerfile
# 构建阶段
FROM node:16 as builder
WORKDIR /app
COPY . .
RUN npm run build

# 运行阶段  
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
```

### Day 3：实战应用和高级特性（4-5小时）

**上午：Docker Compose（1.5小时）**
```yaml
version: '3.8'
services:
  web:
    build: .
    ports:
      - "5000:5000"
    depends_on:
      - db
  db:
    image: postgres:13
    environment:
      POSTGRES_PASSWORD: example
```

**下午：结合你的ACM经验（2.5小时）**

1. **评测系统容器化**
```dockerfile
# 评测机镜像
FROM ubuntu:20.04
RUN apt-get update && apt-get install -y gcc python3

# 安全限制
RUN useradd -m runner
USER runner
WORKDIR /home/runner

# 编译运行C++代码
COPY solution.cpp .
RUN g++ -o solution solution.cpp
CMD ["./solution"]
```

2. **团队协作优化**
```yaml
# docker-compose.yml - 统一开发环境
services:
  acm-env:
    build: .
    volumes:
      - .:/workspace
    working_dir: /workspace
```

## 🎯 ACM选手特别关注点

### 1. 性能敏感配置
```dockerfile
# 优化构建缓存
FROM ubuntu:20.04
# 合并RUN命令减少层数
RUN apt-get update && apt-get install -y \
    build-essential \
    cmake \
    && rm -rf /var/lib/apt/lists/*  # 减小镜像大小
```

### 2. 资源限制（重要！）
```bash
# 限制CPU和内存
docker run -it \
  --cpus=1 \
  --memory=512m \
  --memory-swap=1g \
  your-acm-image
```

### 3. 快速调试技巧
```bash
# 进入运行中的容器
docker exec -it <container> /bin/bash

# 查看容器资源使用
docker stats

# 查看容器日志
docker logs <container>
```

## ⚡ 极简学习资源

**只看这些就够了：**
1. **Docker官方文档** - 命令参考部分
2. **Dockerfile最佳实践** - 官方文档中的一页
3. **Play with Docker** - 在线立即实践

## 🔥 挑战任务（检验掌握程度）

**初级**：将你的ACM模板代码容器化，支持C++/Python编译运行

**中级**：创建一个多语言评测环境（C++、Python、Java）

**高级**：用Docker Compose搭建一个简单的在线评测系统原型

## 💡 学习策略建议

1. **跳过理论深究** - 先会用，再理解原理
2. **命令不用背** - 理解模式：`docker <操作> <对象> <参数>`
3. **重点掌握**：Dockerfile编写、端口映射、数据卷
4. **忽略高级特性**：Swarm、Kubernetes等后续需要再学

## 🚀 快速验证掌握程度

完成以下任务即算掌握：
- [ ] 能将自己的项目容器化
- [ ] 能用Docker运行数据库等依赖服务
- [ ] 能用Docker Compose编排多服务应用
- [ ] 理解镜像分层原理并能优化Dockerfile

作为ACM选手，你应该能很快理解Docker的"分层"思想（类似动态规划的状态转移）和"隔离"概念（类似进程空间隔离）。

需要任何具体的代码示例或遇到问题，随时问我！你可以直接发你的具体场景，我给你针对性的Docker化方案。