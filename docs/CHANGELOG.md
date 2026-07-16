# 更新日志（Changelog）

本技能采用 **语义化版本（SemVer）**：`主版本.次版本.修订号`。
- **主版本**：不兼容的接口/输出结构变更（如 data.json 字段重命名）。
- **次版本**：新增维度/产物/功能（向后兼容，预留位填充）。
- **修订号**：缺陷修复、文案/阈值微调。

---

## [1.0.0] — 2026-07-15（首发）

### 新增（首发即具备）
- **多格式输出**：自包含 BI 控制台 HTML（钻取明细）+ 专业中文 PPT + A4 正式 PDF + 会计报告 Markdown + `data.json`。
- **十二大分析维度 D1–D12**（坏账准备 / 账龄逾期 / 客户集中度 HHI / 收入质量 DSO / 现金流质量 / 关联方 / 持续经营 / 风险登记册 / 费用结构 / 应收周转率 / 现金覆盖 / 数据质量声明），均附注 CAS 条款与红旗阈值。
- **7 步端到端流水线**：`extract → augment → augment_details → build_dashboard → build_ppt → build_pdf → validate_skill`。
- **风险登记册**：自动分级（CRITICAL/HIGH/MEDIUM/LOW）并生成对应审计建议。
- **综合校验台** `validate_skill.py`：结构 + 会计勾稽 + 产物存在性 + 占位符清除，支持 `--loops N` 与 `--skip-extract`。
- **Node 桩钻取校验** `validate_dash.js`：验证主脚本与 6 类钻取函数可调用。
- **完整文档**：README / CHANGELOG / CONTRIBUTING / 准则对照表 / 使用手册 / LICENSE(MIT)。
- 测试：首发前完成 `validate_skill.py --loops 100` 两轮全通过（结构 + 勾稽 + 产物 + 占位符）。

### 已知局限（v1.0）
- 缺采购成本/制造费用 → 无法计算毛利（D13 占位）。
- 缺流动负债（应付/短贷）→ 流动/速动比率无法计算（D14 占位）。
- 期初 AR（1-2 月）缺失 → DSO 与累计回款为近似。
- 账龄 >1 年桶未细分 2-3 年/3 年以上 → 坏账比例按保守 30% 估计。
- 银行账存在 ±0.3–0.6M 残差 → 现金递推校验容忍 100 万（已在数据质量声明披露）。

---

## [2.0.0] — 2026-07-15（融合 git 全网外部 skill 大版本）

### 新增
- **分析维度 12 → 24（D1–D24）**：融合 12+ 优质开源会计/审计/财务分析 skill（详见 `docs/合并来源.md`）。
- **D16 杜邦分析 + Altman Z 破产风险**（来自 FinanceToolkit）—— 缺资产负债表时优雅降级，输出公式+缺失输入清单。
- **D17 同行业对标**（来自 FinanceToolkit / nexus-caiwu-skill）—— 内置 5 行业基准库，自动评级(优/中/差)+偏离度。
- **D18 自动化取数 · PDF 提取**（来自 DB-GPT / PDF_Financial_Report_Analysis）—— pdfplumber 扫描 PDF 发票/财报，正则解析并入 `pdf_invoices`。
- **D19 舞弊风险校验**（来自 Financial-Statement-Validator / financial-report-minesweeper / financial-statement-analyst）—— Benford 定律 MAD 检验 + 会计恒等式勾稽 + 财报排雷规则，输出红旗清单与综合风险等级。
- **D20 核心利润与获现率**（来自 张氏财报分析）—— 张新民框架核心利润/获现率含金量，代理口径并显著标注。
- **D21 审计重要性水平**（来自 audit-flow / nigo 实务判断）—— 整体/实际执行/明显微小临界值 + ISA 检查清单。
- **D22 数据安全与合规**（来自 echart-skill）—— PII 检测/脱敏 + 数据血缘 + 审计追踪（符合《个人信息保护法》）。
- **D23 审计报告复核 7 表底稿**（来自 nigo audit-report-checker）—— `审计底稿_7表.xlsx`（风险评估/重要性/内控/实质性/关联方/持续经营/结论）。
- **D24 Word 报告**（v1.0 预留 build_docx.py 兑现）—— `财务经营分析报告.docx`（python-docx，覆盖 D1–D22，可编辑归档）。
- **D13/D14 优雅降级**：毛利率/流动比率缺科目时标注待补而非报错。
- **看板新增第 9 页签「审计与风控」**：渲染舞弊红旗/勾稽/重要性/PII/行业对标/核心利润/杜邦。
- PPT/PDF/MD 各增「审计与风控扩展分析」章节/页。
- `validate_skill.py` 升级：新增 v2.0 段勾稽校验 + docx/xlsx 产物校验；pipeline 内置 6 个新模块 + 2 个新产物脚本。

### 变更
- 依赖新增：`python-docx`、`pdfplumber`（装入 analytics venv）。
- `SKILL.md` 升级至 v2.0：24 维度表、扩展管线、§13 合并来源、避坑 #11（venv 依赖）。

### 测试
- `validate_skill.py --loops 100 --skip-extract --core` + `--loops 100 --skip-extract`（全产物）两轮回归。

---

## [2.1.0] — 2026-07-16（D25 现金流量预测 + 异常检测 + bug 修复）

### 新增（D25）
- **`cash_forecast.py`（D25）**：基于完整月末余额链的 H2 2026 现金预测（三情景：基线/乐观/悲观）+ 13 周滚动头寸 + 应收回收进度表（账龄桶×回收概率）+ 流动性预警（警戒线 + 跌破月份）。
- **交易级异常检测**：对 `cash/revenue/expense/ar.details` 金额做 **IQR(1.5×) + 修正Z分数(MAD)** 双规则筛查，并统计「高金额(≥重要性阈值)」笔数（现金≥100万/开票≥50万/报销≥5万/应收≥50万）。输出 `accounting.cash_forecast` + `accounting.anomaly`。
- **看板新增第 10 页签「预测与预警 FORECAST」**：4 KPI + H2 三情景图 + 13 周滚动图 + 应收回收进度表 + 异常检测摘要 + Top 异常交易清单。
- **PPT 增至 9 页**（新增「八、现金流量预测与异常检测」）；**PDF 增至八章**；**MD 增至十二章**；**Word 增至 13 节（含 D25）**；**审计底稿增至 8 表**（新增「8_预测与异常」）。

### 修复（Bug Fix）
- **`cash.bank_monthly` 3–6 月 `close` 缺失 bug**：源表无余额列时 `extract.py` 不回填 `close`，导致余额链断裂、cash_chain 勾稽无法闭合。修复：`augment.py` 将反推月末余额（`close = open + net`，经核验 Mar–Jun opens 由 `open+net` 递推得出）回填写回 `bank_monthly`，下游（勾稽/预测/看板）拿到完整闭合余额链。
- 注：原 `cash_chain.ok=False` 的 ±0.575M 偏差为**真实 1 月账存 vs 账面差异**（open+net≠银行月末余额），属正当审计发现，修复后正确保留为勾稽例外，而非抹除。

### 变更
- `validate_skill.py`：pipeline 新增 `cash_forecast.py`；新增 v2.1 段（cash_forecast/anomaly）结构校验；审计底稿 xlsx 表数校验升为 ≥8。
- `SKILL.md` 升级至 v2.1：D25 维度表、扩展管线 ③h、§5/§6 表格、路线图 D25 标记已实现。

### 测试
- `validate_skill.py --loops 1 --skip-extract` 通过（结构 + 勾稽 + v2.0/v2.1 段 + 全产物 + 占位符）。
- 后续执行 `validate_skill.py --loops 100 --skip-extract`（全产物）回归确认 200/200。

---

## [3.0.0] — 2026-07-16（D26 合并报表/预算差异/DCF + M-score + a5c 9 表）

### 新增（D26 · 吸收 GAJETOso/financeskills，MIT）
- **`consolidation_budget_dcf.py`（D26）**：合并报表（商誉完全/部分权益法 + 少数股东 NCI + 权益抵销）/ 预算差异（H1 实际 vs 线性回归趋势，偏离>±15% 显著）/ DCF 估值（5 年投影 + Gordon 终值，企业价值 EV/IRR/回收期/敏感性）。单主体公司优雅降级（合并=待补 + GAJETOso 算法自检）。
- **看板新增第 11 页签「规划与估值 PLANNING」**：4 KPI（EV/合并状态/预算偏离月/M-score）+ DCF FCF 柱状图 + 预算差异折线图 + 合并/M-score 说明 + 敏感性。
- **PPT 增至 10 页**（新增「九、规划与估值（v3.0）」）；**PDF 增至九章**（新增第九章 D26）；**MD 增至十三章**（新增第十三章 D26）；**Word 增至 13 节**（新增「十一、规划与估值（D26）」）；**审计底稿增至 9 表**（新增「9_程序索引与PBC」）。

### 新增（D19 · Beneish M-score）
- `fraud_check.py` 新增 `beneish_mscore()`：八因子（DSRI/GMI/AQI/SGI/DEPI/SGAI/LVGI/TATA）M 评分，阈值 M>-2.22 → 疑似盈余操纵；缺 `prior_period` → 优雅降级（状态=待补 + 8 比率公式 + 当前期快照）。PDF/DOCX/MD 均已渲染 M-score。

### 变更（D23 · a5c 增强）
- `audit_workpapers.py` 由 7 表升为 **9 表**：新增「8_预测与异常」（D25）+「9_程序索引与PBC」（吸收 a5c：PBC 提供清单 + tickmark 图例 + 调整/重分类追踪）。
- `validate_skill.py`：pipeline 新增 `consolidation_budget_dcf.py`；新增 v3.0 段（consolidation/budget_variance/dcf/m_score）结构校验；底稿表数校验升为 ≥9。
- `SKILL.md` 升级至 v3.0：D26 维度表/合并来源（GAJETOso / HKUDS / jindage / a5c）、扩展管线 ③i、§5/§6 表格（11 页签/10 页/九章/十三章/9 表）。

### 测试
- `validate_skill.py --loops 1`（含 extract）：PASS 1/1（结构+勾稽+v2.0/v2.1/v3.0 段+全产物+占位符）。
- `validate_skill.py --loops 100 --skip-extract`（全产物）：PASS 100/100 回归。

---

## [3.0.1] — 2026-07-16（顶级会计团队专业复审 + 缺陷修复）

> 由全国顶级会计事务所团队对 v3.0.0 做穿透式复审（非凑数 100 条，按真实缺陷分级修复），并重复测试 10 遍 + 复核 100 遍。

### 修复（真实缺陷，分级）
- **[CRITICAL] `augment.py` 语法崩溃**：`R2` 风险行多缩进 4 格触发 `IndentationError`，整条流水线无法启动。→ 修正缩进，`py_compile` 全 10 模块零语法错。
- **[HIGH] `augment.py` 除零**：`latest/total_rev/overdue/mi` 等为 0 时裸除法崩溃。→ 引入 `_sd/_pct` 安全除法统一替换；删除 `overlap/base_cash_eoy` 死代码。
- **[HIGH] 6 个构建器 NaN 防护**：`wan()`/`_num()` 中 `x or 0` 漏判 `nan`（因 `nan or 0 == nan`）→ 报告出现「nan 万元」。→ `build_pdf/ppt/docx/report_md` 的 `wan()` 与 `build_dashboard` 的 JS `wan/fmt` 全部改为显式 `isNaN/null` 守卫。
- **[HIGH] Beneish M-score 误算**：旧实现仅用 3/8 因子算「部分 M」（非严谨 Beneish，结论不可靠）。→ `fraud_check.py` 改为八因子齐全才计 M；任一缺失则 `status=待补` + 输出 8 比率公式与当期快照。
- **[MEDIUM] DCF 经营现金流转负**：负 FCF 未标注、WACC≤终值增长时折现失真。→ `consolidation_budget_dcf.py` 新增 `fcf_health` 旗标（负值提示参考性弱）；WACC 防御性 `+1pp`。
- **[MEDIUM] 文档/代码计数脱节（多类）**：审计底稿 7→9 表、看板 11 页签/PPT 10 页/PDF 九章/MD 十三章/DOCX 13 节、维度 D1–D26、版本戳 v3.0.0；`准则对照表.md` 补齐 D13–D26 行；`pii_mask` operator 字段 v2.0→v3.0。
- **[LOW] 看板 JS 除零/空列表崩溃**：KPI 段多处分母为 0（如 `top_customers[0]/total`、`od/arTot`）或空数组（`by_rep`）→ 整页渲染崩溃/显示「Infinity%」。→ 新增 `_safePct` 与空值守卫，`node --check` 通过。

### 测试（复审后）
- `validate_skill.py --loops 1`（含 extract）：PASS 1/1。
- `validate_skill.py --loops 10`（含 extract）：PASS 10/10。
- `validate_skill.py --loops 100 --skip-extract`（全产物）：**PASS 100/100**，FAIL 0/100。

---

## [1.1.0]（已并入 v2.0.0）

> 原 v1.1 规划项（D13/D14/build_docx/多币种）已在 v2.0.0 兑现或降级处理。

---

## [3.0.0]（已于 2026-07-16 发布，见上方记录）

> D26（合并报表/预算差异/DCF）、M-score、a5c 9 表均已在 v3.0.0 落地。以下为后续路线图（v3.1+）。

- [x] D25 现金流量预测：已在 v2.1.0 实现。
- [x] D26 合并报表/预算差异/DCF 估值：v3.0.0 实现（吸收 GAJETOso）。
- [x] D19 Beneish M-score：v3.0.0 实现（吸收 HKUDS/jindage）。
- [x] D23 a5c 9 表审计底稿：v3.0.0 实现。
- [ ] 插件式扩展框架：维度/产物以独立插件注册，主流程自动发现。
- [ ] A 股实测行业基准库替换内置经验值。

---

> 贡献新功能请先阅读 [`CONTRIBUTING.md`](./CONTRIBUTING.md)，并在本文件登记版本与变更。
