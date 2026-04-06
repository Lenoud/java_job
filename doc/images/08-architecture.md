# 8. 系统架构图 (Architecture Diagram)

> **用途**: 描述系统整体分层架构和组件间的关系
> **阶段**: 系统架构设计阶段

```mermaid
graph TB
    subgraph client["前端展示层 (Vue 3 + TypeScript)"]
        direction LR
        subgraph user_portal["用户端"]
            U_LOGIN["登录/注册"]
            U_HOME["首页/职位列表"]
            U_DETAIL["职位详情"]
            U_COMPANY["公司页面"]
            U_RESUME["简历管理"]
            U_MINE["我的投递/收藏/心愿"]
        end
        subgraph admin_portal["管理后台"]
            A_DASHBOARD["仪表盘/统计"]
            A_USER["用户管理"]
            A_THING["职位管理"]
            A_COMPANY["公司管理"]
            A_CONTENT["广告/轮播/公告"]
            A_SYSTEM["系统监控/日志"]
        end
    end

    subgraph gateway["接入层"]
        AXIOS["Axios HTTP Client<br/>请求/响应拦截<br/>TOKEN/ADMINTOKEN注入"]
        ROUTER["Vue Router<br/>路由守卫鉴权"]
    end

    subgraph backend["后端服务层 (Spring Boot 2.5.5)"]
        direction TB
        subgraph web_layer["Web层"]
            INTERCEPTOR["AccessInterceptor<br/>权限校验 / 操作日志"]
            CTRL["Controllers<br/>ThingCtrl / UserCtrl / OrderCtrl<br/>PostCtrl / CommentCtrl / ..."]
        end

        subgraph service_layer["业务逻辑层"]
            USER_SVC["UserService<br/>注册/登录/Token"]
            THING_SVC["ThingService<br/>职位CRUD/标签"]
            POST_SVC["PostService<br/>投递管理"]
            ORDER_SVC["OrderService<br/>订单处理"]
            RESUME_SVC["ResumeService<br/>简历管理"]
            COMMENT_SVC["CommentService<br/>评论/点赞"]
            COLLECT_SVC["ThingCollectService<br/>收藏管理"]
        end

        subgraph dao_layer["数据访问层"]
            MAPPER["MyBatis Plus Mappers<br/>BaseMapper<Entity><br/>自动CRUD"]
        end
    end

    subgraph go_service["Go微服务 (go-zero)"]
        GO_API["go-server-resume<br/>简历服务API"]
    end

    subgraph infra["基础设施层"]
        direction LR
        MYSQL[("MySQL 8.0<br/>数据库: java_job<br/>Docker容器")]
        FILE_SYSTEM["本地文件存储<br/>upload/avatar/<br/>upload/image/<br/>upload/resume/"]
        SWAGGER["Swagger3 / Knife4j<br/>API文档"]
    end

    %% 前端到接入层
    user_portal --> ROUTER
    admin_portal --> ROUTER
    ROUTER --> AXIOS

    %% 接入层到后端
    AXIOS -->|"HTTP /api"| INTERCEPTOR
    INTERCEPTOR --> CTRL

    %% 后端分层
    CTRL --> USER_SVC
    CTRL --> THING_SVC
    CTRL --> POST_SVC
    CTRL --> ORDER_SVC
    CTRL --> RESUME_SVC
    CTRL --> COMMENT_SVC
    CTRL --> COLLECT_SVC

    USER_SVC --> MAPPER
    THING_SVC --> MAPPER
    POST_SVC --> MAPPER
    ORDER_SVC --> MAPPER
    RESUME_SVC --> MAPPER
    COMMENT_SVC --> MAPPER
    COLLECT_SVC --> MAPPER

    %% 数据层
    MAPPER --> MYSQL
    CTRL -->|"文件上传"| FILE_SYSTEM
    GO_API --> MYSQL

    %% API文档
    CTRL -.->|"自动生成"| SWAGGER

    style client fill:#E3F2FD,stroke:#1565C0
    style gateway fill:#FFF3E0,stroke:#E65100
    style backend fill:#E8F5E9,stroke:#2E7D32
    style go_service fill:#FFF9C4,stroke:#F57F17
    style infra fill:#F3E5F5,stroke:#6A1B9A
```

## 架构说明

| 层级 | 技术 | 职责 |
|------|------|------|
| **前端展示层** | Vue 3 + TypeScript + Ant Design Vue + Pinia | 用户端/管理后台页面渲染与交互 |
| **接入层** | Vue Router + Axios | 路由守卫鉴权、HTTP请求拦截、Token注入 |
| **Web层** | Spring MVC + AccessInterceptor | 接收请求、权限校验(@Access)、操作日志记录 |
| **业务逻辑层** | Spring Service | 用户认证、职位管理、投递、订单等核心业务 |
| **数据访问层** | MyBatis Plus 3.5.2 | ORM映射、自动CRUD、SQL生成 |
| **Go微服务** | go-zero | 简历服务（独立微服务） |
| **基础设施层** | MySQL 8.0 + 本地文件系统 | 数据持久化、文件存储、API文档 |
