# 03 · 布局系统

> 4 种布局对应不同的信息结构。每页选一种，同一份报告 10 页需使用 ≥ 4 种不同布局。

---

## 通用布局基础 CSS

```css
/* 卡片通用（各模板会覆盖视觉风格，结构保持一致） */
.card {
  overflow: hidden;
  height: 100%;
  display: flex;
  flex-direction: column;
  gap: 5px;           /* 卡片内间距上限 5px */
  padding: 8px 10px;  /* 上下 ≤8px，左右 ≤12px */
}
.ch {                 /* 卡片标题 */
  font-size: 12.5px; font-weight: 700;
  flex-shrink: 0;
}
.cb {                 /* 卡片正文 */
  font-size: 11.5px; line-height: 1.45;
  overflow: hidden; flex: 1;
}
.cb b, .cb strong { /* 关键词高亮 */ }
.cb em { font-style: normal; font-family: monospace; font-size: 11px; } /* 公式/代码 */

/* mini 数据卡片行 */
.mini-row { display: flex; gap: 5px; flex-shrink: 0; margin-top: 4px; }
.mini {
  flex: 1; padding: 4px 7px; border-radius: 3px;
  display: flex; flex-direction: column;
}
.mh { font-size: 10.5px; font-weight: 600; }
.ml { font-size: 9.5px; margin-top: 1px; }
```

---

## 布局 A · 左大图 + 右三行

**适用：** 技术原理、流程图解、攻击链

```
┌──────────────┬──────────────────────────┐
│              │  ① 文字卡片              │
│  左侧大图    ├──────────────────────────┤
│  366px       │  ② 文字卡片              │
│              ├──────────────────────────┤
│              │  ③ 文字卡片              │
└──────────────┴──────────────────────────┘
```

### 空间计算

```
左图宽   = 366px
gap      = 12px
右侧宽   = 967 - 366 - 12 = 589px
右侧行高 = (580 - 12×2 - padding-top 12) ÷ 3 ≈ 181px
正文可用 ≈ 181 - 标题 24px - padding 16px = 141px → 约 70–100 字
```

### CSS

```css
.layout-a {
  display: grid;
  grid-template-columns: 366px 1fr;
  gap: 12px;
  height: 100%;
  padding-top: 12px;
}
.la-right {
  display: grid;
  grid-template-rows: repeat(3, 1fr);
  gap: 12px;
}
```

### HTML 结构

```html
<div class="ct">
  <div class="layout-a">

    <!-- 左侧大图 366px -->
    <div class="card">
      <div class="ch">核心可视化标题</div>
      <svg viewBox="0 0 340 490" style="width:100%;flex:1;">
        <!-- 必须有真实坐标轴和数据标签 -->
      </svg>
    </div>

    <!-- 右侧三行 -->
    <div class="la-right">
      <div class="card">
        <div class="ch">① 子维度</div>
        <div class="cb">
          <!-- 70-100 字，含 ≥2 个具体数字 -->
          <div class="mini-row">
            <div class="mini"><div class="mh">指标A</div><div class="ml">数值</div></div>
            <div class="mini"><div class="mh">指标B</div><div class="ml">数值</div></div>
            <div class="mini"><div class="mh">指标C</div><div class="ml">数值</div></div>
          </div>
        </div>
      </div>
      <div class="card"><div class="ch">② 子维度</div><div class="cb"><!-- 同上 --></div></div>
      <div class="card"><div class="ch">③ 子维度</div><div class="cb"><!-- 同上 --></div></div>
    </div>

  </div>
</div>
```

---

## 布局 B · 上下四行（每行双栏）

**适用：** 步骤流程、方法对比、四阶段分析

```
┌───────────────────────────────────┐
│  行1：左文字(58%)  右图表(42%)    │
├───────────────────────────────────┤
│  行2：左文字       右图表          │
├───────────────────────────────────┤
│  行3：左文字       右图表          │
├───────────────────────────────────┤
│  行4：左文字       右图表          │
└───────────────────────────────────┘
```

### 空间计算

```
padding-top  = 10px
每行高       = (580 - 10×3 - 10) ÷ 4 = 130px
左文字宽     = 967 × 58% ≈ 561px
右图表宽     = 967 × 42% ≈ 406px
正文可用     ≈ 130 - 标题 22px - padding 16px = 92px → 约 45–55 字
```

> ⚠ 每行必须是左文 + 右图双栏，**禁止纯文字行**

### CSS

```css
.layout-b {
  display: grid;
  grid-template-rows: repeat(4, 1fr);
  gap: 10px;
  height: 100%;
  padding-top: 10px;
}
.lb-row {
  display: grid;
  grid-template-columns: 58% 42%;
  gap: 10px;
}
```

### HTML 结构

```html
<div class="ct">
  <div class="layout-b">
    <div class="lb-row">
      <div class="card">
        <div class="ch">步骤①</div>
        <div class="cb"><!-- 45-55 字，含数字 --></div>
      </div>
      <div class="card" style="padding:6px;">
        <svg viewBox="0 0 380 100" style="width:100%;height:100%;"><!-- 横向条形图 --></svg>
      </div>
    </div>
    <!-- 重复 × 3 -->
  </div>
</div>
```

---

## 布局 C · 3×2 六格网格

**适用：** 分类对比、六维度分析、案例矩阵

```
┌──────────┬──────────┬──────────┐
│  ① 格    │  ② 格    │  ③ 格    │
├──────────┼──────────┼──────────┤
│  ④ 格    │  ⑤ 格    │  ⑥ 格    │
└──────────┴──────────┴──────────┘
```

### 空间计算

```
padding-top = 10px
每格高      = (580 - 8 - 10) ÷ 2 = 281px
每格宽      = (967 - 8×2) ÷ 3 ≈ 317px
正文可用    ≈ 281 - 标题 22px - mini 40px - padding 16px = 203px → 约 70–90 字
```

### CSS

```css
.layout-c {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(2, 1fr);
  gap: 8px;
  height: 100%;
  padding-top: 10px;
}
```

### HTML 结构

```html
<div class="ct">
  <div class="layout-c">
    <div class="card">
      <div class="ch">① 维度标题</div>
      <div class="cb">
        <!-- 70-90 字，含 ≥2 数字 -->
        <svg viewBox="0 0 290 55" style="width:100%;height:55px;flex-shrink:0;margin-top:5px;">
          <!-- 内嵌小图表 -->
        </svg>
      </div>
      <div class="mini-row"><!-- 2-3 个 mini 数据卡 --></div>
    </div>
    <!-- 重复 × 5 -->
  </div>
</div>
```

---

## 布局 D · 2×2 四格 + 右侧大图

**适用：** 执行摘要、综合展示、矩阵分析

```
┌──────────┬──────────┬──────────────┐
│  ① 格    │  ② 格    │              │
├──────────┼──────────┤  右侧大图    │
│  ③ 格    │  ④ 格    │  286px       │
└──────────┴──────────┴──────────────┘
```

### 空间计算

```
左侧总宽    = 967 - 8×2 - 286 = 665px
每小格宽    = (665 - 8) ÷ 2 ≈ 328px
每小格高    = (580 - 8 - 10) ÷ 2 = 281px
右侧图高    = 580 - 10 = 570px（跨两行）
正文可用    ≈ 281 - 标题 22px - padding 16px = 243px → 约 65–90 字
```

### CSS

```css
.layout-d {
  display: grid;
  grid-template-columns: 1fr 1fr 286px;
  grid-template-rows: repeat(2, 1fr);
  gap: 8px;
  height: 100%;
  padding-top: 10px;
}
.ld-right {
  grid-column: 3;
  grid-row: 1 / 3;
}
```

### HTML 结构

```html
<div class="ct">
  <div class="layout-d">
    <div class="card"><div class="ch">① 格</div><div class="cb"><!-- 65-90 字 --></div></div>
    <div class="card"><div class="ch">② 格</div><div class="cb"><!-- 同上 --></div></div>
    <div class="card"><div class="ch">③ 格</div><div class="cb"><!-- 同上 --></div></div>
    <div class="card"><div class="ch">④ 格</div><div class="cb"><!-- 同上 --></div></div>

    <div class="card ld-right" style="padding:10px;">
      <div class="ch">核心图表</div>
      <svg viewBox="0 0 260 520" style="width:100%;flex:1;">
        <!-- 大型可视化 -->
      </svg>
    </div>
  </div>
</div>
```

---

## 布局选择指南

| 内容特征 | 推荐布局 | 原因 |
|---------|---------|------|
| 需要展示大型流程图/架构图 | A | 左侧有足够空间 |
| 4 个步骤/阶段，每步有图 | B | 行结构天然对应步骤 |
| 6 个平行维度横向对比 | C | 等权网格无主次 |
| 执行摘要 / 综合仪表 | D | 右侧图支撑全局视野 |
| T2 编辑分割模板 | — | 左右分割，不用上述布局，自定义列 |
| T3 终端代码模板 | — | 左侧边栏+右主区，不用上述布局 |

---

## 布局多样性检查

```
□ 10 页报告中布局 A/B/C/D 均至少出现 1 次？
□ 相同布局未连续出现 3 次以上？
□ T2/T3 特殊模板未与标准布局混用（它们有自己的列结构）？
```
