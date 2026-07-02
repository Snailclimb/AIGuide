# 代码风格规范（Spring Boot 专项）

通用 Java 命名、格式和编码规范见 `google-style.md`。目录结构与分层架构见 `architecture.md`。本文件聚焦 Spring Boot 项目特有的代码风格规则。

## 目录

- 1. JavaBean 约定
- 2. 不可变性
- 3. Optional 使用
- 4. Stream 最佳实践
- 5. 泛型与类型安全
- 6. 方法与成员
- 7. 现代 Java 特性（17+）
- 8. 通用代码原则

---

## 1. JavaBean 约定

### 后缀规则

| 后缀 | 用途 | 规则 |
|--------|---------|-------|
| `XxxEntity` | 数据库持久对象 | 以 Entity 结尾；Xxx 与表名一致；字段与库字段一一对应；日期统一 `LocalDateTime` / `LocalDate` |
| `XxxVO` | 返回前端对象 | 不可继承 Entity；可继承/组合 DTO、VO、BO；仅用于返回前端/RPC |
| `XxxForm` | 前端请求对象 | 不可继承 Entity；可继承/组合 DTO、VO、BO；仅用于前端/RPC 请求参数 |
| `XxxDTO` | 跨服务数据传输 | 层间通用传输 |
| `XxxBO` | 内部处理对象 | 不可继承 Entity；仅用于 Service/Manager/DAO 层，**不得出现在 Controller 层** |

### 通用规则

- 使用 **Lombok**（`@Getter`, `@Setter`, `@Builder`, `@NoArgsConstructor`, `@AllArgsConstructor`）
- 建议 **同时使用** `@Builder` + `@NoArgsConstructor`
- 字段必须用 **包装类型**（`Integer` 非 `int`），**无默认值**
- 字段注释必须使用 **多行 Javadoc**（`/** */`）
- Bean 不可包含业务逻辑或计算

### 布尔字段命名

使用 `flag` 后缀，**绝不用 "is" 前缀**（JSON 序列化丢失 "is" 前缀，RPC 反向解析出错）：

```java
private Boolean deletedFlag;    // ✅ Java 字段
// deleted_flag                 // ✅ 数据库字段
private Boolean isDeleted;      // ❌ 序列化问题
```

### 禁止直接返回 Entity

```java
// ❌ 暴露所有数据库列，包括敏感字段
public UserEntity getUser(Long userId) { ... }

// ✅ 返回 VO
public UserVO getUser(Long userId) {
    UserEntity entity = userRepository.findById(userId)
            .orElseThrow(() -> new UserNotFoundException(userId));
    return UserVO.from(entity);
}
```

### Bean 映射

- **推荐**：MapStruct（编译期，高性能）
- **可接受**：Spring BeanUtils（简单场景）
- **禁止**：Apache BeanUtils, Dozer, ModelMapper（性能差）

---

## 2. 不可变性

```java
// ✅ 优先 record 作为不可变数据载体
public record OrderSummary(Long orderId, BigDecimal total, OrderStatus status) {}

// ✅ final 字段 + 仅 getter 用于可变领域对象
public class Order {
    private final Long id;
    private final String orderNo;
    // 仅 getter，无 setter
}
```

---

## 3. Optional 使用

```java
// ✅ find* 方法返回 Optional
Optional<Market> market = marketRepository.findBySlug(slug);

// ✅ map/flatMap 链式调用，绝不在未检查时 get()
return market
    .map(MarketResponse::from)
    .orElseThrow(() -> new EntityNotFoundException("Market not found: " + slug));
```

---

## 4. Stream 最佳实践

```java
// ✅ 短小可读的管道
List<String> names = markets.stream()
    .map(Market::name)
    .filter(Objects::nonNull)
    .toList();

// ❌ 隐藏 N+1：每项一次 DB 调用
result.stream().map(c -> toDto(c, hostService.findById(c.getAgentId())))

// ✅ 批量获取，内存中关联
Map<String, Host> hostMap = hostService.findBatchByIds(agentIds).stream()
    .collect(Collectors.toMap(Host::getId, Function.identity()));
List<ContainerDto> dtos = result.stream()
    .map(c -> toDto(c, hostMap.get(c.getAgentId()))).toList();
```

---

## 5. 泛型与类型安全

- 绝不使用原始类型，显式声明泛型参数

---

## 6. 方法与成员

- 方法 ≤ 30 行，参数 ≤ 5 个（超出封装为 JavaBean，见 `architecture.md`）
- 成员顺序：常量 → 字段 → 构造器 → public → protected → private
- 重载方法必须连续排列（见 `google-style.md` 第 7 节）

---

## 7. 现代 Java 特性（17+）

- **Records** — 不可变数据载体
- **Sealed classes** — 受限类型层次
- **Pattern matching `instanceof`** — 消除转型
- **Text blocks** — 多行字符串
- **`switch` expressions** — 必须新式箭头语法（见 `google-style.md`）
- **`Stream.toList()`** — 替代 `.collect(Collectors.toList())`
- **`Optional`** — 替代 null 检查
- **Virtual threads**（21+）— 高并发 I/O

---

## 8. 通用代码原则

- 深层嵌套 → 提前返回 / 守卫语句
- 魔法数字 → 命名常量
- 静态可变状态 → 优先依赖注入
- 上帝类 / 上帝方法 → 按职责拆分
