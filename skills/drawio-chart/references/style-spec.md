# Draw.io 视觉规范

本文件定义 drawio-chart 的统一视觉风格。只有在需要生成或调整图表样式时再读取。

## 配色系统

### 核心语义类别

| 语义类别 | 填充色 | 文字色 | 边框色 | 用途 |
|---------|-------|--------|--------|------|
| **Gateway/Entry** | `#005D7B` | `#FFFFFF` | `none` | API 网关、负载均衡、入口点 |
| **Business Service** | `#E99151` | `#FFFFFF` | `none` | 核心业务服务、领域服务 |
| **Infrastructure Service** | `#7C3AED` | `#FFFFFF` | `none` | 基础设施服务（认证、日志、监控） |
| **Client/Frontend** | `#0891B2` | `#FFFFFF` | `none` | 前端、用户、客户端 |
| **External/3rd Party** | `#64748B` | `#FFFFFF` | `none` | 外部 API、第三方服务 |

### 数据存储类别

| 语义类别 | 填充色 | 文字色 | 边框色 | 用途 |
|---------|-------|--------|--------|------|
| **Primary DB** | `#E99151` | `#FFFFFF` | `none` | 主数据库、核心存储 |
| **Replica DB** | `#E4C189` | `#2D3748` | `none` | 从库、只读副本 |
| **Cache** | `#4CA497` | `#FFFFFF` | `none` | 缓存服务（Redis、Memcached） |
| **Message Queue** | `#4CA497` | `#FFFFFF` | `none` | 消息队列（Kafka、RabbitMQ） |
| **Search Engine** | `#0891B2` | `#FFFFFF` | `none` | 搜索引擎（Elasticsearch） |
| **Object Storage** | `#7C3AED` | `#FFFFFF` | `none` | 对象存储（S3、OSS） |

### 状态类别

| 语义类别 | 填充色 | 文字色 | 边框色 | 用途 |
|---------|-------|--------|--------|------|
| **Success/Status** | `#4CA497` | `#FFFFFF` | `none` | 正常流、成功状态 |
| **Alert/Danger** | `#DC2626` | `#FFFFFF` | `none` | 异常流、错误状态 |
| **Warning/Retry** | `#E99151` | `#FFFFFF` | `none` | 重试、降级、熔断状态 |
| **Info/Neutral** | `#94A3B8` | `#FFFFFF` | `none` | 中性状态、待处理 |

### 容器类别

| 语义类别 | 填充色 | 文字色 | 边框色 | 用途 |
|---------|-------|--------|--------|------|
| **Group/Infra** | `none` | `#2D3748` | `#005D7B` | 容器、网络、分组区域 |
| **Network Zone** | `#F8FAFC` | `#2D3748` | `#E2E8F0` | 网络分区、安全域 |

## 全局常量

| 属性 | 值 | 说明 |
|-----|-----|------|
| **Background** | `#F8FAFC` | 与 floracat 一致的画布背景 |
| **Font Family** | `system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif` | 系统字体栈 |
| **Font Size (Title)** | `20` | 图表主标题文字大小 |
| **Font Size (Node)** | `15` | 节点内文字大小 |
| **Font Size (Edge Label)** | `15` | 连线标签文字大小 |
| **Shape** | `rounded=1` | 所有矩形开启圆角 |
| **Edge Style** | `edgeStyle=orthogonalEdgeStyle` | 正交连线（直角） |
| **Edge Width** | `strokeWidth=2` | 连线粗细 2px |
| **Edge Color** | `#94A3B8` | 箭头色 |
| **Label BG** | `labelBackgroundColor=#F8FAFC` | 连线文字背景与画布底色同步 |

## 高级设置

| 设置 | 值 | 说明 |
|-----|-----|------|
| **Shadow** | `shadow=1` | 启用投影，模拟 floracat 的轻阴影效果 |
| **Sketch Mode** | 禁用 | 保持专业感，除非用户明确要求草图风格 |

## 使用建议

- 同一张图里，尽量让相同语义的节点颜色一致。
- 不要在一张图里无意义地堆很多颜色，优先依赖语义配色，而不是随机上色。
- 标题字号只用于图表主标题；普通节点和连线标签统一使用常规字号。
- 若图中存在容器和节点，优先让容器视觉更轻，节点视觉更重。
