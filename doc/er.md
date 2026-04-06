# Job System ER 图

> 数据库: MySQL (`java_job`) | ORM: MyBatis Plus 3.5.2
> 源码路径: `/server/src/main/java/com/gk/study/entity/`

---

## ER 关系图 (Mermaid)

```mermaid
erDiagram
    b_user {
        String id PK "自增主键"
        String username "用户名"
        String password "密码(MD5)"
        String nickname "昵称"
        String mobile "手机号"
        String email "邮箱"
        String description "描述"
        String role "角色(1普通/2演示/3管理员)"
        String status "状态"
        String score "积分"
        String avatar "头像"
        String token "令牌"
        String createTime "创建时间"
        String pushEmail "推送邮箱"
        String pushSwitch "推送开关"
    }

    b_company {
        Long id PK "自增主键"
        String cover "封面"
        String title "公司名称"
        String guimo "规模"
        String hangye "行业"
        String description "描述"
        String location "地址"
        String userId FK "用户ID"
    }

    b_thing {
        Long id PK "自增主键"
        String title "职位名称"
        String cover "封面"
        String description "描述"
        String education "学历要求"
        String status "状态"
        String createTime "创建时间"
        String location "工作地点"
        String salary "薪资"
        String workExpe "工作经验"
        String pv "浏览量"
        String recommendCount "推荐数"
        String wishCount "心愿数"
        String collectCount "收藏数"
        Long classificationId FK "分类ID"
        String userId FK "发布用户ID"
        String companyId FK "所属公司ID"
    }

    b_classification {
        Long id PK "自增主键"
        String title "分类名称"
        String createTime "创建时间"
    }

    b_tag {
        Long id PK "自增主键"
        String title "标签名称"
        String createTime "创建时间"
    }

    b_thing_tag {
        Long id PK "自增主键"
        Long thingId FK "职位ID"
        Long tagId FK "标签ID"
    }

    b_resume {
        Long id PK "自增主键"
        String cover "封面"
        String name "姓名"
        String sex "性别"
        String birthday "生日"
        String raw "简历附件"
        String education "学历"
        String school "学校"
        String email "邮箱"
        String mobile "手机号"
        String userId FK "用户ID"
    }

    b_post {
        Long id PK "自增主键"
        String resumeId FK "简历ID"
        String userId FK "用户ID"
        String thingId FK "职位ID"
        String companyId FK "公司ID"
        String createTime "创建时间"
    }

    b_order {
        Long id PK "自增主键"
        String status "状态"
        String orderTime "下单时间"
        String payTime "支付时间"
        String thingId FK "职位ID"
        String userId FK "用户ID"
        String count "数量"
        String orderNumber "订单编号"
        String receiverAddress "收货地址"
        String receiverName "收货人"
        String receiverPhone "联系电话"
        String remark "备注"
    }

    b_comment {
        Long id PK "自增主键"
        String content "内容"
        String commentTime "评论时间"
        String likeCount "点赞数"
        String userId FK "用户ID"
        String thingId FK "职位ID"
    }

    b_thing_collect {
        Long id PK "自增主键"
        String thingId FK "职位ID"
        String userId FK "用户ID"
    }

    b_thing_wish {
        Long id PK "自增主键"
        String thingId FK "职位ID"
        String userId FK "用户ID"
    }

    b_address {
        Long id PK "自增主键"
        String name "收货人"
        String mobile "手机号"
        String description "地址详情"
        String def "默认地址"
        String createTime "创建时间"
        String userId FK "用户ID"
    }

    b_ad {
        Long id PK "自增主键"
        String image "图片"
        String link "链接"
        String createTime "创建时间"
    }

    b_banner {
        Long id PK "自增主键"
        String image "图片"
        Long thingId FK "关联职位ID"
        String createTime "创建时间"
    }

    b_notice {
        Long id PK "自增主键"
        String title "标题"
        String content "内容"
        String createTime "创建时间"
    }

    b_op_log {
        Long id PK "自增主键"
        String reIp "请求IP"
        String reTime "请求时间"
        String reUa "用户代理"
        String reUrl "请求URL"
        String reMethod "请求方法"
        String reContent "请求内容"
        String accessTime "访问时间"
    }

    b_error_log {
        Long id PK "自增主键"
        String ip "IP"
        String url "URL"
        String method "方法"
        String content "内容"
        String logTime "日志时间"
    }

    %% ===== 关系定义 =====

    b_user ||--o{ b_company : "用户拥有公司"
    b_user ||--o{ b_resume : "用户拥有简历"
    b_user ||--o{ b_post : "用户创建投递"
    b_user ||--o{ b_order : "用户下订单"
    b_user ||--o{ b_comment : "用户发评论"
    b_user ||--o{ b_thing_collect : "用户收藏职位"
    b_user ||--o{ b_thing_wish : "用户心愿职位"
    b_user ||--o{ b_address : "用户有地址"
    b_user ||--o{ b_thing : "用户发布职位"

    b_company ||--o{ b_thing : "公司发布职位"
    b_company ||--o{ b_post : "公司收到投递"

    b_thing ||--o{ b_comment : "职位有评论"
    b_thing ||--o{ b_thing_collect : "职位被收藏"
    b_thing ||--o{ b_thing_wish : "职位被加心愿"
    b_thing ||--o{ b_order : "职位有订单"
    b_thing ||--o{ b_thing_tag : "职位有标签"
    b_thing ||--o{ b_post : "职位被投递"
    b_thing }o--|| b_classification : "职位属于分类"
    b_thing ||--o{ b_banner : "职位关联轮播图"

    b_tag ||--o{ b_thing_tag : "标签关联职位"

    b_resume ||--o{ b_post : "简历用于投递"
```

---

## 关系汇总

| 关系 | 类型 | 说明 |
|------|------|------|
| b_user → b_company | 一对多 | 用户创建/拥有公司 |
| b_user → b_thing | 一对多 | 用户发布职位 |
| b_user → b_resume | 一对多 | 用户拥有简历 |
| b_user → b_post | 一对多 | 用户创建投递记录 |
| b_user → b_order | 一对多 | 用户下订单 |
| b_user → b_comment | 一对多 | 用户发表评论 |
| b_user → b_thing_collect | 一对多 | 用户收藏职位 |
| b_user → b_thing_wish | 一对多 | 用户加心愿 |
| b_user → b_address | 一对多 | 用户有收货地址 |
| b_company → b_thing | 一对多 | 公司发布职位 |
| b_company → b_post | 一对多 | 公司收到投递 |
| b_classification → b_thing | 一对多 | 分类下有多个职位 |
| b_thing ↔ b_tag | 多对多 | 通过 b_thing_tag 关联 |
| b_thing → b_comment | 一对多 | 职位有多条评论 |
| b_thing → b_order | 一对多 | 职位有多个订单 |
| b_thing → b_thing_collect | 一对多 | 职位被多次收藏 |
| b_thing → b_thing_wish | 一对多 | 职位被多次加心愿 |
| b_thing → b_banner | 一对多 | 职位关联轮播图 |
| b_resume → b_post | 一对多 | 简历用于多次投递 |

---

## 独立表（无外键关联）

| 表名 | 说明 |
|------|------|
| b_ad | 广告，独立无外键 |
| b_notice | 公告，独立无外键 |
| b_op_log | 操作日志，独立无外键 |
| b_error_log | 错误日志，独立无外键 |

---

## 注意事项

- 所有字段均为 VARCHAR 存储时间（非 DATETIME/TIMESTAMP）
- 外键约束由应用层逻辑维护，数据库层无 FOREIGN KEY 约束
- b_user.id 为 String 自增，其余表 id 为 Long 自增
- b_thing_tag 为 b_thing 与 b_tag 的多对多关联表
