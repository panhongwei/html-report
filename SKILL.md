---
name: html-report-generator
description: 将任意输入自动分解成 5-15 个独立 HTML 报告页面，每页严格 1017×720px（对齐 PPT 画布 10.59"×7.499" @96dpi），深度拆解 3-6 个子维度，每维度精炼 60-100 字 + 支撑图表。当用户说"生成报告"、"分析内容做成页面"、"做成HTML"、"内容可视化"时立即使用，无需确认直接生成。
---

# HTML 报告生成器 · 主索引

本 skill 拆分为 6 个专项文件，**生成前必须按顺序读取所有文件**。

---

## 📂 文件索引

| 文件 | 职责 | 生成时机 |
|------|------|---------|
| `01-canvas.md` | 画布尺寸、四区结构、溢出规则 | 每页开始前 |
| `02-design-system.md` | 5种视觉模板、禁止清单、多样性检查 | 规划阶段 |
| `03-layout.md` | 4种布局精确 CSS + 空间容量计算 | 每页选布局时 |
| `04-color-font.md` | 7套配色、字体规则、语义色系统 | 每页设定样式时 |
| `05-content.md` | 反偷懒约束、内容密度、SVG图表库 | 写内容时 |
| `06-workflow.md` | 主题拆解规划、渲染验证、质量核查 | 开始和结束时 |

---

## ⚡ 三条不可逾越的铁律

> 违反任意一条 → 该页推翻重做，无例外。

### 铁律 1：画布锁死 1017×720px

```css
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
html, body {
  width: 1017px;  height: 720px;
  min-width: 1017px; max-width: 1017px;
  min-height: 720px; max-height: 720px;
  overflow: hidden;
}
```

### 铁律 2：四区高度必须精确求和 720px

```
Header   72px  ←  页眉
Content 580px  ←  主内容（仅左右 padding，上下为 0）
Summary  48px  ←  摘要栏
Footer   20px  ←  页脚
─────────────
总计    720px  ✓
```

### 铁律 3：不得使用 LibreOffice 渲染

必须使用 Chrome/Puppeteer 截图（见 `06-workflow.md`）。

---

## 🚀 快速启动流程

```
1. 读 02-design-system.md → 为整份报告规划视觉模板分配
2. 读 06-workflow.md      → 拆解主题，规划每页内容
3. 每页生成时：
   ├─ 读 01-canvas.md     → 确定四区 CSS
   ├─ 读 03-layout.md     → 选布局，计算空间
   ├─ 读 04-color-font.md → 选配色方案
   └─ 读 05-content.md    → 填内容，反偷懒自查
4. 全部生成后：
   └─ 读 06-workflow.md   → 运行截图验证，对照质量清单
```
