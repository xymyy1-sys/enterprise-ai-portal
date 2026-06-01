# Enterprise AI Portal

企业级 AI 门户系统，支持 AI 智能体对话、用户管理、角色权限管理、组织架构同步等功能。

## 项目简介

Enterprise AI Portal 是一个基于 Spring Boot + Vue 3 的企业级 AI 门户平台，集成 Dify AI 能力，提供智能对话、提示词管理、AI 智能体市场等功能。

## 技术架构

### 后端技术栈

| 技术 | 说明 |
|------|------|
| Spring Boot 3.2.4 | 应用框架 |
| Java 17 | 开发语言 |
| Spring Security | 安全框架 |
| JWT | 身份认证 |
| MyBatis-Plus 3.5.5 | ORM 框架 |
| MySQL | 数据库 |
| WebFlux | 异步响应式编程（处理 SSE 流） |
| Hutool | 工具库 |

### 前端技术栈

| 技术 | 说明 |
|------|------|
| Vue 3.4 | 渐进式框架 |
| TypeScript | 类型安全 |
| Vite 5.2 | 构建工具 |
| Element Plus 2.6 | UI 组件库 |
| Pinia | 状态管理 |
| Vue Router 4.3 | 路由管理 |
| marked | Markdown 渲染 |

## 项目结构

```
enterprise-ai-portal/
├── backend/                    # Spring Boot 后端
│   └── src/main/java/com/enterprise/aiportal/
│       ├── config/             # 配置类
│       ├── controller/         # 控制器
│       ├── service/           # 业务逻辑
│       ├── mapper/             # MyBatis Mapper
│       ├── entity/             # 实体类
│       ├── dto/                # 数据传输对象
│       ├── vo/                 # 视图对象
│       ├── security/           # 安全相关（JWT 过滤器）
│       ├── util/               # 工具类
│       └── exception/          # 全局异常处理
│
└── frontend/                   # Vue 3 前端
    └── src/
        ├── components/         # 公共组件
        ├── views/              # 页面视图
        ├── router/             # 路由配置
        ├── stores/             # Pinia 状态管理
        └── App.vue             # 根组件
```

## 核心功能

### 1. AI 对话

- **流式对话**：基于 SSE (Server-Sent Events) 实现实时流式输出
- **多智能体支持**：可选择不同的 AI 智能体进行对话
- **历史上下文**：支持本地历史消息续接，Dify 会话管理
- **提示词管理**：可配置的智能体系统提示词

### 2. 用户认证

- **JWT 认证**：无状态 Token 认证
- **登录/登出**：用户名密码登录
- **SSO 集成**：支持 OA 单点登录回调
- **用户信息**：获取当前登录用户信息、菜单权限

### 3. 权限管理 (RBAC)

- **用户管理**：用户的增删改查、状态管理
- **角色管理**：角色定义、权限分配
- **菜单管理**：动态菜单配置、页面权限
- **部门管理**：组织架构管理

### 4. 组织架构同步

- **用户同步**：从 OA 系统同步用户信息
- **部门同步**：从 OA 系统同步部门数据

## API 接口

### 认证相关

| 接口 | 方法 | 说明 |
|------|------|------|
| `/api/auth/login` | POST | 用户登录 |
| `/api/auth/userinfo` | GET | 获取用户信息 |
| `/api/auth/sso/callback` | GET | SSO 回调 |

### AI 对话

| 接口 | 方法 | 说明 |
|------|------|------|
| `/api/chat/stream` | POST | 流式对话（SSE） |

### 管理接口

| 接口 | 方法 | 说明 |
|------|------|------|
| `/api/admin/users/**` | REST | 用户管理 |
| `/api/admin/roles/**` | REST | 角色管理 |
| `/api/admin/depts/**` | REST | 部门管理 |
| `/api/admin/menus/**` | REST | 菜单管理 |
| `/api/sync/**` | REST | 数据同步 |

## 前端页面

| 页面 | 路径 | 说明 |
|------|------|------|
| 登录页 | `/login` | 用户登录 |
| 工作台 | `/workspace` | AI 对话主界面 |
| 智能体市场 | `/agents` | AI 智能体选择 |
| 提示词管理 | `/prompts` | 提示词配置 |
| 用户管理 | `/admin/users` | 用户管理（管理员） |
| 角色管理 | `/admin/roles` | 角色管理（管理员） |
| 部门管理 | `/admin/depts` | 部门管理（管理员） |
| 菜单管理 | `/admin/menus` | 菜单管理（管理员） |

## 环境要求

- **Java**: 17+
- **Node.js**: 18+
- **MySQL**: 8.0+

## 配置说明

### 后端配置

在 `backend/src/main/resources/application.yml` 或通过环境变量配置：

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/ai_portal
    username: root
    password: your_password

dify:
  api:
    base-url: http://your-dify-server
    default-agent-id: 1
```

### 前端配置

```env
VITE_API_BASE_URL=http://localhost:8080
```

## 开发运行

### 后端启动

```bash
cd backend
./mvnw spring-boot:run
# 或使用 Maven
mvn spring-boot:run
```

### 前端启动

```bash
cd frontend
npm install
npm run dev
```

## 主要业务逻辑

### JWT 认证流程

1. 用户登录成功后，后端生成 JWT Token 返回
2. 前端存储 Token，后续请求通过 `Authorization: Bearer <token>` 头部携带
3. `JwtAuthenticationFilter` 拦截请求，验证 Token 并设置 Spring Security 上下文
4. 有效 Token 用户可访问受保护资源

### AI 对话流程

1. 前端发送对话请求到 `/api/chat/stream`
2. `ChatController` 解析请求，查询对应智能体配置
3. `DifyProxyService` 向 Dify 服务器发起 SSE 请求
4. Dify 返回的流数据通过 `ChatController` 透传给前端
5. 前端通过 `EventSource` 接收并渲染

### 权限控制

1. 用户拥有多个角色（如 `admin`, `user`）
2. 角色拥有多个权限（如 `user:list`, `user:create`）
3. 菜单关联权限码，前端根据权限动态显示菜单
4. 后端接口通过 Spring Security 注解或 Filter 校验权限

## License

MIT