---
name: drawio-chart
description: Draw.io 配图专家：根据需求生成专业的 draw.io 图表，支持流程图、架构图、时序图、ER图等，可导出为 PNG/SVG/PDF 格式。视觉风格与 floracat-architecture-diagram 保持一致。
---

# Draw.io 配图协议

## 工作模式

| 场景 | 模式 | 说明 |
|-----|------|------|
| 用户描述图表需求 | **生成模式** | 根据描述生成 draw.io XML 格式图表 |
| 指定导出格式 | **导出模式** | 将生成的 .drawio 文件导出为 PNG/SVG/PDF |

## 支持的图表类型

| 类型 | 适用场景 | 关键元素 |
|-----|---------|---------|
| **流程图** | 业务流程、算法逻辑、决策树 | 圆角矩形、菱形判断、箭头连线 |
| **架构图** | 系统架构、模块关系 | 分层容器、组件框、数据流向 |
| **时序图** | 服务调用、消息传递 | 生命线、消息箭头、激活条 |
| **ER图** | 数据库设计、实体关系 | 圆柱体、关系线、属性框 |
| **状态机图** | 状态转换、生命周期 | 圆形/圆角矩形、转换箭头 |
| **思维导图** | 知识结构、概念梳理 | 中心节点、分支节点、层级关系 |

---

## 一、通用技术架构绘图规范

### 配色系统

#### 核心语义类别

| 语义类别 | 填充色 | 文字色 | 边框色 | 用途 |
|---------|-------|--------|--------|------|
| **Gateway/Entry** | `#005D7B` | `#FFFFFF` | `none` | API网关、负载均衡、入口点 |
| **Business Service** | `#E99151` | `#FFFFFF` | `none` | 核心业务服务、领域服务 |
| **Infrastructure Service** | `#7C3AED` | `#FFFFFF` | `none` | 基础设施服务（认证、日志、监控） |
| **Client/Frontend** | `#0891B2` | `#FFFFFF` | `none` | 前端、用户、客户端 |
| **External/3rd Party** | `#64748B` | `#FFFFFF` | `none` | 外部API、第三方服务 |

#### 数据存储类别

| 语义类别 | 填充色 | 文字色 | 边框色 | 用途 |
|---------|-------|--------|--------|------|
| **Primary DB** | `#E99151` | `#FFFFFF` | `none` | 主数据库、核心存储 |
| **Replica DB** | `#E4C189` | `#2D3748` | `none` | 从库、只读副本 |
| **Cache** | `#4CA497` | `#FFFFFF` | `none` | 缓存服务（Redis、Memcached） |
| **Message Queue** | `#4CA497` | `#FFFFFF` | `none` | 消息队列（Kafka、RabbitMQ） |
| **Search Engine** | `#0891B2` | `#FFFFFF` | `none` | 搜索引擎（Elasticsearch） |
| **Object Storage** | `#7C3AED` | `#FFFFFF` | `none` | 对象存储（S3、OSS） |

#### 状态类别

| 语义类别 | 填充色 | 文字色 | 边框色 | 用途 |
|---------|-------|--------|--------|------|
| **Success/Status** | `#4CA497` | `#FFFFFF` | `none` | 正常流、成功状态 |
| **Alert/Danger** | `#DC2626` | `#FFFFFF` | `none` | 异常流、错误状态 |
| **Warning/Retry** | `#E99151` | `#FFFFFF` | `none` | 重试、降级、熔断状态 |
| **Info/Neutral** | `#94A3B8` | `#FFFFFF` | `none` | 中性状态、待处理 |

#### 容器类别

| 语义类别 | 填充色 | 文字色 | 边框色 | 用途 |
|---------|-------|--------|--------|------|
| **Group/Infra** | `none` | `#2D3748` | `#005D7B` | 容器、网络、分组区域 |
| **Network Zone** | `#F8FAFC` | `#2D3748` | `#E2E8F0` | 网络分区、安全域 |

### 全局常量

| 属性 | 值 | 说明 |
|-----|-----|------|
| **Background** | `#F8FAFC` | 与 floracat 一致的画布背景 |
| **Font Family** | `system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif` | 系统字体栈，与 floracat 一致 |
| **Font Size (Title)** | `20` | 图表主标题文字大小 |
| **Font Size (Node)** | `15` | 节点内文字大小 |
| **Font Size (Edge Label)** | `15` | 连线标签文字大小 |
| **Shape** | `rounded=1` | 所有矩形开启圆角 |
| **Edge Style** | `edgeStyle=orthogonalEdgeStyle` | 正交连线（直角） |
| **Edge Width** | `strokeWidth=2` | 连线粗细 2px |
| **Edge Color** | `#94A3B8` | 与 floracat 箭头色一致 |
| **Label BG** | `labelBackgroundColor=#F8FAFC` | 连线文字背景与画布底色同步 |

### 高级设置

| 设置 | 值 | 说明 |
|-----|-----|------|
| **Shadow** | `shadow=1` | 启用投影（draw.io 内置轻阴影，模拟 floracat 的 feDropShadow 效果） |
| **Sketch Mode** | 禁用 | 保持专业感，除非用户明确要求草图风格 |

---

## 二、XML 结构规范

### 文本标签规则

- `mxCell` 的 `value` 中禁止使用 HTML 标签，例如 `&lt;b&gt;`、`&lt;/b&gt;`、`&lt;br&gt;`、`<b>`、`<br>`。
- 需要换行时使用 XML 换行实体 `&#xa;`，不要使用 `<br>` 或 `&lt;br&gt;`。
- 需要强调整段文字时使用样式字段（如 `fontStyle=1`）或拆成独立文本节点，不要在 `value` 中嵌入 HTML。
- 文本节点和图形节点默认使用纯文本渲染，样式中优先设置 `html=0`；除非用户明确要求并确认渲染链支持 HTML，否则不要使用 `html=1`。
- 生成或修改 `.drawio` 后，必须检查文件中是否残留 HTML 标签，重点搜索 `&lt;`、`<b>`、`<br>`。

### 连线标签规则

- 短连接线不要放长标签；如果两个节点距离较近，连线 `value` 应留空，或只放“是 / 否 / 成功 / 失败”这类 1-2 个词。
- 分类说明、原因说明、动作说明优先写进目标节点或旁注节点，不要压在连接线上。
- 连线标签不得覆盖箭头主体。生成或修改流程图后，要重点检查短横线、短竖线、菱形节点左右出口处的标签。

### 基础模板

```xml
<?xml version="1.0" encoding="UTF-8"?>
<mxfile host="app.diagrams.net" modified="2025-01-01T00:00:00.000Z" agent="drawio-chart" version="24.0.0">
  <diagram name="Page-1" id="diagram-id">
    <mxGraphModel dx="1422" dy="794" grid="1" gridSize="10" guides="1" tooltips="1" connect="1" arrows="1" fold="1" page="1" pageScale="1" pageWidth="827" pageHeight="1169" math="0" shadow="0" background="#F8FAFC">
      <root>
        <mxCell id="0"/>
        <mxCell id="1" parent="0"/>
        <!-- 图表内容 -->
      </root>
    </mxGraphModel>
  </diagram>
</mxfile>
```

### 标题样式模板

图表主标题必须使用 `fontSize=20`。除主标题外，节点、容器标题、分组标签、连线标签等常规文字统一使用 `fontSize=15`。

```xml
<mxCell id="title" value="图表标题" style="text;html=0;align=center;verticalAlign=middle;resizable=0;points=[];autosize=1;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=20;fontStyle=1;fontColor=#2D3748;" vertex="1" parent="1">
  <mxGeometry x="260" y="20" width="300" height="40" as="geometry"/>
</mxCell>
```

### 节点样式模板

以下模板中的 `{语义类别}` 对应配色表中的行，直接替换 `fillColor`/`fontColor` 即可。

#### 核心语义节点

**通用节点模板（替换 fillColor 即可）：**
```xml
<mxCell id="node-id" value="节点名称" style="rounded=1;whiteSpace=wrap;fillColor={类别填充色};strokeColor=none;fontColor={类别文字色};fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;shadow=1;" vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="140" height="60" as="geometry"/>
</mxCell>
```

**Gateway/Entry 节点（API网关）：**
```xml
<mxCell id="node-id" value="API 网关" style="rounded=1;whiteSpace=wrap;fillColor=#005D7B;strokeColor=none;fontColor=#FFFFFF;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;shadow=1;" vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="140" height="60" as="geometry"/>
</mxCell>
```

**Business Service 节点（业务服务）：**
```xml
<mxCell id="node-id" value="用户服务" style="rounded=1;whiteSpace=wrap;fillColor=#E99151;strokeColor=none;fontColor=#FFFFFF;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;shadow=1;" vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="140" height="60" as="geometry"/>
</mxCell>
```

**Infrastructure Service 节点（基础设施服务）：**
```xml
<mxCell id="node-id" value="认证服务" style="rounded=1;whiteSpace=wrap;fillColor=#7C3AED;strokeColor=none;fontColor=#FFFFFF;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;shadow=1;" vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="140" height="60" as="geometry"/>
</mxCell>
```

**Client/Frontend 节点（前端/客户端）：**
```xml
<mxCell id="node-id" value="Web 客户端" style="rounded=1;whiteSpace=wrap;fillColor=#0891B2;strokeColor=none;fontColor=#FFFFFF;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;shadow=1;" vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="140" height="60" as="geometry"/>
</mxCell>
```

**External/3rd Party 节点（外部服务）：**
```xml
<mxCell id="node-id" value="支付 API" style="rounded=1;whiteSpace=wrap;fillColor=#64748B;strokeColor=none;fontColor=#FFFFFF;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;shadow=1;" vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="140" height="60" as="geometry"/>
</mxCell>
```

#### 数据存储节点

**Primary DB 节点（主数据库）：**
```xml
<mxCell id="node-id" value="MySQL 主库" style="shape=cylinder3;whiteSpace=wrap;fillColor=#E99151;strokeColor=none;fontColor=#FFFFFF;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;shadow=1;" vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="120" height="80" as="geometry"/>
</mxCell>
```

**Replica DB 节点（从库）：**
```xml
<mxCell id="node-id" value="MySQL 从库" style="shape=cylinder3;whiteSpace=wrap;fillColor=#E4C189;strokeColor=none;fontColor=#2D3748;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;shadow=1;" vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="120" height="80" as="geometry"/>
</mxCell>
```

**Cache 节点（缓存）：**
```xml
<mxCell id="node-id" value="Redis 缓存" style="rounded=1;whiteSpace=wrap;fillColor=#4CA497;strokeColor=none;fontColor=#FFFFFF;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;shadow=1;" vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="140" height="60" as="geometry"/>
</mxCell>
```

**Message Queue 节点（消息队列）：**
```xml
<mxCell id="node-id" value="Kafka 集群" style="rounded=1;whiteSpace=wrap;fillColor=#4CA497;strokeColor=none;fontColor=#FFFFFF;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;shadow=1;" vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="140" height="60" as="geometry"/>
</mxCell>
```

**Search Engine 节点（搜索引擎）：**
```xml
<mxCell id="node-id" value="Elasticsearch" style="rounded=1;whiteSpace=wrap;fillColor=#0891B2;strokeColor=none;fontColor=#FFFFFF;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;shadow=1;" vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="140" height="60" as="geometry"/>
</mxCell>
```

**Object Storage 节点（对象存储）：**
```xml
<mxCell id="node-id" value="S3 存储" style="shape=cylinder3;whiteSpace=wrap;fillColor=#7C3AED;strokeColor=none;fontColor=#FFFFFF;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;shadow=1;" vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="120" height="80" as="geometry"/>
</mxCell>
```

#### 状态节点

**Success 节点（正常流）：**
```xml
<mxCell id="node-id" value="处理成功" style="rounded=1;whiteSpace=wrap;fillColor=#4CA497;strokeColor=none;fontColor=#FFFFFF;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;shadow=1;" vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="140" height="60" as="geometry"/>
</mxCell>
```

**Alert/Danger 节点（异常流）：**
```xml
<mxCell id="node-id" value="处理失败" style="rounded=1;whiteSpace=wrap;fillColor=#DC2626;strokeColor=none;fontColor=#FFFFFF;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;shadow=1;" vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="140" height="60" as="geometry"/>
</mxCell>
```

**Warning 节点（重试/降级）：**
```xml
<mxCell id="node-id" value="服务降级" style="rounded=1;whiteSpace=wrap;fillColor=#E99151;strokeColor=none;fontColor=#FFFFFF;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;shadow=1;" vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="140" height="60" as="geometry"/>
</mxCell>
```

**菱形判断节点：**
```xml
<mxCell id="decision-id" value="条件?" style="rhombus;whiteSpace=wrap;fillColor=#005D7B;strokeColor=none;fontColor=#FFFFFF;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;shadow=1;" vertex="1" parent="1">
  <mxGeometry x="100" y="200" width="120" height="80" as="geometry"/>
</mxCell>
```

**分组容器（Group/Infra）：**
```xml
<mxCell id="group-id" value="服务集群" style="swimlane;whiteSpace=wrap;fillColor=none;strokeColor=#005D7B;dashed=1;strokeWidth=2;fontColor=#2D3748;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;" vertex="1" parent="1">
  <mxGeometry x="50" y="50" width="400" height="250" as="geometry"/>
</mxCell>
```

### 连线样式模板

**标准连线（带标签）：**
```xml
<mxCell id="edge-id" value="HTTP" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;strokeWidth=2;strokeColor=#94A3B8;labelBackgroundColor=#F8FAFC;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;fontColor=#64748B;" edge="1" source="source-id" target="target-id" parent="1">
  <mxGeometry relative="1" as="geometry"/>
</mxCell>
```

**无标签连线：**
```xml
<mxCell id="edge-id" value="" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;strokeWidth=2;strokeColor=#94A3B8;" edge="1" source="source-id" target="target-id" parent="1">
  <mxGeometry relative="1" as="geometry"/>
</mxCell>
```

**虚线连接（异步/间接）：**
```xml
<mxCell id="edge-id" value="异步消息" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;dashed=1;strokeWidth=2;strokeColor=#94A3B8;labelBackgroundColor=#F8FAFC;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;fontColor=#64748B;" edge="1" source="source-id" target="target-id" parent="1">
  <mxGeometry relative="1" as="geometry"/>
</mxCell>
```

---

## 三、操作流程

### 生成模式

1. **分析需求**：理解用户要表达的内容和关系
2. **选择图表类型**：根据内容选择最合适的图表类型
3. **规划布局**：确定节点位置和连接关系
4. **生成 XML**：按照模板生成 draw.io XML
5. **写入文件**：保存为 `.drawio` 文件
6. **打开预览**：使用系统默认应用打开文件

### 导出模式

1. **生成 .drawio 文件**：先生成原生格式
2. **检测 draw.io CLI**：检查系统是否安装 draw.io 命令行工具
3. **执行导出**：使用 CLI 导出为指定格式
4. **清理文件**：删除中间 .drawio 文件（导出格式已包含 XML）
5. **返回结果**：告知用户导出文件路径

---

## 四、CLI 导出命令

### 检测命令

```bash
# macOS/Linux
which drawio

# Windows
where drawio
```

### 导出命令

```bash
# PNG 导出（嵌入 XML）
drawio -x -f png -e -b 10 -o output.drawio.png input.drawio

# SVG 导出（嵌入 XML）
drawio -x -f svg -e -o output.drawio.svg input.drawio

# PDF 导出（嵌入 XML）
drawio -x -f pdf -e -o output.drawio.pdf input.drawio
```

### 打开文件

```bash
# macOS
open filename.drawio

# Linux
xdg-open filename.drawio

# Windows
start filename.drawio
```

---

## 五、文件命名规范

| 场景 | 命名格式 | 示例 |
|-----|---------|------|
| 流程图 | `{name}-flow.drawio` | `login-flow.drawio` |
| 架构图 | `{name}-arch.drawio` | `system-arch.drawio` |
| 时序图 | `{name}-seq.drawio` | `api-call-seq.drawio` |
| ER图 | `{name}-er.drawio` | `user-db-er.drawio` |
| 导出 PNG | `{name}.drawio.png` | `login-flow.drawio.png` |
| 导出 SVG | `{name}.drawio.svg` | `system-arch.drawio.svg` |
| 导出 PDF | `{name}.drawio.pdf` | `api-doc.drawio.pdf` |

---

## 六、XML 注意事项

1. **ID 唯一性**：每个 `mxCell` 必须有唯一 `id`
2. **XML 转义**：属性值中的特殊字符必须转义（`&amp;` `&lt;` `&gt;` `&quot;`）
3. **注释禁止**：XML 注释中不能使用 `--`（双连字符）
4. **坐标系统**：使用网格对齐，建议 10px 为单位
5. **入口点**：使用 `entryX/entryY` 精确控制连线入口位置（0-1 之间）

---

## 七、使用示例

```text
# 生成流程图
请生成一个用户登录流程的 draw.io 图表，包含：输入账号密码 -> 验证 -> 成功/失败分支

# 生成架构图并导出 PNG
生成一个微服务架构图，包含网关、服务层、数据层，导出为 PNG 格式

# 生成时序图
生成一个订单创建的时序图，展示客户端、网关、订单服务、库存服务的交互
```
