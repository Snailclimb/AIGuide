# Draw.io 导出与文件规范

本文件定义 drawio-chart 的导出命令、命名约定和交付规则。只有在需要落文件、导出或打开图表时再读取。

## 标准流程

1. 先生成 `.drawio`
2. 检测本机是否存在 `drawio` CLI
3. 若用户要求导出，则执行对应导出命令
4. 返回 `.drawio` 路径和导出文件路径
5. 若导出失败，不删除 `.drawio`

## 检测命令

```bash
# macOS/Linux
which drawio

# Windows
where drawio
```

## 导出命令

```bash
# PNG 导出（嵌入 XML）
drawio -x -f png -e -b 10 -o output.drawio.png input.drawio

# SVG 导出（嵌入 XML）
drawio -x -f svg -e -o output.drawio.svg input.drawio

# PDF 导出（嵌入 XML）
drawio -x -f pdf -e -o output.drawio.pdf input.drawio
```

## 打开文件

```bash
# macOS
open filename.drawio

# Linux
xdg-open filename.drawio

# Windows
start filename.drawio
```

## 文件命名规范

| 场景 | 命名格式 | 示例 |
|-----|---------|------|
| 流程图 | `{name}-flow.drawio` | `login-flow.drawio` |
| 架构图 | `{name}-arch.drawio` | `system-arch.drawio` |
| 时序图 | `{name}-seq.drawio` | `api-call-seq.drawio` |
| ER 图 | `{name}-er.drawio` | `user-db-er.drawio` |
| 状态图 | `{name}-state.drawio` | `order-state.drawio` |
| 思维导图 | `{name}-mind.drawio` | `rag-mind.drawio` |
| 导出 PNG | `{name}.drawio.png` | `login-flow.drawio.png` |
| 导出 SVG | `{name}.drawio.svg` | `system-arch.drawio.svg` |
| 导出 PDF | `{name}.drawio.pdf` | `api-doc.drawio.pdf` |

## 交付规则

- 若用户只说“生成图”，默认交付 `.drawio`
- 若用户明确说“导出 PNG/SVG/PDF”，同时交付 `.drawio` 和导出文件
- 若本机没有 `drawio` CLI，要明确说明未自动导出，并把建议命令返回给用户
- 如果用户要求“只保留导出产物”，删除 `.drawio` 前先确认导出成功
