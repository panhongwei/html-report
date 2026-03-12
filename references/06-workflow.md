# 06 · 工作流程与质量核查

---

## 第一阶段：规划（生成前）

### 步骤 1 · 主题拆解

将输入主题拆解为 8–12 个子维度，每个子维度对应一页：

```
维度分类参考：
- 背景/现状    → 执行摘要页
- 概念/定义    → 核心概念页
- 分类/类型    → 分类对比页
- 原理/机制    → 技术原理页
- 流程/步骤    → 实现流程页
- 案例/实验    → 案例分析页
- 风险/威胁    → 风险评估页
- 检测/评估    → 检测方法页
- 防御/应对    → 防御方案页
- 工具/框架    → 工具生态页
- 趋势/展望    → 未来展望页
```

### 步骤 2 · 确定主基调

根据主题类型选择主基调配色和辅助配色：

```
主题类型判断示例：
- AI 智能体代码编写分析 → 技术/架构分析
- 网络攻击原理分析 → 风险/威胁/攻击
- 安全防御方案 → 防御/安全/优化
- 市场数据统计 → 数据/统计/分析
- 未来趋势预测 → 趋势/展望/预测

主题：[报告主题]
主基调：[根据上表选择] → 方案 X 蓝/紫/青
辅助配色：方案 X 红（风险页）、方案 X 绿（防御页）、方案 X 粉（展望页）
背景类型：暗色报告（T1/T3 为主）或 亮色报告（T4 亮色/T5）
```

### 步骤 3 · 为每页分配三元组

按照 70-20-10 原则分配：

```
页码  主题       布局  配色        视觉模板   说明
P01   执行摘要   D     方案 1 蓝     T1        主基调
P02   智能体架构 A     方案 5 紫     T1        主基调
P03   核心能力分类 C     方案 6 青     T4        主基调（近似色）
P04   代码生成原理 B     方案 5 紫     T3        主基调
P05   典型应用场景 A     方案 1 蓝     T1        主基调
P06   风险评估   D     方案 2 红     T1        对比色（警示）
P07   检测评估   C     方案 6 青     T4        主基调
P08   防御优化   B     方案 4 绿     T1        辅助色（积极）
P09   工具框架   D     方案 5 紫     T3        主基调
P10   趋势展望   C     方案 7 粉     T5        对比色（未来感）
```

**规划约束检查：**
```
□ 主基调配色占比 60-80%？
□ 对比色页面位于逻辑转折点（如风险页、总结页）？
□ 同一布局连续出现 ≤ 2 次？
□ 同一视觉模板连续出现 ≤ 2 次？
□ 背景类型统一（全暗/全亮/章节分明）？
□ 覆盖 ≥ 3 种视觉模板（而非 4 种）？
```

---

## 第二阶段：逐页生成

每页生成时的完整参考顺序：

```
1. 读 01-canvas.md     → 复制四区 CSS 骨架
2. 读 03-layout.md     → 复制对应布局 CSS
3. 读 04-color-font.md → 复制对应配色 :root 变量
4. 读 02-design-system.md → 按视觉模板覆盖背景/卡片/页眉 CSS
5. 读 05-content.md    → 填写内容，选取合适的 SVG 图表
6. 自查 05-content.md 的 5 问表（每格）
```

---

## 第三阶段：渲染验证

### screenshot_batch.js（标准截图脚本）

```javascript
// 用法：node screenshot_batch.js <html 目录或单文件> [输出目录]
const puppeteer = require('/home/claude/.npm-global/lib/node_modules/@mermaid-js/mermaid-cli/node_modules/puppeteer');
const fs = require('fs'), path = require('path');
const CHROME = '/opt/google/chrome/chrome';

async function shot(browser, htmlFile, outFile) {
  const page = await browser.newPage();
  await page.setViewport({ width: 1017, height: 720, deviceScaleFactor: 1 });
  await page.goto('file://' + path.resolve(htmlFile), { waitUntil: 'networkidle0', timeout: 15000 });
  const ov = await page.evaluate(() => ({
    w: document.body.scrollWidth,
    h: document.body.scrollHeight
  }));
  if (ov.w > 1017 || ov.h > 720)
    console.warn(`  ⚠ 溢出：${ov.w}×${ov.h} → ${path.basename(htmlFile)}`);
  await page.screenshot({ path: outFile, clip: { x:0, y:0, width:1017, height:720 } });
  await page.close();
}

async function main() {
  const input  = process.argv[2];
  const outDir = process.argv[3] || path.dirname(path.resolve(input));
  const files  = fs.statSync(input).isDirectory()
    ? fs.readdirSync(input).filter(f => f.endsWith('.html')).sort().map(f => path.join(input, f))
    : [input];
  const browser = await puppeteer.launch({
    executablePath: CHROME,
    args: ['--no-sandbox','--disable-setuid-sandbox','--disable-dev-shm-usage','--disable-gpu']
  });
  for (const f of files) {
    const out = path.join(outDir, path.basename(f, '.html') + '.jpg');
    process.stdout.write(`  ${path.basename(f)} → `);
    await shot(browser, f, out);
    console.log('✓');
  }
  await browser.close();
  console.log(`\n完成 ${files.length} 页，检查有无 ⚠ 溢出提示`);
}
main().catch(e => { console.error(e); process.exit(1); });
```

### 运行方式

```bash
# 批量截图整个目录
node screenshot_batch.js /mnt/user-data/outputs/[报告名]/

# 单页截图
node screenshot_batch.js /mnt/user-data/outputs/[报告名]/p01.html
```

### 溢出修复参考

| 警告内容 | 原因 | 修复 |
|---------|------|------|
| `scrollHeight > 720` | `.ct` 有 padding-top/bottom | 删除 `.ct` 的 top/bottom padding |
| `scrollWidth > 1017` | 卡片 min-width 过宽 | 给卡片加 `overflow:hidden` |
| 高度 721–740px | 布局容器的 gap 过大 | gap 从 12px 改为 8px |
| 高度 > 750px | 字号超限导致文字撑高 | 降低字号，减少 line-height |

---

## 第四阶段：质量核查清单

### 🔴 必须通过（否则重做）

**结构规则：**
- [ ] Chrome 截图无溢出警告
- [ ] `html, body` 宽高 = `1017×720px`（不是 1280×720）
- [ ] 四区高度和 = 720px（72+580+48+20，T3 模板：32+40+608+40=720）
- [ ] `.ct` 仅有左右 padding，无 top/bottom

**反偷懒规则：**
- [ ] 网格 `gap` ≤ 8px
- [ ] 卡片 `padding` 上下 ≤ 8px
- [ ] `line-height` ≤ 1.45
- [ ] 每个卡片有 3 层内容（标题 + 正文 + mini 卡/SVG/数据块）
- [ ] 每个卡片正文 ≥ 40 字且含 ≥ 2 个具体数字
- [ ] 视觉检查：无大片空白底色可见（背景色面积 < 内容面积）

**设计规则：**
- [ ] 主基调配色占比 60-80%
- [ ] 对比色页面位于逻辑转折点（风险页、总结页等）
- [ ] 背景类型统一（全暗/全亮/章节分明）
- [ ] 覆盖 ≥ 3 种视觉模板
- [ ] 没有连续 3 页使用同一模板

### 🟢 一致性检查（新增）

- [ ] 整份报告有明确的主基调（在规划阶段已确定）
- [ ] 主基调配色占比 60-80%
- [ ] 对比色页面位于逻辑转折点（如风险页、总结页）
- [ ] 背景色没有无规律的明暗跳跃
- [ ] 页眉结构有统一的设计语言（字体/间距/元素）
- [ ] 没有为了多样性而强行变化视觉模板

### 🟡 应当通过（否则优化）

- [ ] 每页至少 1 个 SVG，有坐标轴和数值
- [ ] 布局 B 每行为左文右图双栏
- [ ] 10 页使用全部 4 种布局（A/B/C/D）
- [ ] 大数字 KPI 每页 ≤ 4 个
- [ ] 至少 1 页有页面大标题（≥32px）
- [ ] 同款卡片组合出现 ≤ 4 次

### 🟢 加分项

- [ ] SVG 使用 `<linearGradient>` 增加层次感
- [ ] T1 模板使用了 Syne 展示字体
- [ ] T2 模板左栏标题字号 ≥ 44px
- [ ] 关键数字用 `<em>` 等宽字体主色高亮
- [ ] 页脚进度条宽度与页码比例一致
- [ ] 至少 1 页有 SVG 时间轴或甘特图

---

## 输出规范

```
1. 文件位置：/mnt/user-data/outputs/[报告名]/p01.html … p[N].html
2. 截图验证：node screenshot_batch.js，确认无 ⚠ 溢出
3. 展示文件：调用 present_files 展示所有 HTML
4. 说明摘要：页数 + 使用的视觉模板列表 + 布局变体数 + 溢出检测结果
5. 一致性报告：主基调配色占比 + 对比色页面位置 + 背景类型
```
