# Google Java Style 规范

基于 Google Java Style Guide 的 Java 源代码格式与编码规范。与 Spring Boot 项目规范（见其他参考文件）结合使用。

## 目录

- 1. 源文件与导入
- 2. 格式规范
- 3. 特定构造
- 4. 命名规范
- 5. 编程实践
- 6. Javadoc
- 7. 类成员排序

---

## 1. 源文件与导入

- 文件编码 **UTF-8**；文件名 = 顶级类名 + `.java`；每个文件有且仅有一个顶级类
- 仅使用 ASCII 空格 (0x20)，**禁用 Tab**
- 特殊转义优先使用 `\n`、`\t` 等形式，不用八进制或 Unicode 转义；非 ASCII 字符用实际字符或转义取决于可读性

**导入规则：**

- **禁止通配符导入**：`import xxx.*` 一律不允许
- 导入不换行，不适用列限制
- **排序**：先所有静态导入，再所有非静态导入，两组间一个空行，组内 ASCII 排序
- 不对静态嵌套类使用静态导入

---

## 2. 格式规范

### 2.1 大括号

`if`, `else`, `for`, `do`, `while` **始终使用大括号**（即使空或单条语句）。Lambda 可选。

**K & R 风格**：开括号前不换行，开括号后换行，闭括号前换行，闭括号后仅在终止语句时换行（`else` 前不换行）。

空块可写为 `{}`，但多块语句（if/else、try/catch/finally）中禁止。

### 2.2 缩进与列限制

- **+2 空格**缩进，禁用 Tab
- 列限制 **100 字符**（例外：包声明、导入、文本块内容、长 URL）

### 2.3 换行

**优先在更高语法层级断开。**

| 构造 | 断开位置 |
|------|---------|
| 非赋值运算符（含 `.`、`::`、`&`、`\|`） | 运算符 **前** |
| 赋值运算符 / foreach 冒号 | 运算符 **后** |
| 方法/构造器名 | 与 `(` 保持连接 |
| 逗号 | 与前一个 token 连接 |

续行缩进至少 **+4 空格**，多条续行可按语法平行性调整。

### 2.4 空白

- 类成员之间始终一个空行（连续字段间可选）
- 不鼓励多个连续空行
- 水平空白：关键字与括号间（`if (`、`} else`）、开大括号前、二元运算符两侧（`::` 和 `.` 除外）、`,` `:` `;` `)` 后、类型与标识符间
- 水平对齐不要求，不为对齐修改无关行

### 2.5 其他格式规则

- 一行一条语句
- 分组括号推荐使用
- **禁止 C 风格数组**：`String[] args`（非 `String args[]`）
- 每个声明只声明一个变量（for 循环例外）
- 局部变量就近声明并初始化
- long 后缀大写 `L`：`3000000000L`
- 修饰符顺序：

```
public protected private abstract default static final sealed non-sealed
  transient volatile synchronized native strictfp
```

---

## 3. 特定构造

### 3.1 Switch

- 内容缩进 +2，case 标签与 switch 同级
- **旧式 switch fall-through 必须注释**（`// fall through`）
- **所有 switch 必须穷尽**，必须有 `default`
- **switch 表达式必须使用新式箭头语法**

```java
return switch (list.size()) {
  case 0 -> "";
  case 1 -> list.getFirst();
  default -> String.join(", ", list);
};
```

### 3.2 注解

- 类型使用注解紧跟被注解类型前：`final @Nullable String name`
- 类/方法注解每个独占一行（单个无参注解可与签名同行）
- 字段注解多个可同行：`@Partial @Mock DataLoader loader;`

### 3.3 注释

- 块注释与周围代码同缩进，多行 `/* */` 后续行以 `*` 对齐，不使用方框式注释
- TODO 格式：`// TODO: bug-reference - 描述`（全大写，避免指向个人）

---

## 4. 命名规范

- 仅使用 ASCII 字母、数字、下划线
- **禁止特殊前缀/后缀**：`name_`、`mName`、`s_name`、`kName`

### 标识符类型

| 类型 | 风格 | 示例 |
|------|------|------|
| 包/模块 | 全小写无下划线 | `com.example.deepspace` |
| 类 | UpperCamelCase | `OrderService`、`Readable` |
| 方法 | lowerCamelCase | `sendMessage` |
| 常量 | UPPER_SNAKE_CASE | `MAX_PAGE_SIZE` |
| 非常量字段 | lowerCamelCase | `computedValues` |
| 参数 | lowerCamelCase | public 方法避免单字符 |
| 局部变量 | lowerCamelCase | 即使 final 也不用常量风格 |
| 类型变量 | 单大写字母+可选数字 或 类名+T | `E`、`T2`、`RequestT` |

### 常量定义

常量 = `static final` + **内容深度不可变** + 方法无副作用。

```java
// ✅ 常量
static final int NUMBER = 5;
static final ImmutableList<String> NAMES = ImmutableList.of("Ed", "Ann");

// ❌ 非常量（即使 static final）
static final Set<String> mutableSet = new HashSet<>();  // 可变集合
static final Logger logger = ...;                        // 有副作用
static final String[] arr = {...};                       // 数组可变
```

### 驼峰转换

| 原文 | 正确 | 错误 |
|------|------|------|
| "XML HTTP request" | `XmlHttpRequest` | `XMLHTTPRequest` |
| "new customer ID" | `newCustomerId` | `newCustomerID` |
| "supports IPv6 on iOS?" | `supportsIpv6OnIos` | `supportsIPv6OnIOS` |
| "YouTube importer" | `YouTubeImporter` | `YoutubeImporter` |

测试类以 `Test` 结尾；测试方法下划线分隔：`transferMoney_deductsFromSource`

---

## 5. 编程实践

- **@Override 始终使用**（覆盖、实现、record 访问器）。例外：父方法 `@Deprecated` 时可省略
- **捕获的异常不可忽略**：确实无需操作时用注释说明原因
- **静态成员通过类名限定**：`Foo.aStaticMethod()`（非 `aFoo.aStaticMethod()`）
- **不覆盖 `Object.finalize()`**

---

## 6. Javadoc

### 格式

- 多行：`/** ... */` 各行以 `*` 对齐
- 单行（无块标签时）：`/** 简短描述 */`
- 段落间空行，段首加 `<p>`（无空格）
- 块标签顺序：`@param` → `@return` → `@throws` → `@deprecated`

### 摘要片段

每个 Javadoc 以 **摘要片段** 开头：名词/动词短语，首字母大写，以句号结尾。

```java
// ❌ /** @return the customer ID */
// ✅ /** Returns the customer ID. */
```

### 使用场景

至少为所有可见（public/protected）的类、成员和 record 组件提供 Javadoc。例外：简单 getter、覆盖方法可省略。非必须的 Javadoc 用 `/** */`（非 `//`）。

---

## 7. 类成员排序

使用 **可解释的逻辑顺序**，推荐：常量 → 字段 → 构造器 → 公共 → protected → private。**重载方法必须连续排列**，中间不插入其他成员。
