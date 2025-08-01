## model配置文件
```conf
[request_definition]
r = sub, obj, act 

[policy_definition]
p = sub, obj, act

[policy_effect]
e = some(where (p.eft == allow))

[matchers]
m = r.sub == p.sub && r.obj == p.obj && r.act == p.act
```
## 策略文件
```svc
p, alice, data1, read
p, bob, data2, write
p, data2_admin, data2, read
p, data2_admin, data2, write
g, alice, data2_admin
```

1. 请求定义
[request_definition]部分定义了e.Enforce(...)函数中的参数。
r = sub, obj, act #request  sub即请求主体，obj即被访问资源，act即访问方法
2. 策略定义
[policy_definition] 部分定义了策略文件中的策略。
p = sub, obj, act #policy  sub即策略主体，obj即被访问资源，act即访问方法
p2 = sub, act
策略文件：
```svc
p, alice, data1, read
p2, bob, write-all-objects
```
策略中的每一行都被称为策略规则。 每个策略规则都以策略类型开始，如p或p2。 如果有多个定义，它用于匹配策略定义。 上述策略显示了以下绑定。 绑定可以在匹配器中使用。

Ptype: "p", V0: "ADMIN", V1: "/api/v1/operate/timedate", V2: "GET"
含义：ADMIN 角色可以对 /api/v1/operate/timedate 执行 GET 操作

Ptype: "g", V0: "admin", V1: "ADMIN"
含义：用户 admin 拥有 ADMIN 角色

```
(alice, data1, read) -> (p.sub, p.obj, p.act)
(bob, write-all-objects) -> (p2.sub, p2.act)
```
3. 策略效果
[policy_effect] 部分定义了e.Enforce(...)函数的返回值。如果多个策略规则匹配请求，它决定是否应批准访问请求。 例如，一条规则允许，另一条规则拒绝。
e = some(where (p.eft == allow)) #RBAC 规则：角色分配、角色授权、全选授权

上述策略效果意味着，如果有任何匹配的allow策略规则，最终效果是allow（也称为允许覆盖）。 p.eft是策略的效果，它可以是allow或deny。 这是可选的，其默认值是allow

[policy_effect]
e = some(where (p.eft == allow)) && !some(where (p.eft == deny))
这意味着必须至少有一个匹配的allow策略规则，并且不能有任何匹配的deny策略规则。

[policy_effect]
e = !some(where (p.eft == deny))
这意味着，如果没有匹配的deny策略规则，最终的效果是allow（也称为deny-override）。

4. 匹配器
[matchers] 部分定义了e.Enforce(...)函数的匹配器。它是策略匹配器的定义。 匹配器是定义如何根据请求评估策略规则的表达式。

[matchers]
m = r.sub == p.sub && r.obj == p.obj && r.act == p.act
意味着请求中的主题、对象和动作应与策略规则中的相匹配。
### RBAC 
规则：角色分配、角色授权、全选授权



## 开发流程
### 核心组件
1. provider 提供者 ：是一个策略模式的接口，定义了权限验证的统一规范：
    - 抽象不同的权限验证方式
    - 支持 Casbin、LDAP、AD 等多种实现
    - 提供统一的权限检查接口
2. adapter ：适配器，是 Casbin 的数据持久化层：
负责权限规则的存储和读取，mysql等数据库，csv、json等文件适配器，内存适配器
Adapter 是 Casbin 与存储后端的连接器，您的项目使用 GORM 适配器将策略存储在数据库中：
    - 加载权限策略到内存
    - 保存权限策略到存储介质
3. Policy 策略 定义谁可以对什么资源执行什么操作，存储在数据库表中：
类型：
    - 权限策略 (p)：定义谁可以对什么资源执行什么操作
    - 角色策略 (g)：定义用户和角色的关系
4. enforcer ：根据策略数据进行权限校验。
Enforcer 是 Casbin 的核心组件，负责加载模型和策略，并执行权限检查
Enforcer 包含了三个核心方法：
    - enforce：检查用户是否有权限对某个资源进行某个操作
    - add_policy：添加权限策略
    - remove_policy：删除权限策略
5. Model 定义了访问控制模型的结构，在您的项目中通过 model.conf 文件加载。    
Model 包含了三个核心元素：
    - Subject：主体，可以是用户、角色或组织机构等
        - User：用户
        - Role：角色
        - Group：组织机构
    - Object：被访问的资源
        - Resource：资源
    - Action：对资源执行的操作
        - Read：读取
        - Write：写入
        - Update：更新
        - Delete：删除
        - Execute：执行
        

6. Matchers（匹配器） ：定义如何匹配策略和请求
Matchers 是 Casbin 的策略匹配器，用于匹配策略和请求。
6. Matchers（匹配器） ：定义如何匹配策略和请求

### 权限初始化流程
1. 创建表 ：确保 auto_casbin_rule 表存在
2. 创建适配器 ：连接数据库
3. 创建 Enforcer ：加载模型和适配器
4. 加载策略 ：从数据库加载现有策略
5. 初始化预设数据 ：
   - 角色-操作绑定 ：为每个角色添加 API 操作权限
   - 用户-角色绑定 ：为用户分配角色
6. 存策略 ：将更新后的策略保存到数据库

### 权限检查流程
当用户访问 API 时，权限检查流程如下：
1. 从请求中获取用户信息
2. 构建 Casbin 请求（用户、资源路径、HTTP 方法）
3. 调用 Enforcer.Enforce() 检查权限
4. 根据结果允许或拒绝访问
```svc
sequenceDiagram
    participant U as 用户
    participant I as 拦截器
    participant P as Provider
    participant E as Enforcer
    participant A as Adapter
    participant D as 数据库

    U->>I: API 请求
    I->>P: CheckApiAction()
    P->>E: Enforce(user, path, method)
    E->>A: 查询权限规则
    A->>D: SELECT * FROM auto_casbin_rule
    D-->>A: 返回权限数据
    A-->>E: 权限规则
    E->>E: 应用匹配规则
    E-->>P: 验证结果
    P-->>I: AuthResult
    I-->>U: 允许/拒绝访问
```