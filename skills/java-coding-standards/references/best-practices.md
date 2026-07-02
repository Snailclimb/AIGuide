# 最佳实践

## 目录

- 1. 异常处理
- 2. 日志规范
- 3. 性能优化
- 4. 线程池使用
- 5. 远程调用
- 6. 敏感数据处理
- 7. 单元测试（AIR & BCDE）
- 8. 数据库设计

## 1. 异常处理

### 通用规则

- 领域错误使用 **非受检异常**；创建领域专用异常类
- 避免宽泛的 `catch (Exception ex)`，除非在中心位置重新抛出/记录
- 抛出完整、具体的异常信息 —— 绝不 `throw new BizException(e.getMessage())`
- **绝不可将异常定义为静态变量**（导致堆栈跟踪损坏）
- **绝不可同时记录和重新抛出**同一异常（导致重复日志条目）

```java
// ✅ 领域异常
throw new OrderNotFoundException("Order not found: " + orderNo);

// ❌ 静态异常实例（堆栈跟踪会错误）
public static final BusinessException ORDER_EXISTS = new BusinessException("Order exists", 3001);

// ❌ 记录后重新抛出（重复日志）
try { ... }
catch (IllegalArgumentException e) {
    log.error("Error", e);
    throw e; // 在全局处理器中重复
}

// ✅ 要么记录要么重新抛出/包装
catch (IllegalArgumentException e) {
    throw new MyBusinessException("Context description", e);
}
```

### 全局异常处理器

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(BusinessException.class)
    public Result<?> handleBusiness(BusinessException e) {
        return Result.fail(e.getCode(), e.getMessage());
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public Result<?> handleValidation(MethodArgumentNotValidException e) {
        String msg = e.getBindingResult().getFieldErrors().stream()
            .map(FieldError::getDefaultMessage)
            .collect(Collectors.joining("; "));
        return Result.fail(ResultEnum.VALIDATE_FAILED, msg);
    }
}
```

### 使用 try-with-resources

```java
// ✅ 对 AutoCloseable 始终使用 try-with-resources
try (var in = new BufferedInputStream(new FileInputStream("input.txt"));
     var out = new BufferedOutputStream(new FileOutputStream("output.txt"))) {
    in.transferTo(out);
}
```

---

## 2. 日志规范

```java
// ✅ 使用 SLF4J 门面，绝不直接使用 Log4j/Logback API
private static final Logger log = LoggerFactory.getLogger(OrderService.class);

// ✅ 结构化 key=value 用于可观测性
log.info("order_created orderId={} userId={}", orderId, userId);
log.error("order_creation_failed orderId={}", orderId, ex); // 传入完整异常

// ❌ 丢失堆栈跟踪
log.error("Error: {}", e.getMessage()); // 堆栈跟踪丢失！

// ✅ 完整异常作为最后一个参数
log.error("File read failed", e);
```

**日志规则：**

1. 只记录实际用于调试或监控的内容
2. **日志中脱敏敏感数据**（身份证、手机号、密码）
3. 使用正确级别：`DEBUG`（仅开发，生产禁用）、`INFO`（正常流程）、`WARN`（可恢复问题）、`ERROR`（影响功能）
4. **生产环境禁止 DEBUG 日志**
5. 避免跨调用层重复日志 —— 在处理错误的层记录
6. 绝不同时记录和重新抛出同一异常

---

## 3. 性能优化

### 数据库访问

```java
// ❌ 绝不循环调用数据库
list.forEach(item -> repository.insert(item));

// ✅ 批量操作
repository.batchInsert(list);
```

提示：MySQL JDBC URL 中启用 `rewriteBatchedStatements=true`；批量插入预生成 ID；大数据集使用异步多线程批处理。

### 串行 → 并行

当 API 从多个独立来源聚合数据时，并发调用：

```java
CompletableFuture<UserInfo> userFuture = CompletableFuture.supplyAsync(() -> userService.getInfo(userId));
CompletableFuture<List<Order>> orderFuture = CompletableFuture.supplyAsync(() -> orderService.list(userId));
CompletableFuture.allOf(userFuture, orderFuture).join();
```

### 异步处理

将非关键或长时间运行的工作（邮件、文件处理、数据同步）通过线程池、`@Async`、`CompletableFuture` 或消息队列异步执行。

### 池化资源

使用连接池（HikariCP、Jedis Pool）和线程池。绝不为每个请求创建/销毁昂贵资源。

### 降低锁粒度

```java
// ✅ 仅同步关键部分
public void process() {
    // 非关键工作...
    synchronized (lock) {
        // 仅关键部分
    }
    // 更多非关键工作...
}

// ✅ 对不相关操作使用独立锁
private final Object lock1 = new Object();
private final Object lock2 = new Object();
```

共享变量优先乐观并发（`AtomicInteger`、`LongAdder`、CAS）而非悲观锁。

### 方法参数优先使用基本类型

```java
// ✅ 避免自动装箱和 null 检查
public static int add(int a, int b) { return a + b; }

// ❌ 不必要的装箱
public static Integer add(Integer a, Integer b) { ... }
```

### 使用缓冲 I/O 和零拷贝

- 始终用 `BufferedInputStream` / `BufferedOutputStream` 包装流
- 大文件传输使用 `FileChannel.transferTo()` 或 `MappedByteBuffer`（零拷贝）

### 避免长事务

- `@Transactional` 内无远程调用
- 单个事务内不处理大数据集
- 高并发场景优先编程式事务（`TransactionTemplate`）而非 `@Transactional`

### 使用局部变量

优先局部变量而非成员变量：栈分配（无 GC 压力）、天然线程安全、访问更快。

---

## 4. 线程池使用

1. **绝不**使用 `Executors.newFixedThreadPool()` / `newCachedThreadPool()` —— OOM 风险
2. 始终通过 `ThreadPoolExecutor` 构造器创建，参数显式指定
3. **为线程命名**便于调试（`new ThreadFactoryBuilder().setNameFormat("order-pool-%d").build()`）
4. 不同业务域使用独立线程池
5. 监控线程池指标（队列大小、活跃数、拒绝数）
6. 始终优雅关闭线程池
7. 警惕 `ThreadLocal` 在池化线程间泄漏 —— 在 `finally` 中清理

---

## 5. 远程调用

**始终设置超时**：`connectTimeout` 和 `readTimeout` 都要设置。缺失超时可能导致连接耗尽和级联系统故障。

```java
RestTemplate restTemplate = new RestTemplateBuilder()
    .setConnectTimeout(Duration.ofSeconds(3))
    .setReadTimeout(Duration.ofSeconds(10))
    .build();
```

---

## 6. 敏感数据处理

1. **前端响应**：脱敏敏感字段（`163****892`）
2. **数据库密码**：加盐哈希（BCrypt）
3. **数据库 PII**（身份证、银行卡）：对称加密（AES）
4. **传输中**：HTTPS + 敏感载荷非对称加密（RSA）
5. **代码中**：绝不硬编码密钥 —— 使用配置文件、环境变量或密钥库

---

## 7. 单元测试（AIR & BCDE）

### AIR 原则

- **Automatic**：完全自动化，无人工干预
- **Independent**：测试不可相互依赖或依赖执行顺序
- **Repeatable**：本地、CI、任何时间结果一致

### BCDE 原则

- **Border**：覆盖循环边界、边界情况、null、空集合
- **Correct**：验证有效输入的正确输出
- **Design**：确保实现符合业务设计文档
- **Error**：测试无效输入和预期异常

### 执行规则

- 测试粒度：类或方法级别。跨系统测试归入集成测试套件
- 所有测试代码位于 `src/test/java`
- **覆盖率目标**：整体 ≥ 70%；核心业务模块：100% 语句 + 分支覆盖
- **数据库测试**：绝不假设预存在数据。通过代码/mocks 准备数据。启用自动回滚或用特殊前缀标记测试数据
- **如果代码难以测试，就重构它**。避免复杂构造器、全局可变状态或深层嵌套条件

### 工具

- **JUnit 5 + AssertJ** 用于流畅断言
- **Mockito** 用于 mock；避免部分 mock
- 优先确定性测试 —— 无隐藏 sleep 或时间依赖

```java
@Test
void shouldReturnUserWhenIdExists() {
    // given
    when(userRepository.findById(1L)).thenReturn(Optional.of(testUser));
    // when
    UserVO result = userService.getUserById(1L);
    // then
    assertThat(result.getName()).isEqualTo("Alice");
}
```

---

## 8. 数据库设计

### 命名

- 数据库名：`lowercase_underscored_env`（如 `smart_admin_v2_dev`）
- 表名：`t_` 前缀 + lowercase_underscored（如 `t_employee`）

### 每表必需列

| 列 | 类型 | 默认值 |
|--------|------|---------|
| `[module]_id` | BIGINT, 自增主键 | — |
| `create_time` | DATETIME | `CURRENT_TIMESTAMP` |
| `update_time` | DATETIME | `CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP` |

### 注释

- 枚举/状态列必须在 COMMENT 中列出所有可能值（如 `COMMENT '状态 0:未开始, 1:进行中, 2:完成'`）
- 逻辑变更时更新注释

### 索引

遵循阿里巴巴 Java 开发手册索引指南（覆盖复合索引顺序、覆盖索引使用、索引选择性等）。
