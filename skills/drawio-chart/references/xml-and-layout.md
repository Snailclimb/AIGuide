# Draw.io XML 与布局规范

本文件包含 XML 结构规则、文本标签约束、节点/连线模板以及不同图表类型的布局建议。只有在需要生成或修改 `.drawio` XML 时再读取。

## 文本标签规则

- `mxCell` 的 `value` 中禁止使用 HTML 标签，例如 `&lt;b&gt;`、`&lt;/b&gt;`、`&lt;br&gt;`、`<b>`、`<br>`。
- 需要换行时使用 XML 换行实体 `&#xa;`，不要使用 `<br>` 或 `&lt;br&gt;`。
- 需要强调整段文字时使用样式字段（如 `fontStyle=1`）或拆成独立文本节点，不要在 `value` 中嵌入 HTML。
- 文本节点和图形节点默认使用纯文本渲染，样式中优先设置 `html=0`；除非用户明确要求并确认渲染链支持 HTML，否则不要使用 `html=1`。
- 生成或修改 `.drawio` 后，必须检查文件中是否残留 HTML 标签，重点搜索 `&lt;`、`<b>`、`<br>`。

## 连线标签规则

- 短连接线不要放长标签；如果两个节点距离较近，连线 `value` 应留空，或只放“是 / 否 / 成功 / 失败”这类 1-2 个词。
- 分类说明、原因说明、动作说明优先写进目标节点或旁注节点，不要压在连接线上。
- 连线标签不得覆盖箭头主体。生成或修改流程图后，要重点检查短横线、短竖线、菱形节点左右出口处的标签。

## 基础模板

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

## 标题样式模板

图表主标题必须使用 `fontSize=20`。除主标题外，节点、容器标题、分组标签、连线标签等常规文字统一使用 `fontSize=15`。

```xml
<mxCell id="title" value="图表标题" style="text;html=0;align=center;verticalAlign=middle;resizable=0;points=[];autosize=1;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=20;fontStyle=1;fontColor=#2D3748;" vertex="1" parent="1">
  <mxGeometry x="260" y="20" width="300" height="40" as="geometry"/>
</mxCell>
```

## 节点样式模板

以下模板中的 `{语义类别}` 对应配色表中的行，直接替换 `fillColor` / `fontColor` 即可。

### 通用节点模板

```xml
<mxCell id="node-id" value="节点名称" style="rounded=1;whiteSpace=wrap;fillColor={类别填充色};strokeColor=none;fontColor={类别文字色};fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;shadow=1;" vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="140" height="60" as="geometry"/>
</mxCell>
```

### 常见节点模板

```xml
<mxCell id="gateway-id" value="API 网关" style="rounded=1;whiteSpace=wrap;fillColor=#005D7B;strokeColor=none;fontColor=#FFFFFF;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;shadow=1;" vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="140" height="60" as="geometry"/>
</mxCell>

<mxCell id="service-id" value="用户服务" style="rounded=1;whiteSpace=wrap;fillColor=#E99151;strokeColor=none;fontColor=#FFFFFF;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;shadow=1;" vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="140" height="60" as="geometry"/>
</mxCell>

<mxCell id="infra-id" value="认证服务" style="rounded=1;whiteSpace=wrap;fillColor=#7C3AED;strokeColor=none;fontColor=#FFFFFF;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;shadow=1;" vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="140" height="60" as="geometry"/>
</mxCell>

<mxCell id="db-id" value="MySQL 主库" style="shape=cylinder3;whiteSpace=wrap;fillColor=#E99151;strokeColor=none;fontColor=#FFFFFF;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;shadow=1;" vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="120" height="80" as="geometry"/>
</mxCell>

<mxCell id="decision-id" value="条件?" style="rhombus;whiteSpace=wrap;fillColor=#005D7B;strokeColor=none;fontColor=#FFFFFF;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;shadow=1;" vertex="1" parent="1">
  <mxGeometry x="100" y="200" width="120" height="80" as="geometry"/>
</mxCell>

<mxCell id="group-id" value="服务集群" style="swimlane;whiteSpace=wrap;fillColor=none;strokeColor=#005D7B;dashed=1;strokeWidth=2;fontColor=#2D3748;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;" vertex="1" parent="1">
  <mxGeometry x="50" y="50" width="400" height="250" as="geometry"/>
</mxCell>
```

## 连线样式模板

```xml
<mxCell id="edge-id" value="HTTP" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;strokeWidth=2;strokeColor=#94A3B8;labelBackgroundColor=#F8FAFC;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;fontColor=#64748B;" edge="1" source="source-id" target="target-id" parent="1">
  <mxGeometry relative="1" as="geometry"/>
</mxCell>
```

```xml
<mxCell id="edge-id" value="" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;strokeWidth=2;strokeColor=#94A3B8;" edge="1" source="source-id" target="target-id" parent="1">
  <mxGeometry relative="1" as="geometry"/>
</mxCell>
```

```xml
<mxCell id="edge-id" value="异步消息" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;dashed=1;strokeWidth=2;strokeColor=#94A3B8;labelBackgroundColor=#F8FAFC;fontFamily=system-ui, -apple-system, PingFang SC, Microsoft YaHei, sans-serif;fontSize=15;fontColor=#64748B;" edge="1" source="source-id" target="target-id" parent="1">
  <mxGeometry relative="1" as="geometry"/>
</mxCell>
```

## 图表类型布局建议

### 流程图

- 主干流程尽量保持单向阅读
- 分支判断使用菱形
- 决策出口只保留短标签
- 避免大量连线交叉

### 架构图

- 先画层，再画层内节点
- 相同职责的节点放进同一容器
- 数据流和控制流不要混成一类箭头
- 外部依赖和内部模块视觉上要拉开层次

### 时序图

- 参与者从左到右排布
- 消息箭头按时间顺序自上而下
- 避免无意义的横向拉宽
- 激活条只在确有必要时添加

### ER 图

- 实体名清晰、字段简短
- 主键、外键关系明确
- 不要把所有字段都塞进一张特别大的 ER 图
- 复杂系统优先拆分子域

### 状态机图

- 状态是节点，事件是边
- 避免状态名和边标签都写成长句
- 开始和结束状态要明显

### 思维导图

- 从中心主题向外发散
- 第一层分支控制在 4-8 个
- 同层级节点保持视觉一致

## XML 注意事项

1. 每个 `mxCell` 必须有唯一 `id`
2. 属性值中的特殊字符必须转义：`&amp;` `&lt;` `&gt;` `&quot;`
3. XML 注释中不能使用 `--`
4. 使用网格对齐，建议以 10px 为单位
5. 需要精细控制连线时，用 `entryX/entryY` 指定入口位置
