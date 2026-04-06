# 4. 时序图 (Sequence Diagram)

> **用途**: 描述用户登录 → 浏览职位 → 投递职位的完整交互时序
> **阶段**: 系统设计阶段（详细设计）

```mermaid
sequenceDiagram
    actor User as 用户
    participant FE as Vue前端
    participant HTTP as Axios拦截器
    participant INT as AccessInterceptor
    participant CTRL as Controller
    participant SVC as Service
    participant MAP as Mapper(MyBatis)
    participant DB as MySQL

    %% ========== 登录流程 ==========
    rect rgb(230, 245, 255)
        Note over User, DB: 1. 用户登录流程
        User ->> FE: 输入用户名和密码
        FE ->> HTTP: POST /api/user/userLogin
        HTTP ->> INT: 请求到达（公开接口无需Token）
        INT ->> CTRL: UserController.userLogin()
        CTRL ->> CTRL: MD5(password + "abcd1234")
        CTRL ->> SVC: UserService.getNormalUser(username, encPwd)
        SVC ->> MAP: UserMapper.selectOne(queryWrapper)
        MAP ->> DB: SELECT * FROM b_user WHERE username=? AND password=?
        DB -->> MAP: 返回用户记录
        MAP -->> SVC: User对象
        SVC -->> CTRL: User对象
        CTRL ->> CTRL: 生成token = MD5(username+"abcd1234")
        CTRL ->> SVC: UserService.updateUser(token)
        SVC ->> MAP: UserMapper.updateById(user)
        MAP ->> DB: UPDATE b_user SET token=? WHERE id=?
        DB -->> MAP: 更新成功
        CTRL -->> HTTP: APIResponse{code:200, data:{user}}
        HTTP -->> FE: 响应数据
        FE ->> FE: localStorage存储token/userId/nickname
        FE -->> User: 登录成功，跳转首页
    end

    %% ========== 浏览职位 ==========
    rect rgb(255, 245, 230)
        Note over User, DB: 2. 浏览职位列表
        User ->> FE: 点击/搜索职位
        FE ->> HTTP: GET /api/thing/list?keyword=Java&c=1
        HTTP ->> HTTP: 添加TOKEN请求头
        HTTP ->> INT: 请求到达
        INT ->> INT: 记录操作日志(OpLog)
        INT ->> CTRL: ThingController.list(keyword, sort, c, tag)
        CTRL ->> SVC: ThingService.getThingList()
        SVC ->> MAP: ThingMapper.selectList(queryWrapper)
        MAP ->> DB: SELECT * FROM b_thing WHERE ...
        DB -->> MAP: 职位列表
        MAP -->> SVC: List~Thing~
        SVC ->> SVC: 填充tags（查询b_thing_tag+b_tag）
        SVC -->> CTRL: 完整职位列表
        CTRL -->> FE: APIResponse{data: thingList}
        FE -->> User: 渲染职位列表页
    end

    %% ========== 投递职位 ==========
    rect rgb(230, 255, 230)
        Note over User, DB: 3. 投递职位流程
        User ->> FE: 点击"投递"按钮
        FE ->> HTTP: POST /api/post/create
        HTTP ->> INT: 校验TOKEN（@Access LOGIN）
        INT ->> INT: 验证用户身份
        INT ->> CTRL: PostController.create(post)
        CTRL ->> SVC: PostService.createPost(post)
        SVC ->> MAP: PostMapper.insert(post)
        MAP ->> DB: INSERT INTO b_post(resumeId, userId, thingId, companyId)
        DB -->> MAP: 插入成功
        MAP -->> SVC: 返回成功
        SVC -->> CTRL: 操作完成
        CTRL -->> FE: APIResponse{code:200, msg:"投递成功"}
        FE -->> User: 显示投递成功提示
    end

    %% ========== 管理员操作 ==========
    rect rgb(255, 230, 230)
        Note over User, DB: 4. 管理员发布职位
        User ->> FE: 管理员填写职位表单
        FE ->> HTTP: POST /api/thing/create (multipart/form-data)
        HTTP ->> HTTP: 添加ADMINTOKEN请求头
        HTTP ->> INT: 校验管理员权限（@Access ADMIN）
        INT ->> INT: 验证role=3
        INT ->> CTRL: ThingController.create(thing, imageFile)
        CTRL ->> CTRL: 上传封面图(UUID重命名)
        CTRL ->> SVC: ThingService.createThing(thing)
        SVC ->> SVC: 处理标签关联(b_thing_tag)
        SVC ->> MAP: ThingMapper.insert(thing)
        MAP ->> DB: INSERT INTO b_thing(...)
        DB -->> MAP: 插入成功
        CTRL -->> FE: APIResponse{code:200}
        FE -->> User: 职位发布成功
    end
```

## 时序说明

| 序号 | 流程 | 涉及组件 | 权限要求 |
|------|------|----------|----------|
| 1 | 用户登录 | FE → Controller → Service → Mapper → DB | 公开 |
| 2 | 浏览职位 | FE → Interceptor → Controller → Service → DB | 公开 |
| 3 | 投递职位 | FE → Interceptor(验证) → Controller → Service → DB | LOGIN |
| 4 | 管理员发布 | FE → Interceptor(验证role=3) → Controller → Service → DB | ADMIN |
