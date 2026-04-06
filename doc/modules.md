# Java Server 模块接口文档

> 源码路径: `/home/bobo.guest/job_sys/server`
> 基础路径: `/api`
> 统一响应: `APIResponse { code, msg, data, trace, timestamp }`

---

## 1. User 用户模块

**路径**: `/user` | **实体表**: `user`

| 方法 | 路径 | 参数 | 权限 | 说明 |
|------|------|------|------|------|
| GET | /list | keyword(String,可选) | 公开 | 用户列表 |
| GET | /detail | userId(String) | 公开 | 用户详情 |
| POST | /login | username, password | 公开 | 管理员登录 (role>1) |
| POST | /userLogin | username, password | 公开 | 普通用户登录 (role=1) |
| POST | /userRegister | username, password, rePassword, nickname?, mobile?, email?, avatarFile? | 公开 | 用户注册 |
| POST | /create | username, password, nickname?, mobile?, email?, role?, status?, avatarFile? | ADMIN | 创建用户 |
| POST | /delete | ids(String,逗号分隔) | ADMIN | 批量删除 |
| POST | /update | id, username?, nickname?, mobile?, email?, role?, status?, score?, description?, pushEmail?, pushSwitch?, avatarFile? | ADMIN | 更新用户(不改密码) |
| POST | /updateUserInfo | id, nickname?, mobile?, email?, description?, avatarFile?, pushEmail?, pushSwitch? | LOGIN | 用户改资料(不改用户名/密码) |
| POST | /updatePwd | userId, password, newPassword | LOGIN | 修改密码 |

**实体字段**: id, username, password, nickname, mobile, email, description, role, status, score, avatar, token, createTime, pushEmail, pushSwitch

---

## 2. Thing 职位模块

**路径**: `/thing` | **实体表**: `b_thing`

| 方法 | 路径 | 参数 | 权限 | 说明 |
|------|------|------|------|------|
| GET | /list | keyword?, sort?, c?(分类id), tag? | 公开 | 职位列表(支持搜索/排序/分类/标签筛选) |
| GET | /detail | id(String) | 公开 | 职位详情 |
| POST | /create | title, cover?, description?, education?, status?, location?, salary?, workExpe?, classificationId?, tags?, imageFile?, userId?, companyId? | ADMIN | 创建职位 |
| POST | /delete | ids(String,逗号分隔) | ADMIN | 批量删除 |
| POST | /update | id, 同create字段 | ADMIN | 更新职位 |
| GET | /listUserThingApi | userId(String) | 公开 | 用户的职位列表 |

**实体字段**: id, title, cover, description, education, status, createTime, location, salary, workExpe, pv, recommendCount, wishCount, collectCount, classificationId, tags, userId, companyId

---

## 3. Order 订单模块

**路径**: `/order` | **实体表**: `b_order`

| 方法 | 路径 | 参数 | 权限 | 说明 |
|------|------|------|------|------|
| GET | /list | - | 公开 | 订单列表 |
| GET | /userOrderList | userId(String), status(String) | 公开 | 用户订单列表(按状态) |
| POST | /create | thingId, userId, count?, orderNumber?, receiverAddress?, receiverName?, receiverPhone?, remark? | 公开 | 创建订单 |
| POST | /delete | ids(String,逗号分隔) | ADMIN | 批量删除 |
| POST | /update | id, status?, payTime?, ... | 公开 | 更新订单 |
| POST | /cancelOrder | id(Long) | ADMIN | 管理员取消订单 |
| POST | /cancelUserOrder | id(Long) | LOGIN | 用户取消订单 |

**实体字段**: id, status, orderTime, payTime, thingId, userId, count, orderNumber, receiverAddress, receiverName, receiverPhone, remark

---

## 4. Comment 评论模块

**路径**: `/comment` | **实体表**: `b_comment`

| 方法 | 路径 | 参数 | 权限 | 说明 |
|------|------|------|------|------|
| GET | /list | - | 公开 | 评论列表 |
| GET | /listThingComments | thingId(String), order(String) | 公开 | 职位评论列表 |
| GET | /listUserComments | userId(String) | 公开 | 用户评论列表 |
| POST | /create | content, userId, thingId | 公开 | 创建评论 |
| POST | /delete | ids(String,逗号分隔) | ADMIN | 批量删除 |
| POST | /update | id, content?, likeCount? | ADMIN | 更新评论 |
| POST | /like | id(String) | 公开 | 点赞评论 |

**实体字段**: id, content, commentTime, likeCount, userId, thingId

---

## 5. Post 投递模块

**路径**: `/post` | **实体表**: `b_post`

| 方法 | 路径 | 参数 | 权限 | 说明 |
|------|------|------|------|------|
| GET | /list | - | 公开 | 投递列表 |
| POST | /create | resumeId?, userId, thingId?, companyId? | ADMIN | 创建投递 |
| POST | /delete | ids(String,逗号分隔) | ADMIN | 批量删除 |
| POST | /update | id, ... | ADMIN | 更新投递 |
| GET | /listUserPostApi | userId(String) | 公开 | 用户投递列表 |
| GET | /listCompanyPostApi | companyId(String) | 公开 | 公司投递列表 |

**实体字段**: id, resumeId, userId, thingId, companyId, createTime

---

## 6. Resume 简历模块

**路径**: `/resume` | **实体表**: `b_resume`

| 方法 | 路径 | 参数 | 权限 | 说明 |
|------|------|------|------|------|
| GET | /list | - | 公开 | 简历列表 |
| GET | /detail | userId(String) | 公开 | 用户简历详情 |
| POST | /create | name, sex?, birthday?, raw?, education?, school?, email?, mobile?, userId, coverFile?, rawFile? | ADMIN | 创建简历 |
| POST | /delete | ids(String,逗号分隔) | ADMIN | 批量删除 |
| POST | /update | id, 同create字段 | ADMIN | 更新简历 |

**实体字段**: id, cover, name, sex, birthday, raw, education, school, email, mobile, userId

---

## 7. Company 公司模块

**路径**: `/company` | **实体表**: `b_company`

| 方法 | 路径 | 参数 | 权限 | 说明 |
|------|------|------|------|------|
| GET | /list | - | 公开 | 公司列表 |
| GET | /listUserCompany | userId(String) | 公开 | 用户的公司列表 |
| POST | /create | title, guimo?, hangye?, description?, location?, userId, coverFile? | ADMIN | 创建公司 |
| POST | /delete | ids(String,逗号分隔) | ADMIN | 批量删除 |
| POST | /update | id, 同create字段 | ADMIN | 更新公司 |

**实体字段**: id, cover, title, guimo, hangye, description, location, userId

---

## 8. Classification 分类模块

**路径**: `/classification` | **实体表**: `b_classification`

| 方法 | 路径 | 参数 | 权限 | 说明 |
|------|------|------|------|------|
| GET | /list | - | 公开 | 分类列表 |
| POST | /create | title | ADMIN | 创建分类 |
| POST | /delete | ids(String,逗号分隔) | ADMIN | 批量删除 |
| POST | /update | id, title? | ADMIN | 更新分类 |

**实体字段**: id, title, createTime

---

## 9. Tag 标签模块

**路径**: `/tag` | **实体表**: `b_tag`

| 方法 | 路径 | 参数 | 权限 | 说明 |
|------|------|------|------|------|
| GET | /list | - | 公开 | 标签列表 |
| POST | /create | title | ADMIN | 创建标签 |
| POST | /delete | ids(String,逗号分隔) | ADMIN | 批量删除 |
| POST | /update | id, title? | ADMIN | 更新标签 |

**实体字段**: id, title, createTime

---

## 10. ThingCollect 收藏模块

**路径**: `/thingCollect` | **实体表**: `b_thing_collect`

| 方法 | 路径 | 参数 | 权限 | 说明 |
|------|------|------|------|------|
| POST | /collect | thingId, userId | LOGIN | 收藏 |
| POST | /unCollect | id(String) | LOGIN | 取消收藏 |
| GET | /getUserCollectList | userId(String) | 公开 | 用户收藏列表 |

**实体字段**: id, thingId, userId

---

## 11. ThingWish 心愿模块

**路径**: `/thingWish` | **实体表**: `b_thing_wish`

| 方法 | 路径 | 参数 | 权限 | 说明 |
|------|------|------|------|------|
| POST | /wish | thingId, userId | LOGIN | 加入心愿单 |
| POST | /unWish | id(String) | LOGIN | 移出心愿单 |
| GET | /getUserWishList | userId(String) | 公开 | 用户心愿列表 |

**实体字段**: id, thingId, userId

---

## 12. Address 地址模块

**路径**: `/address` | **实体表**: `b_address`

| 方法 | 路径 | 参数 | 权限 | 说明 |
|------|------|------|------|------|
| GET | /list | userId(String) | 公开 | 用户地址列表 |
| POST | /create | name, mobile, description, def?, userId | 公开 | 创建地址(设默认时取消其他默认) |
| POST | /delete | ids(String,逗号分隔) | 公开 | 批量删除 |
| POST | /update | id, name?, mobile?, description?, def? | 公开 | 更新地址 |

**实体字段**: id, name, mobile, description, def, createTime, userId

---

## 13. Ad 广告模块

**路径**: `/ad` | **实体表**: `b_ad`

| 方法 | 路径 | 参数 | 权限 | 说明 |
|------|------|------|------|------|
| GET | /list | - | 公开 | 广告列表 |
| POST | /create | image?, link?, imageFile? | ADMIN | 创建广告 |
| POST | /delete | ids(String,逗号分隔) | ADMIN | 批量删除 |
| POST | /update | id, image?, link?, imageFile? | ADMIN | 更新广告 |

**实体字段**: id, image, link, createTime

---

## 14. Banner 轮播图模块

**路径**: `/banner` | **实体表**: `b_banner`

| 方法 | 路径 | 参数 | 权限 | 说明 |
|------|------|------|------|------|
| GET | /list | - | 公开 | 轮播图列表 |
| POST | /create | image?, thingId?, imageFile? | ADMIN | 创建轮播图 |
| POST | /delete | ids(String,逗号分隔) | ADMIN | 批量删除 |
| POST | /update | id, image?, thingId?, imageFile? | ADMIN | 更新轮播图 |

**实体字段**: id, image, thingId, createTime

---

## 15. Notice 公告模块

**路径**: `/notice` | **实体表**: `b_notice`

| 方法 | 路径 | 参数 | 权限 | 说明 |
|------|------|------|------|------|
| GET | /list | - | 公开 | 公告列表 |
| POST | /create | title, content | ADMIN | 创建公告 |
| POST | /delete | ids(String,逗号分隔) | ADMIN | 批量删除 |
| POST | /update | id, title?, content? | ADMIN | 更新公告 |

**实体字段**: id, title, content, createTime

---

## 16. Overview 总览模块

**路径**: `/overview`

| 方法 | 路径 | 参数 | 权限 | 说明 |
|------|------|------|------|------|
| GET | /sysInfo | - | 公开 | 系统信息(CPU/内存/OS等) |
| GET | /count | - | 公开 | 统计数据(职位数/分类数/访问量) |

---

## 17. OpLog 操作日志模块

**路径**: `/opLog` | **实体表**: `b_op_log`

| 方法 | 路径 | 参数 | 权限 | 说明 |
|------|------|------|------|------|
| GET | /list | - | 公开 | 操作日志列表 |
| GET | /loginLogList | - | 公开 | 登录日志列表 |
| POST | /create | reIp, reTime?, reUa?, reUrl?, reMethod?, reContent?, accessTime? | 公开 | 创建日志 |
| POST | /delete | ids(String,逗号分隔) | 公开 | 批量删除 |
| POST | /update | id, ... | 公开 | 更新日志 |

**实体字段**: id, reIp, reTime, reUa, reUrl, reMethod, reContent, accessTime

---

## 18. ErrorLog 错误日志模块

**路径**: `/errorLog` | **实体表**: `b_error_log`

| 方法 | 路径 | 参数 | 权限 | 说明 |
|------|------|------|------|------|
| GET | /list | - | 公开 | 错误日志列表 |
| POST | /create | ip?, url?, method?, content?, logTime? | 公开 | 创建错误日志 |
| POST | /delete | ids(String,逗号分隔) | 公开 | 批量删除 |
| POST | /update | id, ... | 公开 | 更新错误日志 |

**实体字段**: id, ip, url, method, content, logTime

---

## 权限说明

| 级别 | 注解 | 说明 |
|------|------|------|
| 公开 | 无 @Access | 无需登录即可访问 |
| LOGIN | @Access(level=LOGIN) | 需要登录 |
| ADMIN | @Access(level=ADMIN) | 需要管理员权限 |

## 通用业务规则

- **密码加密**: MD5(明文 + salt)，salt = "abcd1234"
- **Token生成**: MD5(username + salt)
- **文件上传**: UUID重命名，存储至 upload/{类型}/ 目录
- **批量删除**: ids 参数为逗号分隔的ID字符串
- **用户角色**: 1=普通用户, 2=演示账号, 3=管理员
