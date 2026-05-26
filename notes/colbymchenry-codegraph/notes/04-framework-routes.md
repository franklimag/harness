# 框架路由检测

> 14 个框架的路由模式识别 — 让知识图谱"理解"框架约定。

## 一、概述

CodeGraph 不仅理解语言语法，还理解 **框架约定**。框架路由检测让 CodeGraph 能够将 URL 路由映射到处理函数，将控制器注解映射到 HTTP 端点。

### 为什么需要框架感知？

普通的代码分析工具只能看到：

```python
# 只是一个普通装饰器和函数
@app.route('/users')
def get_users():
    return User.query.all()
```

框架感知的 CodeGraph 能理解：

```
HTTP GET /users  ──routes_to──▶  get_users()
                                    │
                                    └── calls ──▶ User.query.all()
```

### 支持的 14 个框架

| # | 框架 | 语言 | 路由方式 |
|---|------|------|----------|
| 1 | Django | Python | `urls.py` — `path()` / `include()` |
| 2 | Flask | Python | 装饰器 — `@app.route()` |
| 3 | FastAPI | Python | 装饰器 — `@router.get()` |
| 4 | Express | JavaScript | 方法调用 — `router.get()` |
| 5 | NestJS | TypeScript | 装饰器 — `@Controller()` + `@Get()` |
| 6 | Laravel | PHP | 静态方法 — `Route::get()` |
| 7 | Drupal | PHP | 配置文件 — `*.routing.yml` |
| 8 | Rails | Ruby | DSL — `routes.rb` |
| 9 | Spring | Java | 注解 — `@RequestMapping` |
| 10 | Gin/chi/gorilla | Go | 方法调用 — `r.GET()` |
| 11 | Axum/actix/Rocket | Rust | 宏/方法 — `.route()` / `#[get()]` |
| 12 | ASP.NET | C# | 属性 — `[HttpGet]` |
| 13 | Vapor | Swift | 方法调用 — `app.get()` |
| 14 | React Router/SvelteKit | JS/TS | 文件系统路由 |

## 二、Python 框架

### 2.1 Django

**路由模式**: URL 配置文件 (`urls.py`) 中的 `path()` 和 `include()`

```python
# urls.py
from django.urls import path, include
from . import views

urlpatterns = [
    path('users/', views.user_list, name='user-list'),
    path('users/<int:pk>/', views.user_detail, name='user-detail'),
    path('api/', include('api.urls')),
]
```

**CodeGraph 提取结果**:

```
Node: user_list (function, views.py:15)
Node: user_detail (function, views.py:30)
Edge: "GET /users/" ──routes_to──▶ user_list
Edge: "GET /users/<int:pk>/" ──routes_to──▶ user_detail
Edge: "/api/" ──includes──▶ api.urls
```

**检测逻辑**:
1. 识别 `urlpatterns` 列表
2. 解析 `path()` 调用的第一个参数（URL 模式）
3. 解析第二个参数（视图函数引用）
4. 建立 `routes_to` 边

### 2.2 Flask

**路由模式**: `@app.route()` 装饰器

```python
from flask import Flask
app = Flask(__name__)

@app.route('/users', methods=['GET'])
def get_users():
    return jsonify(User.query.all())

@app.route('/users/<int:user_id>', methods=['GET', 'POST'])
def user_detail(user_id):
    ...
```

**CodeGraph 提取结果**:

```
Node: get_users (function, app.py:6)
Node: user_detail (function, app.py:10)
Edge: "GET /users" ──routes_to──▶ get_users
Edge: "GET,POST /users/<int:user_id>" ──routes_to──▶ user_detail
```

### 2.3 FastAPI

**路由模式**: `@router.get()` / `@router.post()` 等装饰器

```python
from fastapi import APIRouter
router = APIRouter(prefix="/users")

@router.get("/")
async def list_users():
    ...

@router.post("/")
async def create_user(user: UserCreate):
    ...

@router.get("/{user_id}")
async def get_user(user_id: int):
    ...
```

**CodeGraph 提取结果**:

```
Node: list_users (function, routes.py:5)
Node: create_user (function, routes.py:9)
Node: get_user (function, routes.py:13)
Edge: "GET /users/" ──routes_to──▶ list_users
Edge: "POST /users/" ──routes_to──▶ create_user
Edge: "GET /users/{user_id}" ──routes_to──▶ get_user
```

## 三、Node.js 框架

### 3.1 Express

**路由模式**: `router.method(path, handler)` 方法调用

```javascript
const express = require('express');
const router = express.Router();

router.get('/users', authenticate, getUsers);
router.post('/users', validateBody, createUser);
router.get('/users/:id', getUserById);
```

**CodeGraph 提取结果**:

```
Edge: "GET /users" ──routes_to──▶ getUsers
Edge: "POST /users" ──routes_to──▶ createUser
Edge: "GET /users/:id" ──routes_to──▶ getUserById
Edge: "GET /users" ──middleware──▶ authenticate
Edge: "POST /users" ──middleware──▶ validateBody
```

### 3.2 NestJS

**路由模式**: 类装饰器 `@Controller()` + 方法装饰器 `@Get()/@Post()`

```typescript
@Controller('users')
export class UsersController {
  constructor(private readonly usersService: UsersService) {}

  @Get()
  findAll(): Promise<User[]> {
    return this.usersService.findAll();
  }

  @Get(':id')
  findOne(@Param('id') id: string): Promise<User> {
    return this.usersService.findOne(id);
  }

  @Post()
  create(@Body() dto: CreateUserDto): Promise<User> {
    return this.usersService.create(dto);
  }
}
```

**CodeGraph 提取结果**:

```
Node: UsersController (class, users.controller.ts:1)
Node: findAll (method, users.controller.ts:5)
Node: findOne (method, users.controller.ts:10)
Node: create (method, users.controller.ts:15)
Edge: "GET /users" ──routes_to──▶ findAll
Edge: "GET /users/:id" ──routes_to──▶ findOne
Edge: "POST /users" ──routes_to──▶ create
Edge: findAll ──calls──▶ UsersService.findAll
```

## 四、其他语言框架

### 4.1 Rails (Ruby)

**路由模式**: `routes.rb` DSL

```ruby
Rails.application.routes.draw do
  resources :users do
    member do
      post :activate
    end
  end
  get '/health', to: 'health#check'
end
```

**CodeGraph 提取**:
- `GET /users` → `UsersController#index`
- `POST /users` → `UsersController#create`
- `GET /users/:id` → `UsersController#show`
- `POST /users/:id/activate` → `UsersController#activate`
- `GET /health` → `HealthController#check`

### 4.2 Spring (Java)

**路由模式**: `@RequestMapping` / `@GetMapping` 注解

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    @GetMapping
    public List<User> listUsers() { ... }

    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) { ... }

    @PostMapping
    public User createUser(@RequestBody UserDto dto) { ... }
}
```

**CodeGraph 提取**:
- `GET /api/users` → `UserController.listUsers`
- `GET /api/users/{id}` → `UserController.getUser`
- `POST /api/users` → `UserController.createUser`

### 4.3 Gin (Go)

**路由模式**: `r.Method(path, handler)` 方法调用

```go
func SetupRouter() *gin.Engine {
    r := gin.Default()

    users := r.Group("/users")
    {
        users.GET("", listUsers)
        users.GET("/:id", getUser)
        users.POST("", createUser)
    }

    return r
}
```

**CodeGraph 提取**:
- `GET /users` → `listUsers`
- `GET /users/:id` → `getUser`
- `POST /users` → `createUser`

### 4.4 Axum (Rust)

**路由模式**: `.route(path, handler)` 链式调用

```rust
let app = Router::new()
    .route("/users", get(list_users).post(create_user))
    .route("/users/:id", get(get_user).put(update_user))
    .layer(middleware::from_fn(auth_middleware));
```

**CodeGraph 提取**:
- `GET /users` → `list_users`
- `POST /users` → `create_user`
- `GET /users/:id` → `get_user`
- `PUT /users/:id` → `update_user`

### 4.5 Laravel (PHP)

**路由模式**: `Route::method()` 静态方法

```php
Route::get('/users', [UserController::class, 'index']);
Route::post('/users', [UserController::class, 'store']);
Route::get('/users/{id}', [UserController::class, 'show']);
```

### 4.6 Drupal (PHP) — v0.9.2 新增

**路由模式**: `*.routing.yml` 配置文件

```yaml
# mymodule.routing.yml
mymodule.user_list:
  path: '/admin/users'
  defaults:
    _controller: '\Drupal\mymodule\Controller\UserController::list'
  requirements:
    _permission: 'administer users'
```

**CodeGraph 提取**:
- `GET /admin/users` → `UserController::list`

### 4.7 ASP.NET (C#)

**路由模式**: `[Http*]` 属性

```csharp
[ApiController]
[Route("api/[controller]")]
public class UsersController : ControllerBase
{
    [HttpGet]
    public async Task<IActionResult> GetAll() { ... }

    [HttpGet("{id}")]
    public async Task<IActionResult> GetById(int id) { ... }
}
```

### 4.8 Vapor (Swift)

**路由模式**: `app.method()` 方法调用

```swift
func routes(_ app: Application) throws {
    app.get("users") { req in
        return try await User.query(on: req.db).all()
    }
    app.post("users") { req in
        let user = try req.content.decode(User.self)
        try await user.save(on: req.db)
        return user
    }
}
```

## 五、文件系统路由

### 5.1 React Router / Next.js / SvelteKit

**路由模式**: 文件路径即 URL 路径

```
src/routes/
├── +page.svelte              → /
├── about/
│   └── +page.svelte          → /about
├── users/
│   ├── +page.svelte          → /users
│   ├── [id]/
│   │   └── +page.svelte      → /users/:id
│   └── +page.server.ts       → /users (数据加载)
└── api/
    └── health/
        └── +server.ts        → /api/health
```

**CodeGraph 提取**:
- 文件路径 → URL 路径映射
- `+page.svelte` → 页面组件
- `+page.server.ts` → 数据加载函数
- `+server.ts` → API 端点

### 5.2 文件路由的挑战

| 挑战 | 说明 | 处理方式 |
|------|------|----------|
| 动态路由 | `[id]` / `[...slug]` | 识别为参数 |
| 布局嵌套 | `+layout.svelte` | 识别父子关系 |
| 服务端/客户端 | `+page.server.ts` vs `+page.ts` | 区分类型 |
| 路由分组 | `(group)` 目录 | 识别为逻辑分组 |

## 六、Mixed iOS/RN/Expo 桥接

### 6.1 桥接类型

CodeGraph 还能追踪跨语言/跨平台的调用关系：

```
┌───────────────────┐          ┌───────────────────┐
│   JavaScript/TS    │          │    Swift/ObjC     │
│   (React Native)   │          │    (iOS Native)   │
├───────────────────┤          ├───────────────────┤
│ NativeModules.X()  │──bridge─▶│ @objc func X()   │
│ TurboModule.call() │──JSI───▶│ func turboCall()  │
│ Fabric component   │──Fabric─▶│ UIView subclass  │
│ ExpoModules.get()  │──expo──▶│ @ExpoModule      │
└───────────────────┘          └───────────────────┘
```

### 6.2 桥接检测

| 桥接方式 | JS 端模式 | Native 端模式 |
|----------|-----------|---------------|
| Legacy Bridge | `NativeModules.ModuleName` | `@objc` / `RCT_EXPORT_METHOD` |
| TurboModules | `TurboModuleRegistry.get()` | C++ Host Object / JSI |
| Fabric | `requireNativeComponent()` | `RCTViewManager` |
| Expo Modules | `requireNativeModule()` | `@ExpoModule` |
| Swift ↔ ObjC | — | Bridging Header / `@objc` |

### 6.3 意义

这使得 CodeGraph 能在混合 app 中追踪跨语言调用链：

```
React Component (JS)
    → NativeModules.Camera.capture() (JS bridge call)
        → CameraModule.capture() (Swift native)
            → AVCaptureSession (iOS framework)
```

## 七、框架检测逻辑

### 7.1 检测顺序

```
1. 扫描项目依赖文件 (package.json, Gemfile, go.mod, etc.)
2. 识别框架依赖
3. 加载对应的框架查询
4. 在相关文件中执行框架特定查询
5. 提取路由边
```

### 7.2 框架检测信号

| 框架 | 检测信号 |
|------|----------|
| Django | `django` in requirements.txt / `urls.py` 文件 |
| Flask | `flask` in requirements.txt |
| FastAPI | `fastapi` in requirements.txt |
| Express | `express` in package.json |
| NestJS | `@nestjs/core` in package.json |
| Laravel | `laravel/framework` in composer.json |
| Drupal | `drupal/core` in composer.json |
| Rails | `rails` in Gemfile |
| Spring | `spring-boot` in pom.xml/build.gradle |
| Gin | `github.com/gin-gonic/gin` in go.mod |
| Axum | `axum` in Cargo.toml |
| ASP.NET | `Microsoft.AspNetCore` in .csproj |
| Vapor | `vapor` in Package.swift |
| SvelteKit | `@sveltejs/kit` in package.json |

## 八、待深入研究

- [ ] 中间件/守卫的解析（不仅是路由处理函数）
- [ ] 路由前缀嵌套的正确解析
- [ ] 动态路由参数的类型提取
- [ ] API 版本号 (v1/v2) 的处理
- [ ] GraphQL resolver 是否被视为"路由"？
- [ ] gRPC service 定义的解析
- [ ] WebSocket 路由的检测

---

*前置阅读: [03-mcp-tools.md](./03-mcp-tools.md)*
*后续阅读: [05-auto-sync.md](./05-auto-sync.md)*
*最后更新: 2026-05-24*
