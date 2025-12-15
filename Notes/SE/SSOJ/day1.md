我深知这是一个很大的项目......

不管怎么说，既然决定做了那就要认真来做！

项目框架（AI 写的，感觉挺合理的。）后续有删改再说吧

```
ssoj/
├── judger/                         # ⚙️ 判题核心模块（C++）
│   ├── CMakeLists.txt              # CMake构建配置
│   ├── src/                        # 源代码目录
│   │   ├── main.cpp                # 判题程序入口
│   │   ├── compiler.cpp/h          # 编译模块（支持C/C++/Python等）
│   │   ├── runner.cpp/h            # 运行模块（执行用户代码）
│   │   ├── security.cpp/h          # 安全模块（seccomp/ptrace限制syscall）
│   │   ├── resource.cpp/h          # 资源限制模块（cgroup限制CPU/内存）
│   │   ├── comparator.cpp/h        # 输出比对模块（标准输出vs预期输出）
│   │   └── utils.cpp/h             # 工具函数（日志、文件操作等）
│   ├── config/                     # 配置文件
│   │   ├── syscall_whitelist.json  # 允许的系统调用白名单
│   │   └── language_config.json    # 各语言编译运行配置
│   ├── tests/                      # 单元测试
│   │   ├── test_compiler.cpp
│   │   ├── test_runner.cpp
│   │   └── test_security.cpp
│   └── README.md                   # 判题机使用说明与API文档
│
├── backend/                        # 🌐 业务后端（Spring Boot）
│   ├── pom.xml                     # Maven依赖配置
│   ├── src/main/java/com/ssoj/backend
│   │   ├── SsojApplication.java    # Spring Boot启动类
│   │   ├── config/                 # 配置类
│   │   │   ├── SecurityConfig.java # Spring Security配置
│   │   │   ├── CorsConfig.java     # 跨域配置
│   │   │   └── AsyncConfig.java    # 异步任务配置
│   │   ├── controller/             # REST API控制器
│   │   │   ├── UserController.java # 用户相关API（注册/登录）
│   │   │   ├── ProblemController.java # 题目API（列表/详情/创建）
│   │   │   ├── SubmissionController.java # 提交API（提交代码/查询结果）
│   │   │   └── AdminController.java # 管理员API（题目管理等）
│   │   ├── service/                # 业务逻辑层
│   │   │   ├── UserService.java
│   │   │   ├── ProblemService.java
│   │   │   ├── SubmissionService.java
│   │   │   └── JudgeService.java   # 核心：调度判题机
│   │   ├── dao/                    # 数据访问层（MyBatis Mapper）
│   │   │   ├── UserMapper.java
│   │   │   ├── ProblemMapper.java
│   │   │   └── SubmissionMapper.java
│   │   ├── entity/                 # 实体类
│   │   │   ├── User.java
│   │   │   ├── Problem.java
│   │   │   ├── Submission.java
│   │   │   └── TestCase.java
│   │   └── util/                   # 工具类
│   │       ├── JudgerInvoker.java  # 调用判题机的封装
│   │       ├── JwtUtil.java        # JWT Token工具
│   │       └── FileUtil.java       # 文件操作工具
│   ├── src/main/resources/
│   │   ├── application.yml         # Spring Boot配置文件
│   │   ├── application-dev.yml     # 开发环境配置
│   │   ├── application-prod.yml    # 生产环境配置
│   │   └── mapper/                 # MyBatis XML映射文件
│   │       ├── UserMapper.xml
│   │       ├── ProblemMapper.xml
│   │       └── SubmissionMapper.xml
│   ├── src/test/                   # 后端单元测试
│   │   └── java/com/ssoj/
│   │       ├── UserServiceTest.java
│   │       └── JudgeServiceTest.java
│   └── README.md                   # 后端开发说明
│
├── frontend/                       # 🎨 前端（Vue 3）
│   ├── package.json                # npm依赖配置
│   ├── vite.config.js              # Vite构建配置
│   ├── index.html                  # 入口HTML
│   ├── src/
│   │   ├── main.js                 # Vue应用入口
│   │   ├── App.vue                 # 根组件
│   │   ├── router/                 # 路由配置
│   │   │   └── index.js            # 路由定义（首页/题目/提交/个人中心）
│   │   ├── views/                  # 页面组件
│   │   │   ├── Home.vue            # 首页
│   │   │   ├── Login.vue           # 登录/注册页
│   │   │   ├── ProblemList.vue     # 题目列表页
│   │   │   ├── ProblemDetail.vue   # 题目详情+提交页
│   │   │   ├── SubmissionList.vue  # 提交记录列表
│   │   │   ├── SubmissionDetail.vue # 提交详情（判题结果可视化）
│   │   │   └── Admin.vue           # 管理员页面
│   │   ├── components/             # 可复用组件
│   │   │   ├── CodeEditor.vue      # 代码编辑器（集成Monaco/CodeMirror）
│   │   │   ├── JudgeStatus.vue     # 判题状态展示组件
│   │   │   ├── ProblemCard.vue     # 题目卡片
│   │   │   └── NavBar.vue          # 导航栏
│   │   ├── api/                    # API请求封装
│   │   │   ├── request.js          # axios封装
│   │   │   ├── user.js             # 用户相关API
│   │   │   ├── problem.js          # 题目相关API
│   │   │   └── submission.js       # 提交相关API
│   │   ├── store/                  # Pinia状态管理
│   │   │   ├── index.js
│   │   │   ├── user.js             # 用户状态（登录信息）
│   │   │   └── problem.js          # 题目状态
│   │   ├── assets/                 # 静态资源
│   │   │   ├── logo.png
│   │   │   └── styles/             # 样式文件
│   │   │       └── global.css
│   │   └── utils/                  # 前端工具函数
│   │       ├── auth.js             # Token存储与验证
│   │       └── constants.js        # 常量定义（判题状态等）
│   ├── public/                     # 公共资源
│   └── README.md                   # 前端开发说明
│
├── database/                       # 🗄️ 数据库相关
│   ├── schema.sql                  # 数据库表结构定义（DDL）
│   ├── init_data.sql               # 初始化数据（测试用户/题目）
│   └── migrations/                 # 数据库迁移脚本（可选）
│       ├── V1__create_tables.sql
│       └── V2__add_indexes.sql
│
├── deployment/                     # 🚀 部署与容器化
│   ├── docker/                     # Docker相关文件
│   │   ├── judger.Dockerfile       # 判题机镜像
│   │   ├── backend.Dockerfile      # 后端镜像
│   │   └── frontend.Dockerfile     # 前端镜像（Nginx）
│   ├── docker-compose.yml          # 一键启动所有服务
│   ├── docker-compose.dev.yml      # 开发环境配置
│   ├── nginx/                      # Nginx配置
│   │   └── nginx.conf              # 前端服务+反向代理配置
│   └── scripts/                    # 部署脚本
│       ├── build.sh                # 构建所有镜像
│       ├── deploy.sh               # 部署脚本
│       └── clean.sh                # 清理脚本
│
├── tests/                          # 🧪 集成测试与压力测试
│   ├── integration/                # 集成测试（端到端）
│   │   ├── test_submit_flow.py     # 测试完整提交流程
│   │   └── test_api.py             # 测试所有API
│   ├── performance/                # 性能/压力测试
│   │   ├── concurrent_submit.py    # 并发提交测试
│   │   └── benchmark.md            # 性能测试报告
│   └── security/                   # 安全测试
│       ├── test_malicious_code.cpp # 测试恶意代码拦截
│       └── security_report.md      # 安全测试报告
│
├── scripts/                        # 🔧 开发脚本
│   ├── setup_dev.sh                # 开发环境一键搭建
│   ├── generate_testcase.py        # 生成测试用例工具
│   └── backup_db.sh                # 数据库备份脚本
│
├── .gitignore                      # Git忽略配置
├── README.md                       # 项目总README
├── Jenkinsfile                     # CI/CD配置
├── deployment.yaml                 # K8s部署配置
└── dockerfile                      # 项目总Dockerfile
```


开始做 judger 模块

上班。。。。

# 基本思路

judger 的基本工作流程：

1. 编译用户提交的代码

2. 在受限环境下运行程序

3. 比对输出结果

4. 返回判题结果（AC/WA/TLE/MLE/RE/CE/SE）

# 模块划分

- **utils** - 基础工具函数，文件读写、日志、临时目录管理

- **compiler** - 编译模块，支持 C/C++ 和 Python

- **runner** - 核心执行模块，负责运行程序和监控

- **resource** - 资源限制和统计

- **comparator** - 输出比对

- **security** - 安全限制（seccomp，暂时没实现）

- **main** - 主流程整合

# 实现过程
## utils.cpp

先从简单的开始，写了文件读写、JSON 转义、日志这些基础功能。这部分还算顺利。

有个 `createTempDir` 用的是 `mkdtemp`，要记得检查返回值是不是 NULL。还有 `trimTrailingWhitespace` 处理输出尾部空白，在比对的时候会用到。

## comparator.cpp

输出比对，主要写了两种：  

- 精确匹配

- 忽略行尾空白

错误信息会明确指出哪个位置不对，Expected 和 Received 是什么。

SPJ 啥的以后再写

## compiler.cpp

  
编译模块支持 C/C++ 和 Python。C/C++ 用 `popen` 调 g++，把编译错误信息抓出来。Python 就不用编译了，直接把源文件路径返回。


编译参数用的是 `-O2 -std=c++17`，-O2 优化后面测试的时候还引发了一个问题。

谁会用 java ??？以后再支持吧
## runner.cpp

这是最复杂的部分，涉及进程管理。

用 `fork` 创建子进程，子进程里：

- 用 `setResourceLimits` 设置资源限制

- 用 `dup2` 重定向标准输入输出

- 用 `execv` 执行程序

父进程里用 `wait4` 监控子进程：

- 非阻塞轮询（WNOHANG）

- 每 10ms 检查一次

- 超过墙钟时间就 kill 掉

- 通过 `rusage` 获取 CPU 时间和内存使用

- 根据退出信号判断状态（SIGXCPU→TLE，SIGSEGV→RE 等）

## resource.cpp

资源限制用 `setrlimit`：

- RLIMIT_CPU - CPU 时间限制

- RLIMIT_AS - 内存限制

- RLIMIT_STACK - 栈限制

- RLIMIT_FSIZE - 输出文件大小限制

`getResourceUsage` 解析 `rusage` 结构体，算出 CPU 时间（user + system）和内存使用。

# 测试和调试

让 ai 写了一堆测试文件：

- test_utils - 测试工具函数

- test_compiler - 测试编译

- test_comparator - 测试输出比对

- test_runner_simple - 测试基本执行

- test_runner_comprehensive - 测试各种边界情况

- test_resource_limits - 测试资源限制


测 TLE 的时候，-O2 优化把循环给优化没了，测不出来 TLE，加了个  `volatile` 关键字，解决了

# 性能测试

写了个 benchmark ，测一下不同数据量的运行时间：

- 1e7 次操作：14ms

- 1e8 次操作：101ms

- 2e8 次操作：200ms

- 1e9 次操作：994ms

基本符合预期，简单循环大概是 1e9 次/秒。考虑到实际要寻址以及复杂的计算，1 秒时限跑 1e8~2e8 的数据量是比较合理的。

### 完整流程测试

最后跑了一遍完整的判题流程，测试了：

- AC - A+B 问题正常通过

- WA - 输出错误能准确识别

- TLE - 超时检测正常

- MLE - 内存超限检测（虽然这次没触发）

- RE - 段错误能捕获

- CE - 编译错误信息完整

- Python - Python 程序也能正常判题
  
全都过了，judger 基本能用了。

顺便测了 Atcoder 上的某道题，也过了
# 总结

没完成 java 的判题，但是 c++的跑通了，还行。。  

SPJ 以后在搞

以及多文件多组数据判题也以后在搞

安全也是以后再搞（或者懒得搞了随缘吧）

下一步应该是和后端对接，看看实际判题效果怎么样。如果有问题再调整。

搞了六个小时。已经一点了。好累。

谁来杀了我