# 今天的主题： 数据库设计与实现

安装好了 mysql，配置一下数据库
```sql
CREATE DATABASE ssoj;
USE ssoj;	
```

```sql
-- 开发角色
CREATE USER 'ssoj_dev'@'localhost' IDENTIFIED BY '********';
GRANT ALL PRIVILEGES ON ssoj.* TO 'ssoj_dev'@'localhost';

CREATE USER 'ssoj_dev'@'%' IDENTIFIED BY '*******';
GRANT SELECT, INSERT, UPDATE, DELETE ON ssoj.* TO 'ssoj_dev'@'%';

-- 测试角色
CREATE USER 'ssoj_demo'@'%' IDENTIFIED BY '********';
GRANT SELECT ON ssoj.* TO 'ssoj_demo'@'%';

-- 应用角色
CREATE USER 'ssoj_app'@'localhost' IDENTIFIED BY '********';
GRANT SELECT, INSERT, UPDATE, DELETE ON ssoj.* TO 'ssoj_app'@'localhost';

FLUSH PRIVILEGES;
```

完成了 `schema.sql`
```sql
CREATE DATABASE IF NOT EXISTS ssoj CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
USE ssoj;

CREATE TABLE user (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    nickname VARCHAR(100),
    password VARCHAR(255) NOT NULL COMMENT '加密后密码',
    email VARCHAR(100) UNIQUE NOT NULL,
    profile TEXT,
    avatar VARCHAR(500) COMMENT '头像URL',
    phone VARCHAR(20),
    role VARCHAR(20) NOT NULL DEFAULT 'USER',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_role (role),
    INDEX idx_created_at (created_at)
);

CREATE TABLE problem(
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(100) NOT NULL UNIQUE,
    description TEXT,
    input_format TEXT,
    output_format TEXT,
    sample_input TEXT,
    sample_output TEXT,
    difficulty VARCHAR(20) NOT NULL,
    time_limit DOUBLE DEFAULT 1.0 COMMENT '时间限制(秒)',
    memory_limit INT DEFAULT 262144 COMMENT '内存限制(KB)',
    author_id BIGINT,
    number_of_submissions INT DEFAULT 0,
    number_of_accepted INT DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (author_id) REFERENCES user(id) ON DELETE SET NULL,
    INDEX idx_difficulty(difficulty),
    INDEX idx_author_id(author_id),
    INDEX idx_created_at(created_at)
);

CREATE TABLE tag(
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) UNIQUE NOT NULL,
    color VARCHAR(7) DEFAULT '#23ff20',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

CREATE TABLE problem_tag(
    problem_id BIGINT NOT NULL,
    tag_id BIGINT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY(problem_id,tag_id),
    FOREIGN KEY(problem_id) REFERENCES problem(id) ON DELETE CASCADE,
    FOREIGN KEY(tag_id) REFERENCES tag(id) ON DELETE CASCADE,
    INDEX idx_tag_id(tag_id)
);

CREATE TABLE test_case(
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    problem_id BIGINT NOT NULL,
    input_path VARCHAR(500) NOT NULL COMMENT '输入文件路径',
    output_path VARCHAR(500) NOT NULL COMMENT '输出文件路径',
    is_sample BOOLEAN DEFAULT FALSE COMMENT '是否为样例测试点',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_problem_id(problem_id),
    FOREIGN KEY(problem_id) REFERENCES problem(id) ON DELETE CASCADE
);

CREATE TABLE submission(
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    user_id BIGINT NOT NULL,
    problem_id BIGINT NOT NULL,
    code TEXT NOT NULL,
    language VARCHAR(50) NOT NULL,
    status VARCHAR(30) NOT NULL,
    max_time_used INT DEFAULT NULL COMMENT '最大运行时间(ms)',
    max_memory_used INT DEFAULT NULL COMMENT '最大内存使用(KB)',
    submitted_at TIMESTAMP NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,

    INDEX idx_user_problem_time(user_id, problem_id, submitted_at),
    INDEX idx_status(status),

    FOREIGN KEY(user_id) REFERENCES user(id) ON DELETE CASCADE,
    FOREIGN KEY(problem_id) REFERENCES problem(id) ON DELETE CASCADE
);

CREATE TABLE result(
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    submission_id BIGINT NOT NULL,
    test_case_id BIGINT NOT NULL,
    status VARCHAR(50) NOT NULL COMMENT 'AC/WA/TLE/MLE/RE/CE等',
    error_message TEXT COMMENT '编译/运行错误信息',
    time_used INT DEFAULT NULL COMMENT '运行时间(ms)',
    memory_used INT DEFAULT NULL COMMENT '内存使用(KB)',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    INDEX idx_submission_id (submission_id),
    INDEX idx_test_case_id (test_case_id),
    INDEX idx_status (status),

    FOREIGN KEY(submission_id) REFERENCES submission(id) ON DELETE CASCADE,
    FOREIGN KEY(test_case_id) REFERENCES test_case(id) ON DELETE CASCADE
);
```

设计了 8 张表：

### 核心表
1. **user** - 用户表，存用户信息
2. **problem** - 题目表，存题目元数据
3. **test_case** - 测试用例表，一个题目多个测试点
4. **submission** - 提交表，用户提交一次记一条
5. **result** - 判题结果表，一个提交对应多个测试点的结果

### 配置表
6. **tag** - 标签表（DP、贪心等）
7. **problem_tag** - 中间表，题目和标签的多对多关系

## 遇到的问题

忘了。不过最后都顺利解决了
## 最后的表结构

整个 schema.sql 文件写完了，包括：
- CREATE DATABASE 语句（这样执行时自动创建库）
- 8 张表的完整 CREATE TABLE 语句
- 所有外键关系和索引

表都设计得比较规范：
- 所有字符集都是 utf 8 mb 4（支持中文 emoji）
- 时间字段都有 TIMESTAMP，自动维护创建和更新时间
- 关键字段都有注释，解释单位和含义

下一步应该是：
1. 更新后端 Entity 类对应字段
2. 写 Mapper XML 的 SQL 语句
3. 实现业务逻辑
