# 9. 部署图 (Deployment Diagram)

> **用途**: 描述系统在物理/运行环境中的部署拓扑
> **阶段**: 系统部署 / 运维阶段

```mermaid
graph TB
    subgraph client_devices["客户端设备"]
        direction LR
        BROWSER["🌐 浏览器<br/>(Chrome/Firefox/Edge)"]
    end

    subgraph app_server["应用服务器<br/>(Linux / Windows)"]
        direction TB

        subgraph fe_runtime["前端运行时"]
            WEB_SERVER["Web Server<br/>(Nginx / 静态托管)<br/>端口: 80/443"]
            VUE_APP["Vue 3 SPA<br/>打包后的静态资源<br/>HTML + JS + CSS"]
        end

        subgraph java_runtime["Java运行时 (JDK 8+)"]
            SPRING["Spring Boot 内嵌Tomcat<br/>端口: 9100<br/>Context: /api"]
            subgraph spring_components["Spring组件"]
                INTERCEPTOR_COMP["AccessInterceptor<br/>权限校验"]
                CONTROLLERS["REST Controllers"]
                SERVICES["Business Services"]
                MYBATIS["MyBatis Plus"]
            end
        end

        subgraph go_runtime["Go运行时"]
            GO_SERVER["go-server-resume<br/>go-zero 微服务<br/>端口: 8888"]
        end

        subgraph file_storage["文件存储"]
            UPLOAD_DIR["upload/<br/>├── avatar/<br/>├── image/<br/>├── resume/<br/>└── company/"]
        end
    end

    subgraph docker_env["Docker 环境"]
        direction TB
        MYSQL_CONTAINER["MySQL 8.0 容器<br/>容器名: java_job_mysql<br/>端口: 3306<br/>数据卷: java_job_mysql_data"]
        MYSQL_DB[("数据库: java_job<br/>18张业务表")]
        MYSQL_CONTAINER --- MYSQL_DB
    end

    subgraph external["外部依赖"]
        SWAGGER_UI["Swagger UI<br/>(Knife4j)<br/>路径: /doc.html"]
    end

    %% 客户端到服务器
    BROWSER -->|"HTTPS"| WEB_SERVER
    BROWSER -->|"HTTP API<br/>127.0.0.1:8888/api"| SPRING
    BROWSER -->|"HTTP API<br/>127.0.0.1:8888"| GO_SERVER

    %% 前端静态资源
    WEB_SERVER -->|"serve"| VUE_APP

    %% Spring Boot内部
    SPRING --> INTERCEPTOR_COMP
    INTERCEPTOR_COMP --> CONTROLLERS
    CONTROLLERS --> SERVICES
    SERVICES --> MYBATIS

    %% 数据库连接
    MYBATIS -->|"JDBC<br/>mysql-connector-java<br/>Druid连接池"| MYSQL_CONTAINER
    GO_SERVER -->|"数据库连接"| MYSQL_CONTAINER

    %% 文件上传
    CONTROLLERS -->|"UUID重命名<br/>写入文件"| UPLOAD_DIR

    %% API文档
    SPRING -.->|"自动映射"| SWAGGER_UI

    style client_devices fill:#E3F2FD,stroke:#1565C0
    style app_server fill:#E8F5E9,stroke:#2E7D32
    style docker_env fill:#FFF3E0,stroke:#E65100
    style external fill:#F3E5F5,stroke:#6A1B9A
```

## 部署节点说明

| 节点 | 技术 | 端口 | 说明 |
|------|------|------|------|
| 浏览器 | Chrome/Firefox | - | 用户访问入口，加载Vue SPA |
| Web Server | Nginx | 80/443 | 托管Vue前端静态资源 |
| Spring Boot | JDK 8+ / Tomcat | 9100 | Java后端REST API (context: /api) |
| Go Service | go-zero | 8888 | 简历微服务API |
| MySQL容器 | Docker / MySQL 8 | 3306 | 数据库服务 (docker-compose部署) |

## Docker Compose 配置

```yaml
# docker-compose.yml
services:
  mysql:
    image: mysql:8
    container_name: java_job_mysql
    ports: "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: root123
      MYSQL_DATABASE: java_job
    volumes: mysql_data:/var/lib/mysql
```

## 关键配置

| 配置项 | 值 | 来源 |
|--------|-----|------|
| 后端端口 | 9100 | application.yml: server.port |
| API前缀 | /api | application.yml: server.servlet.context-path |
| 数据库 | java_job | application.yml: spring.datasource.url |
| 前端BASE_URL | http://127.0.0.1:8888 | web/src/store/constants.ts |
| 文件上传路径 | server/upload/ | application.yml: File.uploadPath |
| 密码加密 | MD5(明文 + abcd1234) | UserController |
| Token生成 | MD5(username + abcd1234) | UserController |
