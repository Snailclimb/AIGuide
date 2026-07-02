# 架构设计规范

## 目录

- 1. 项目命名
- 2. 目录结构（模块化）
- 3. 分层架构
  - 3.1 Controller 层
  - 3.2 Service 层
  - 3.3 Manager 层
  - 3.4 DAO / Repository 层
- 4. 方法参数规范
- 5. 设计模式 — 审慎使用
- 6. 统一 API 响应
- 7. 接口驱动设计

## 1. 项目命名

全部采用 **小写 + 中划线** 分隔。

```
✅ mall-management-system / order-service-client / user-api
❌ mall_management-system / mallManagementSystem / orderServiceClient
```

---

## 2. 目录结构（模块化）

按业务模块组织，每个模块一个独立顶级文件夹，内含完整的 MVC 划分。

```
src/main/java/com/example/app/
  common/                        # 各项目的通用类库
    anno/                        # 通用注解（权限、登录等）
    constant/                    # 通用常量（ResponseCodeConst 等）
    domain/                      # 全局 JavaBean（BaseEntity, PageParamDTO 等）
    exception/                   # 全局异常（BusinessException 等）
    json/                        # JSON 类库（LongJsonSerializer 等）
    swagger/                     # Swagger 文档配置
    validator/                   # 通用验证器（CheckEnum, CheckBigDecimal 等）
  config/                        # 项目配置类
  constant/                      # 全局公共常量
  handler/                       # 全局处理器
  interceptor/                   # 全局拦截器
  listener/                      # 全局监听器
  module/                        # 各业务模块（方便将来拆成微服务）
    role/                        # 角色模块
      RoleController.java
      RoleConst.java             # 模块相关常量
      RoleService.java
      RoleDao.java
      domain/
        RoleEntity.java
        RoleForm.java
        RoleVO.java
    employee/                    # 员工模块
    login/                       # 登录模块
    email/                       # 邮件模块
  third/                         # 三方服务（Redis, OSS, 微信 SDK 等）
  util/                          # 全局工具类
  Application.java               # 启动类
src/main/resources/
  application.yml
  mapper/                        # MyBatis XML mapper 文件
```

**设计意图：** 每个模块自包含，未来可独立拆分为微服务。模块内 `domain/` 包存放 Entity、DTO、VO、BO 等 JavaBean 对象。

---

## 3. 分层架构

```
Controller → Service → Manager → DAO
```

| 层 | 职责 | 核心约束 |
|----|------|---------|
| Controller | 路由和委托 | 无业务逻辑、无复杂校验、无数据组装 |
| Service | 业务逻辑编排 | 合理拆分大 Service；验证逻辑放此处 |
| Manager | 通用能力下沉 + DAO 组合 | `@Transactional` 放在此层；封装第三方平台 |
| DAO | 数据访问 | MyBatis-Plus；禁用 Wrapper |

### 3.1 Controller 层

**仅负责路由和委托**，绝不可包含：
- 业务逻辑或计算
- 复杂参数校验（仅使用 `@Valid` / `@Validated` 声明式校验）
- 数据组装、转换或字段赋值
- 直接的数据库/缓存/MQ 操作

**URL 命名规则：** `/业务模块/子模块/动作`，只使用 GET/POST 方法。

```java
// ✅ 正确：URL 动词命名，仅 GET/POST
GET  /department/get/{id}      // 查询详情
POST /department/query         // 复杂查询
POST /department/add           // 添加
POST /department/update        // 更新
GET  /department/delete/{id}   // 删除
GET  /department/employee/delete/{id}  // 子模块操作

// ❌ 错误：RESTful 风格（不推荐，不利于一眼识别操作）
PUT  /department/{id}
DELETE /department/{id}
```

**`@RequestMapping` 仅放在方法上**，不放在类上（便于全局搜索 URL）：

```java
// ✅ 正确
@RestController
public class DepartmentController {

    @GetMapping("/department/list")
    public ResponseDTO<List<DepartmentVO>> listDepartment() {
        return departmentService.listDepartment();
    }
}

// ❌ 错误：@RequestMapping 放在类上
@RequestMapping("/department")
public class DepartmentController {
    @GetMapping("/list")
    public ResponseDTO<List<DepartmentVO>> listDepartment() { ... }
}
```

**Swagger 注解必须包含后端作者：**

```java
@ApiOperation("更新部门信息 @author 卓大")
@PostMapping("/department/update")
public ResponseDTO<String> updateDepartment(@Valid @RequestBody DepartmentUpdateForm form) {
    return departmentService.updateDepartment(form);
}
```

**只能在 Controller 层获取当前请求用户**（从 ThreadLocal 获取，其他层可能非 request 线程调用）：

```java
@PostMapping("/employee/add")
public ResponseDTO<String> addEmployee(@Valid @RequestBody EmployeeAddForm form) {
    RequestUser requestUser = SmartRequestUtil.getRequestUser();
    return employeeService.addEmployee(form, requestUser);
}
```

### 3.2 Service 层

**合理拆分大 Service：** 按功能拆分为独立 Service。

```
OrderQueryService      订单查询业务
OrderCreateService     订单新建业务
OrderDeliverService    订单发货业务
OrderValidatorService  订单验证业务
```

**Service 负责业务编排和验证，数据准备完成后委托 Manager 层执行事务操作：**

```java
// ✅ 正确：验证在 Service（无事务），DB 操作在 Manager（有事务）
// DepartmentService.java
public ResponseDTO<String> upOrDown(Long departmentId, Long swapId) {
    DepartmentEntity departmentEntity = departmentDao.selectById(departmentId);
    if (departmentEntity == null) {
        return ResponseDTO.wrap(DepartmentResponseCodeConst.NOT_EXISTS);
    }
    DepartmentEntity swapEntity = departmentDao.selectById(swapId);
    if (swapEntity == null) {
        return ResponseDTO.wrap(DepartmentResponseCodeConst.NOT_EXISTS);
    }
    Long count = employeeDao.countByDepartmentId(departmentId);
    if (count != null && count > 0) {
        return ResponseDTO.wrap(DepartmentResponseCodeConst.EXIST_EMPLOYEE);
    }
    departmentManager.upOrDown(departmentEntity, swapEntity);
    return ResponseDTO.succ();
}

// DepartmentManager.java
@Transactional(rollbackFor = Exception.class)
public void upOrDown(DepartmentEntity departmentEntity, DepartmentEntity swapEntity) {
    Long departmentSort = departmentEntity.getSort();
    departmentEntity.setSort(swapEntity.getSort());
    departmentDao.updateById(departmentEntity);
    swapEntity.setSort(departmentSort);
    departmentDao.updateById(swapEntity);
}
```

**`@Transactional` 使用注意事项：**

1. **`rollbackFor` 必须指定 `Exception.class`**
2. **保持事务范围最小**：将验证、远程调用等非 DB 操作移到事务方法之外
3. **类内部方法调用 `@Transactional` 不生效**（Spring AOP 代理限制）

```java
// ❌ 错误：同类内部调用，事务不生效
@Service
public class OrderService {
    public void createOrder(OrderAddForm form) {
        this.saveData(form); // 直接调用，不走代理，事务无效！
    }

    @Transactional(rollbackFor = Exception.class)
    public void saveData(OrderAddForm form) {
        orderDao.insert(form);
    }
}

// ✅ 解决方案 1：将事务方法移到 Manager 层（推荐）
// OrderService.java
public void createOrder(OrderAddForm form) {
    orderManager.saveData(form); // 跨类调用，走代理
}

// ✅ 解决方案 2：使用 AopContext（需启动类加 @EnableAspectJAutoProxy(exposeProxy = true)）
public void createOrder(OrderAddForm form) {
    OrderService proxy = (OrderService) AopContext.currentProxy();
    proxy.saveData(form);
}
```

### 3.3 Manager 层

Manager 层的三大职责（引自《阿里 Java 手册》）：

1. **对第三方平台封装**：预处理返回结果及转化异常信息
2. **对 Service 层通用能力下沉**：缓存方案、中间件通用处理
3. **与 DAO 层交互**：对多个 DAO 的组合复用

**`@Transactional` 推荐放在 Manager 层**，而非 Service 层。Service 准备好数据后传递给 Manager 执行事务操作。

### 3.4 DAO / Repository 层

- 持久化优先使用 **MyBatis-Plus**；JPA 项目使用 Spring Data JPA
- 所有 Dao 继承自 `BaseMapper`
- **MyBatis-Plus 规则**：
  - **禁止 Wrapper 查询构建器**：所有查询逻辑必须在 XML mapper 中，便于 SQL 复用和慢查询追踪
  - **禁止 XML 中硬编码常量**：所有状态值/常量必须通过 `@Param` 传入
  - **XML 中 JOIN 使用完整表名**，禁止使用别名（`tn`、`e` 等晦涩简称）

```java
// ✅ 常量通过参数传入
public interface NoticeDao {
    Integer noticeCount(@Param("sendStatus") Integer sendStatus);
}

// ✅ XML 使用完整表名
// SELECT t_notice.*, t_employee.actual_name AS createUserName
// FROM t_notice
// LEFT JOIN t_employee ON t_notice.create_user_id = t_employee.employee_id

// ❌ XML 中使用别名（难以区分）
// SELECT tn.*, e.actual_name FROM t_notice tn LEFT JOIN t_employee e ON ...
```

将 DB/缓存/MQ 访问代码分离到专用类，绝不与业务逻辑混合。

---

## 4. 方法参数规范

每个方法最多 **5 个参数**，超出则封装成 JavaBean 对象。

```java
// ❌ 错误：参数过多，同类型参数仅靠顺序区分
public String signEnvelop(JdRequestParam param, String password,
    String priCert, String pubCert, String username, String ip, String userAgent) { }

// ✅ 正确：封装为 JavaBean
public String signEnvelop(SignEnvelopRequest request) { }
```

**原因：**
- 降低出错几率（尤其是同类型参数仅靠顺序区分）
- 保持代码整洁清晰
- 方便他人调用

---

## 5. 设计模式 — 审慎使用

当模式解决实际问题时应用。绝不为用模式而用模式。

### 策略模式 — 替代复杂条件分支

当方法包含可能增长的复杂分支逻辑时，将每个分支提取为策略实现。

简单场景下，`Map<String, Function<>>` 能以更少样板代码达到同样效果。

### 建造者模式 — 复杂对象构建

```java
Caffeine.newBuilder()
    .expireAfterWrite(60, TimeUnit.MINUTES)
    .initialCapacity(100)
    .maximumSize(500)
    .build();
```

### 责任链模式 — 多步骤处理

当请求经过多个顺序处理器（过滤器、验证器、处理器）时使用。

### 观察者模式 — 事件解耦

使用 Spring `ApplicationEvent` 或者消息队列将副作用逻辑（通知、审计日志）与核心业务流程解耦。

### 模板方法模式 — 共享流程，不同步骤

将公共流程提取到抽象父类；将变化步骤留作抽象方法供子类实现。

---

## 6. 统一 API 响应

所有 API 端点必须返回一致的 `Result<T>` 结构：

```java
public class Result<T> {
    private Integer code;
    private String message;
    private T data;
    //...
}
```

---

## 7. 接口驱动设计

- 面向接口编程，而非实现
- Service 层应定义接口，实现类注入到 Controller
- 便于测试、替换实现、AOP 代理
