```Java
@RestController
// 标记该类可以处理Web请求，返回JSON

@RequestMapping("/api")
// 设定该类或该对象的工作路径

@GetMapping("/{id}")
// 设定该方法处理对应路径下的GET请求,{id}是给PathVarible用的

@PathVariable Long id
// 将URL里的占位符传递给方法的参数

@RequestParam Long id
// 将?key=value中的值传递给方法的参数

@RequestBody Map<String,String> map
// 用来接收请求体

@MapperScan("com.example.demo.mapper")
// 用来标记Mapper所在软件包

@Mapper
// 标记该接口是一个Mapper

@AutoWired
// 自动获取对象不需要手动初始化

```

```yaml
server.servlet.set-context
# 用来配置该项目的工作路径

server.port
# 端口

server.profiles.active
# 启用哪个application-***配置文件


```

Bean 是用来储存数据的标准化类，实现了一些通用的接口

RESTful API 无状态、使用 JSON 或 XML 等格式交换数据、URL 只表示资源而不包含动词，并借助 HTTP 状态码表示请求结果

增 POST 
删 DELETE
查 GET
改 PUT
部分改 PATCH

