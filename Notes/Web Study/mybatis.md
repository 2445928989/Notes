MyBatis 是一个持久层框架，用来把 Java 与数据库交互的 SQL 进行映射和管理。

典型的 MyBatis 开发流程：

1. 设计实体：在 entity 包定义 POJO（字段与数据库列对应）。

2. 定义 Mapper 接口：在 dao 包写接口方法（例如 ProblemMapper）。

3. 写  SQL 映射：在 resources/mapper/XXXMapper. Xml（或用注解）为接口方法添加 \<select>/insert/update/delete，并用 \<resultMap> 映射列到实体。

4. 配置集成：在 Spring Boot 中确认 mybatis. Mapper-locations=classpath: mapper/\*. Xml、@Mapper 或在主类用 @MapperScan ("com. Ssoj. Backend. Dao")，并配置 DataSource。
在 Service 中使用：在 Service 注入 Mapper（@Autowired 或构造器注入）并实现业务逻辑与事务。

5. 编写测试：用单元/集成测试验证 SQL 与映射（可用内存数据库或测试容器），检查 mybatis 日志输出的 SQL 与参数。

6. 处理高级需求：实现动态 SQL（\<if>/\<foreach>）、分页、TypeHandler、自定义拦截器、二级缓存等。

7. 运行调试与优化：确认返回结果、N+1 问题、索引使用、SQL 性能，必要时调整 SQL/索引或改为更合适的查询