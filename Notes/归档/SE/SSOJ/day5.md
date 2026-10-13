# 进行一个后端的写

JavaBean 用于封装数据与业务逻辑，它可以

把表单数据和数据库查询结果映射为对象方便在不同层之间传递数据

Spring MVC 架构

```
Client Request
     ↓
DispatcherServlet (前端控制器)
     ↓
HandlerMapping (找到对应Controller)
     ↓
Controller (处理请求)
     ↓ ←→ Service (业务逻辑)
     ↓ ←→ DAO/Repository (数据访问)
     ↓
ModelAndView (返回结果)
     ↓
ViewResolver (解析视图)
     ↓
View (JSP/Thymeleaf等)
     ↓
Response to Client
```

就本项目而言，设计是这样的

```
config是用来设置自定义配置的

controller是用来和前端对接处理请求的
service是接受controller的请求提供服务的
dao是用来与数据库交互的

entity是用来在层与层之间用对象的形式传递数据的
util是方便用的工具
```


于是完成 entity 中的内容，保持与数据表定义一致即可

示例：User.xml

```java
package com.ssoj.backend.entity;

import lombok.Data;
import java.time.LocalDateTime;

/**
 * 用户实体类
 */
@Data
public class User {
    private Long id;
    private String username;
    private String nickname;
    private String password;
    private String email;
    private String profile;
    private String avatar;
    private String phone;
    private String role;
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
}
```

@Data 是 lombok 的功能，可以自动创建 getter() 、setter()

然后我需要完成数据库表和 entity 的映射

这里我们用 mybatis 进行映射

什么是 [[mybatis]] ？

完善 backend/src/main/resources/mapper 中的各种 mapper.xml

示例：UserMapper.xml (未完成)

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.ssoj.backend.dao.UserMapper">
    
    <!-- ResultMap 定义 -->
    <resultMap id="UserResultMap" type="com.ssoj.backend.entity.User">
        <id property="id" column="id"/>
        <result property="username" column="username"/>
        <result property="nickname" column="nickname"/>
        <result property="password" column="password"/>
        <result property="email" column="email"/>
        <result property="profile" column="profile"/>
        <result property="avatar" column="avatar"/>
        <result property="phone" column="phone"/>
        <result property="role" column="role"/>
        <result property="createdAt" column="created_at"/>
        <result property="updatedAt" column="updated_at"/>
    </resultMap>
    
    <!-- TODO: 实现查询 -->
    <select id="findById" resultMap="UserResultMap">
        SELECT * FROM user WHERE id = #{id}
    </select>
    <select id="findByNickname" resultMap="UserResultMap">
        SELECT * FROM user WHERE nickname = #{nickname}
    </select>
    
    <!-- TODO: 实现其他 SQL -->
    
</mapper>

```

# AI 给的进度评估

我会根据项目需求和当前实现状态做一个评估。

**已完成的部分（约 25-30%）：**

✅ **基础架构（~90%）**
- 项目结构、依赖配置（pom. Xml）
- 数据库设计（schema. Sql 完整定义）
- 实体类与数据库对齐

✅ **骨架代码（~25%）**
- Controller/Service/DAO 接口已创建
- Mapper XML 有 resultMap 但 SQL 都是 TODO
- 配置文件（application. Properties、. Env. Example）

✅ **判题器基础（~30%）**
- C++ judger 程序结构存在
- JudgerInvoker 骨架

**未完成或待实现（约 70-75%）：**

❌ **核心业务逻辑（0%）**
- 所有 Service 方法都是 TODO
- 所有 Controller 方法都是 TODO
- Mapper XML 中的 SQL 语句全部缺失

❌ **关键功能缺失**
- 循环依赖问题未解决（SubmissionService ↔ JudgeService）
- JWT 认证/授权未实现（过滤器、拦截器）
- 异步配置（@EnableAsync、线程池）
- 判题流程集成（Service → Invoker → 更新结果）

❌ **测试与质量（~5%）**
- 单元测试基本没有
- 集成测试缺失
- 测试当前因循环依赖失败

❌ **前端（0%）**
- 完全未开始

❌ **部署与运维（~10%）**
- Dockerfile/deployment. Yaml 存在但可能不完整
- CI/CD 未配置

---

**综合评估：**

- **仅后端**：约 **30-35%**（骨架搭好，核心逻辑待实现）
- **包含前端的完整项目**：约 **20-25%**

**关键里程碑与剩余工作量：**

1. **实现 DAO 层 SQL**（~5-8%工作量）- 补充所有 Mapper XML
2. **实现 Service 业务逻辑**（~25-30%）- 核心功能、事务、解决循环依赖
3. **实现 Controller**（~10-15%）- 请求处理、参数校验、权限
4. **安全与认证**（~8-10%）- JWT 过滤器、权限拦截
5. **判题系统集成**（~10-12%）- 完整判题流程、异步处理
6. **测试**（~8-10%）- 单元测试、集成测试
7. **前端**（~25-30%）- UI、API 对接、状态管理
8. **部署与文档**（~5%）

没时间做了，操作系统实验马上截止了，前面的区域以后再来探索吧