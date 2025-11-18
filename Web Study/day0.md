node.js是用来跑js代码的RE(运行时环境)

npm是node.js版的pip

web框架是用来加速web服务器开发的

|   |   |   |   |
|---|---|---|---|
|**语言**|**框架**|**特点**|**适合场景**|
|JavaScript|Express.js|轻量灵活，中间件生态丰富|中小型项目，API服务|
|JavaScript|Koa.js|更现代，异步处理更好|需要高性能异步处理|
|Python|Django|功能全面，开箱即用|大型企业应用|
|Python|Flask|轻量灵活，学习曲线平缓|小型项目，微服务|
|Java|Spring Boot|企业级，生态强大|大型复杂系统|

npm下的package.json是用来记录项目需要的包信息的

package-lock.json是具体的版号之类的，防止包新了然后不支持了

# 中间件？

一种流水线般的响应服务链

可以一环扣一环，流程之间共享`req`
执行顺序由声明顺序决定

```js
app.use((req, res, next) => {

    console.log('中间件1触发');

    next();

}, (req, res, next) => {

    console.log('中间件2触发');

    res.send('来自中间件2的消息')

});
```


| HTTP方法     | CRUD操作 | 描述     | 示例                    |
| ---------- | ------ | ------ | --------------------- |
| **GET**    | Read   | 获取资源   | `GET /api/users`      |
| **POST**   | Create | 创建资源   | `POST /api/users`     |
| **PUT**    | Update | 更新整个资源 | `PUT /api/users/1`    |
| **PATCH**  | Update | 部分更新资源 | `PATCH /api/users/1`  |
| **DELETE** | Delete | 删除资源   | `DELETE /api/users/1` |

## Router？

相当于是做分发的，不同页面可以分配给不同的Router来做，比如

```js
// routers/Users.js
router.get('/:username', (req, res, next) => {...});
router.post('/', (req, res, next) => {...});
router.delete('/:username', (req, res, next) => {...});

// routers/Articles.js
router.get('/:title', (req, res, next) => {...});
router.post('/', (req, res, next) => {...});
router.delete('/:title', (req, res, next) => {...});

// app.js
let UsersRouter = require('./routers/Users');
let ArticlesRouter = require('./routers/Articles');

app.use('./api/users', UsersRouter);
app.use('./api/articles', ArticlesRouter);
```

这样你就可以分开来写每种东西的逻辑，只需要在`app.js`中轻松挂载即可，真是太方便了！


## 把前后端连到一起

首先你需要把前端作为静态文件引入后端

```js
app.use(express.static('public'));
```

前端发请求可以用`fetch`函数