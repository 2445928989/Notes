太好了！我们继续按原计划进行。现在你已经理解了基础概念，让我们专注于完成单体应用的容器化。

## 🎯 继续第二天的具体任务

### **当前状态回顾**
- ✅ 理解了Docker基础概念
- ✅ 知道了你的架构是单体应用（前端在backend/public目录）
- ✅ 已经创建了后端Dockerfile

### **任务一：完善和测试单体应用容器（30分钟）**

#### 1. 确认Dockerfile内容
你的 `backend/Dockerfile` 应该类似这样：
```dockerfile
FROM node:16-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "server.js"]
```

#### 2. 构建镜像
```bash
# 在backend目录执行
docker build -t my-blog-app .
```

#### 3. 运行并测试
```bash
# 运行容器
docker run -p 3000:3000 my-blog-app

# 你应该看到类似输出：
# 服务器运行在 http://localhost:3000
```

#### 4. 验证功能
打开浏览器访问：
- `http://localhost:3000` - 应该看到你的博客页面
- `http://localhost:3000/api/articles` - 应该看到JSON数据

**测试所有功能**：
- [ ] 文章列表显示
- [ ] 发布新文章
- [ ] 删除文章

### **任务二：优化容器配置（30分钟）**

#### 1. 完善.dockerignore
确保 `.dockerignore` 包含：
```
node_modules
npm-debug.log
.git
.gitignore
README.md
.env
.DS_Store
*.log
```

#### 2. 添加健康检查（可选但推荐）
在Dockerfile中添加：
```dockerfile
# 在CMD之前添加健康检查
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD node healthcheck.js
```

创建 `healthcheck.js`：
```javascript
// 简单的健康检查
const http = require('http');

const options = {
  host: 'localhost',
  port: 3000,
  path: '/api/articles',
  timeout: 2000
};

const request = http.request(options, (res) => {
  console.log(`STATUS: ${res.statusCode}`);
  if (res.statusCode === 200) {
    process.exit(0);
  } else {
    process.exit(1);
  }
});

request.on('error', function(err) {
  console.log('ERROR');
  process.exit(1);
});

request.end();
```

### **任务三：学习关键Docker命令（30分钟）**

#### 1. 基础容器管理
```bash
# 查看运行中的容器
docker ps

# 查看所有容器（包括停止的）
docker ps -a

# 停止容器
docker stop <容器名>

# 删除容器
docker rm <容器名>

# 查看镜像
docker images

# 删除镜像
docker rmi <镜像名>
```

#### 2. 调试命令
```bash
# 查看容器日志
docker logs <容器名>

# 实时查看日志
docker logs -f <容器名>

# 进入容器内部（调试用）
docker exec -it <容器名> sh

# 在容器内，你可以：
# ls -la         查看文件
# ps aux         查看进程
# cat app.js     查看文件内容
```

### **任务四：创建开发和生产配置（30分钟）**

#### 1. 开发环境配置
创建 `docker-compose.dev.yml`：
```yaml
version: '3.8'

services:
  blog-app:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - .:/app
      - /app/node_modules
    environment:
      - NODE_ENV=development
    restart: unless-stopped
```

#### 2. 生产环境配置
创建 `docker-compose.yml`：
```yaml
version: '3.8'

services:
  blog-app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
    restart: unless-stopped
```

#### 3. 使用Docker Compose
```bash
# 开发环境
docker-compose -f docker-compose.dev.yml up -d

# 生产环境
docker-compose up -d

# 查看状态
docker-compose ps

# 停止服务
docker-compose down
```

## 🚨 故障排除指南

### **常见问题及解决**

#### 问题1：端口被占用
```bash
# 错误：端口3000已被使用
# 解决：更改端口或停止占用端口的程序
docker run -p 3001:3000 my-blog-app
```

#### 问题2：容器内文件权限
```bash
# 如果遇到权限问题，在Dockerfile中添加：
USER node
```

#### 问题3：应用启动失败
```bash
# 查看详细日志
docker logs <容器名>

# 进入容器调试
docker exec -it <容器名> sh
cd /app
ls -la
node server.js
```

## 🎯 今日成功标准

完成今天的学习后，你应该能够：

- [ ] 使用 `docker build` 构建应用镜像
- [ ] 使用 `docker run` 运行应用容器
- [ ] 在浏览器中访问容器化后的应用
- [ ] 所有博客功能正常工作
- [ ] 理解基本的Docker命令
- [ ] 使用docker-compose管理服务

## 🔄 立即实践步骤

1. **先执行这些命令**：
   ```bash
   cd backend
   docker build -t my-blog-app .
   docker run -p 3000:3000 my-blog-app
   ```

2. **测试功能是否正常**

3. **如果正常，告诉我"容器运行成功"**

4. **如果出错，把错误信息发给我**

现在开始动手吧！从第一步开始，遇到任何问题随时问我。记住：实践是最好的学习方式！