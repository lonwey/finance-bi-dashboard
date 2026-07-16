---
name: finance-bi-dashboard
description: 中国会计准则（CAS）合规的会计级财务经营分析技能。把混杂的财务/业务资料库（Excel/PDF 开票记录、对账、应收AR、员工报销、银行账、每日订单报告）一键转成多格式（自包含 BI 控制台 HTML + 专业 PPT + 正式 PDF + 会计报告 MD）、带全面板钻取明细、附风险登记册与数据质量声明的交付物。适用于"经营看板""数据大屏""审计底稿""发给别人不报错的单文件报告"等场景。调用时给出资料目录即可复用。
color: teal
emoji: 📊
agent_created: true
version: 3.0.2
---

# Finance BI Dashboard · 会计级财务经营分析（v3.0.2）

> 准则合规基线：**《企业会计准则》（CAS）** + **《中国注册会计师审计准则》**。
> 本技能产出的是**经营分析 / 管理建议书**性质成果，并非法定审计报告；所有结论均附带**数据质量声明**与**勾稽校验**，可溯源、可复核、可分发。
>
> **v2.0 升级**：融合 git 全网 12+ 优质会计/审计/财务分析开源 skill（详见 §13 合并来源），分析维度由 12 扩展至 **24 个（D1–D24）**，新增 Benford 舞弊校验、PII 数据安全、审计重要性、行业对标、张氏核心利润、杜邦/Altman、7 表审计底稿、Word 报告、PDF 取数等能力，并保持「优雅降级」——缺科目时自动标注待补、不报错。
> **v2.1 升级**：新增 **D25 现金流量预测 + 交易级异常检测**（修复 v2.0 中 `cash.bank_monthly` 3–6 月 `close` 缺失 bug，回填完整余额链；基于完整链做 H2 三情景预测 + 13 周滚动头寸 + 应收回收进度表；对银行流水/开票/报销/应收做 IQR+修正Z分数异常检测）。看板新增第 10 个页签「预测与预警」，PPT 增至 9 页、PDF 增至八章、MD 增至十二章、审计底稿增至 8 表。
> **v3.0 升级**：新增 **D26 合并报表/预算差异/DCF 估值**（吸收 git 全网 GAJETOso/financeskills（MIT，算法抽取 + 适配 CAS）—— 集团合并报表（商誉完全/部分权益法 + 少数股东 NCI + 权益抵销）、预算差异分析、DCF 企业价值/IRR/敏感性）；D19 新增 **Beneish M-score**（来自 HKUDS/FinMem + jindage 财务造假检测方法论）；D23 审计底稿升级为 **9 表**（吸收 a5c/audit-workpapers 的 PBC 提供清单 + tickmark 图例 + 调整/重分类追踪）。看板新增第 11 个页签「规划与估值 PLANNING」，PPT 增至 10 页、PDF 增至九章、MD 增至十三章、Word 增至 13 节、审计底稿增至 9 表。保持优雅降级。
> **v3.0.1 升级（专业审计加固）**：经顶级会计团队穿透式复审，修复 `augment.py` 缩进崩溃、全量除法除零、6 个构建器 NaN 泄漏、Beneish M-score 八因子齐全性判定、DCF 负经营现金流旗标与 WACC 防御；看板 JS 增加除零/空列表守卫；文档与代码计数全面对齐（审计底稿 9 表、看板 11 页签、PPT 10 页、PDF 九章、MD 十三章、Word 13 节、维度 D1–D26）。验证台 10/10 + 100/100 回归全过。
> **v3.0.2 脱敏发布（公开分发版）**：与 v3.0.1 逻辑完全一致，仅做**去标识化**——真实公司名 / 客户名 / 区域码 / 本地绝对路径全部替换为通用占位符（`示例科技有限公司` / `示例客户甲` / `示例区域` / 相对目录 `data`·`output`），不含任何业务或个人信息，可安全上传 GitHub 等公开仓库。验证台 10/10 + 100/100 回归全过。

把任意「财务/业务资料库」变成**多格式、零外链、可双击离线打开**的交付物，并支持点击任意图表下钻到**交易级明细**。

---

## 1. 何时用

- 老板/财务负责人要给管理层、股东、银行、投资人看「经营分析看板」，要求专业、严谨、可审计。
- 资料是乱七八糟的 Excel（开票、报销、AR、银行账、订单报告）+ 少量 PDF。
- 要「发给别人，对方打开不报错」→ 必须自包含（HTML 内联 Chart.js）。
- 要「点某个客户/报销人/区域看明细」→ 钻取弹窗（HTML 版）。
- 要「正式上会 / 归档」→ 同步产出 PPT（汇报用）与 PDF（正式归档用）。
- 要「符合中国准则口径」→ 所有指标附注 CAS 条款、计提政策与红旗阈值。

---

## 2. 合规基线（CAS 条款 ↔ 分析维度映射）

| # | 分析维度 | 主要 CAS 条款 | 审计准则 | 监管/披露要求 |
|---|---|---|---|---|
| D1 | 应收账款坏账准备 | CAS 22（金融工具）/ 旧 CAS 8（资产减值） | 1311 存货等资产监盘、1301 审计证据 | 按账龄/预期信用损失计提，附注披露 |
| D2 | 账龄结构与逾期披露 | CAS 30（财务报表列报） | 1331 期后事项 | 应收账龄分段披露 |
| D3 | 客户集中度 / 收入集中 | CAS 36（关联方披露）/ 持续经营 | 1324 持续经营 | 前五大客户占比披露 |
| D4 | 收入质量与账期（DSO） | CAS 14（收入，五步法） | 1141 财务报表审计总体 | 时段法/时点法确认 |
| D5 | 现金流质量 | CAS 31（现金流量表） | 1312 函证 | 经营/投资/筹资活动现金流 |
| D6 | 关联方及关联交易 | CAS 36（关联方披露） | 1323 关联方审计 | 关联方认定+交易披露 |
| D7 | 持续经营能力 | CAS 30 / 审计 1324 | 1324 持续经营 | 重大不确定性披露 |
| D8 | 风险登记册 | 企业内部控制规范 | 1211 了解被审计单位 | 风险矩阵 |
| D9 | 费用结构与报销合规 | CAS 30 / CAS 9（职工薪酬） | 1311 存货等盘点 | 费用归属期间 |
| D10 | 应收账款周转率 | 财务分析通用 | — | 周转效率 |
| D11 | 现金覆盖倍数（流动性） | CAS 30 | 1324 持续经营 | 短期偿债 |
| D12 | 数据质量声明 | — | 315 风险评估、500 审计证据 | 证据充分适当 |
| D13 | 毛利率/成本结构 | CAS 1（存货）/ CAS 14 | — | 缺 COGS 时标注待补 |
| D14 | 流动/速动比率 | CAS 30 | 1324 持续经营 | 缺流动负债时标注待补 |
| D15 | 现金流量预测 | CAS 31 | — | 滚动预测（预留） |
| D16 | 杜邦分析 + Altman Z | 财务分析通用 | 1324 持续经营 | ROE 拆解 / 破产风险 |
| D17 | 同行业对标 | — | — | 内置行业基准库 |
| D18 | 自动化取数（PDF/系统） | — | — | pdfplumber 发票提取 |
| D19 | 舞弊风险校验（Benford+排雷+M-score） | CAS 14 / 36 | 1141 / 舞弊考量 | 首位数 MAD + 30 条排雷 + Beneish M-score |
| D20 | 核心利润与获现率（张氏） | CAS 30 | — | 核心利润含金量 |
| D21 | 审计重要性水平 | — | ISA 320 / 1141 | 整体/实际执行/临界值 |
| D22 | 数据安全与合规（PII） | 《个人信息保护法》 | 数据治理 | 脱敏+血缘+审计追踪 |
| D23 | 审计报告复核（9 表底稿 · a5c PBC/tickmark/调整追踪） | 审计准则通用 | ISA 230/315 | 风险/重要性/内控/实质性/关联方/持续经营/结论/预测与异常/程序索引PBC |
| D24 | 多格式交付（Word/PDF提取） | — | — | docx + PDF 取数 |
| D25 | 现金流量预测 + 交易级异常检测 | CAS 31 | — | H2 三情景 + 13 周滚动 + 应收回收 + IQR/修正Z 异常 |
| D26 | 合并报表/预算差异/DCF 估值 | CAS 33 / 企业会计准则第20号 | — | 集团合并(商誉/NCI/抵销) + 预算差异 + DCF 企业价值 |

> 说明：本技能定位为**管理用经营分析**，不构成法定审计意见；D1–D11 的阈值与计提比例为**行业常见保守口径**，正式审计时应以被审计单位会计政策与企业会计准则具体应用指南为准。D13–D24 中标注「待补」的维度在缺对应科目时**优雅降级**（输出公式+缺失输入清单，不报错），接入完整三表后自动精确化。

---

## 3. 二十四大分析维度（公式 · 数据源 · 阈值 · 红旗 · 输出字段）

### D1 应收账款坏账准备（Bad-debt Provision）
- **公式**：`坏账准备 = (未逾期+0-6月+6-12月逾期)×5% + 逾期>1年×30%`；`净应收 = 期末应收 − 坏账准备`。
- **数据源**：`ar.series[-1]`（期末应收、逾期、账龄三桶）。
- **阈值/红旗**：账上未计提 → 🔴 利润虚增；>1年桶未细分 → 🟠 30% 为保守下限估计，正式应细分 2-3年(30–50%)、3年以上(50–100%)。
- **CAS**：CAS 22（预期信用损失）/ 旧 CAS 8。
- **输出**：`accounting.bad_debt.{prov_total, net_ar, prov_pct_of_ar, policy}`。

### D2 账龄结构与逾期披露（Aging & Overdue）
- **公式**：账龄三桶（0-6月 / 6-12月 / >1年）是**逾期额**的分拆；`账期内 = 期末应收 − 逾期`。各桶占比 = 桶额 / 期末应收。
- **校验（勾稽）**：`age_0_6 + age_6_12 + age_1plus ≈ latest_overdue`（容差 < 1 元）。
- **红旗**：>1年占比 > 30% 应收 → 🔴 硬风险。
- **CAS**：CAS 30 列报披露。
- **输出**：`accounting.aging_on_ar.{current, age_0_6, age_6_12, age_1plus}`。

### D3 客户/收入集中度（HHI）
- **公式**：`HHI = Σ(各客户收入占比%)²`（含残余项）；分级 `<1500 竞争型 / 1500–2500 中度集中 / >2500 高度集中`。
- **红旗**：`top1 收入占比 > 30%` 或 HHI > 2500 → 🔴 单点依赖。
- **CAS**：CAS 36 / 持续经营。
- **输出**：`accounting.concentration.{rev_hhi, ar_hhi, rev_hhi_grade, top1_rev_share, top10_rev_share, top1_ar_share}`。

### D4 收入质量与账期（DSO）
- **公式**：`DSO = 期末应收 / (YTD收入 / 天数)`；`AR/收入% = 期末应收 / YTD收入 × 100`。
- **红旗**：`DSO > 90天` 或 `AR/收入 > 60%` → 🟠 资金占用偏高。
- **CAS**：CAS 14。
- **输出**：`accounting.revenue_quality.{dso_days, ar_to_rev_pct, coll_invoiced_proxy_pct}`。

### D5 现金流质量（Cash-flow Quality）
- **公式**：月末余额链 `close[m] = (close[m-1] or open[m]) + net[m]`；`现金/逾期倍数 = 期末余额 / 逾期应收`；`现金降幅% = (峰值−期末)/峰值`。
- **红旗**：`现金/逾期倍数 < 1` → 🔴 流动性吃紧；降幅 > 40% → 🟠。
- **CAS**：CAS 31。
- **输出**：`accounting.cash_quality.{bank_close_series, peak_close, jun_close, drop_pct, ytd_net, cash_cover_overdue_x}`。

### D6 关联方及关联交易（Related Party）
- **公式**：按 `related_keywords`（如"示例关联方"）在 AR 客户名匹配；`关联方应收占比 = 关联方尚欠 / 期末应收`；`回款率 = 已收 / 送货额`。
- **红旗**：关联方回款率 < 50% 或 0 回款户数 > 0 → 🔴 应专项披露+催收。
- **CAS**：CAS 36。
- **输出**：`accounting.related_party.{customers, total_owed, coll_pct, zero_coll_count, share_of_ar, keywords}`。

### D7 持续经营能力（Going Concern）
- **公式**：基准情景（H2 正常回款，持续经营无重大不确定性）；压力情景（>1年全部损失上限 `stress_loss_up`）；缺口 `prov_gap = 应计提 − 账面计提(默认0)`。
- **红旗**：`stress_loss_up` 占净资产比重高 → 🟠 需披露不确定性。
- **CAS**：CAS 30 / 审计 1324。
- **输出**：`accounting.going_concern.{base_case, stress_loss_up, prov_gap, assessment}`。

### D8 风险登记册（Risk Register）
- **评分模型**：`score ≥ 80 CRITICAL / ≥55 HIGH / ≥35 MEDIUM / 其他 LOW`；自动纳入 R1 关联方、R2 >1年、R3 逾期、R4 坏账不足、R5 现金骤降、R6 单点依赖、R7 区域断崖(可选)、R8 AR/收入比。
- **CAS**：企业内控规范 / 审计 1211。
- **输出**：`accounting.risk_register[]`（id/title/amt/score/level/type/desc/action）。

### D9 费用结构与报销合规（Expense Structure）
- **公式**：按内容关键词归类（通讯/差旅/餐饮招待/生产测试/办公采购/物流样品/人力薪酬/其他）；按月、按报销人聚合。
- **红旗**：无采购成本/制造费用 → 🟠 无法算毛利，需在数据质量声明披露。
- **CAS**：CAS 30 / CAS 9。
- **输出**：`expense.{monthly, by_category, by_rep, total}`。

### D10 应收账款周转率（AR Turnover）
- **公式**：`周转率 = YTD收入 / 期末应收`；`周转天数 = 365 / 周转率`（或 DSO 近似）。
- **CAS**：财务分析通用。
- **输出**：由 `revenue_quality.dso_days` 反推。

### D11 现金覆盖倍数（Liquidity Coverage）
- **公式**：`cash_cover_overdue_x = 期末余额 / 逾期应收`（见 D5）。
- **CAS**：CAS 30 / 审计 1324。

### D12 数据质量声明（Data Quality Statement）
- **内容**：资料覆盖范围、缺失项（毛利/流动负债/期初AR）、勾稽方法、局限与假设。
- **CAS**：审计 315 / 500。
- **输出**：`accounting.data_limitations[]` + 各产物"数据质量"页/章节。

### D13 毛利率/成本结构（v2.0）
- **公式**：`毛利率 = (收入 − 营业成本) / 收入`。
- **降级**：本项目缺 COGS → 标注「待补」，接入成本表后启用。
- **输出**：`accounting.benchmark.metrics[毛利率]`（行业对标联动）。

### D14 流动/速动比率（v2.0）
- **公式**：`流动比率 = 流动资产 / 流动负债`；`速动 = (流动资产 − 存货) / 流动负债`。
- **降级**：缺流动负债 → 标注待补。
- **输出**：行业对标项内联动。

### D15 现金流量预测（v2.0 预留 → v2.1 实现为 D25）
- **计划**：滚动 13 周经营现金流预测（基于 bank_monthly 趋势 + AR 回款节奏）。
- **状态**：✅ 已在 **D25** 完整实现（见下）。

### D16 杜邦分析 + Altman Z 破产风险（v2.0）
- **杜邦**：`ROE = 净利率 × 总资产周转率 × 权益乘数`。
- **Altman Z（制造业）**：`Z = 1.2·(WC/TA)+1.4·(RE/TA)+3.3·(EBIT/TA)+0.6·(MVE/Debt)+1.0·(Sales/TA)`；Z>2.99 安全区 / 1.81–2.99 灰色 / <1.81 高风险。
- **降级**：缺资产负债表 → 输出公式 + 缺失输入清单，净利率给出代理值。
- **输出**：`accounting.dupont` / `accounting.altman`。

### D17 同行业对标（v2.0）
- **基准库**：内置 通用制造业/房地产/零售/科技/通用 的毛利率/流动比率/速动/应收周转/DSO/Altman/ROE；可替换为 A 股实测库。
- **评级**：优/中/差 + 偏离度%。
- **输出**：`accounting.benchmark.metrics[]`。

### D18 自动化取数 · PDF 发票/财报提取（v2.0）
- **能力**：pdfplumber 扫描 PDF，正则解析 发票号/日期/销售方/税号/金额，并入 `data.json['pdf_invoices']`。
- **输出**：`pdf_invoices[]` + `pdf_scan_summary`。

### D19 舞弊风险校验（Benford + 会计恒等式 + 财报排雷，v2.0）
- **Benford**：对 AR/费用/开票/银行交易金额做首位数分布检验，MAD < 0.006 高度合规 / < 0.012 可接受 / < 0.015 边际 / ≥ 0.015 偏离(疑似操纵)。
- **会计恒等式/勾稽**：AR 明细合计 vs 期末应收；开票合计 vs 收入；银行余额链闭合。
- **排雷规则**：应收增速 vs 收入、现金流与盈利背离、关联方占款、客户集中、长账龄、坏账计提不足、期末突击确认、大额整数交易、DSO、现金骤降、流动性覆盖、关联方零回款（缺科目规则已预留）。
- **输出**：`accounting.fraud.{benford, identity, minesweeper, score, risk_level, red_flags}`。

### D20 核心利润与获现率（张新民框架，v2.0）
- **核心利润** = 营业收入 − 营业成本 − 税金及附加 − 销售/管理/财务费用 + 与经营相关的其他收益。
- **获现率（含金量）** = 经营活动现金流量净额 / 核心利润；≥1 优 / ≥0.7 中 / <0.7 差。
- **降级**：缺营业成本/三费拆分 → 代理口径并显著标注。
- **输出**：`accounting.core_profit`。

### D21 审计重要性水平（v2.0）
- **基准** = max(营业收入, 资产总额, 净资产)；**整体重要性** = 基准 × 0.5%；**实际执行** = 整体 × 75%；**明显微小** = 整体 × 50%。
- **ISA 检查清单**：风险评估/重要性/内控/实质性程序/关联方/持续经营/期后/舞弊/结论。
- **输出**：`accounting.audit`。

### D22 数据安全与合规（PII 脱敏 + 数据血缘 + 审计追踪，v2.0）
- **PII 检测**：身份证 / 手机 / 银行卡 / 邮箱 正则扫描所有交易明细文本字段。
- **脱敏**：身份证留前6后4、手机留前3后4、银行卡留前4后4、邮箱隐去中间。
- **数据血缘**：记录检测发生的 segment/field；**审计追踪**：操作时间/主体/动作/数据分类/留存策略。
- **输出**：`accounting.pii` + `mask_record()` 供展示层按需脱敏。

### D23 审计报告复核（9 表底稿，v3.0 · a5c 增强）
- **9 表**：风险评估 / 重要性水平 / 了解内控 / 实质性程序 / 关联方 / 持续经营 / 审计结论 / 预测与异常(D25) / 程序索引与PBC。
- **v3.0 增强（吸收 a5c/audit-workpapers）**：第 9 表新增 ① PBC 提供清单（10 项 PBC-01..PBC-10，含状态色阶）；② tickmark 图例（√/F/C/T/A/R/N/A/◆ 含义）；③ 调整/重分类追踪（由风险登记册红/橙 + 舞弊红旗自动生成 ADJ 调整分录建议）。
- **输出**：`审计底稿_9表.xlsx`（openpyxl，带风险色阶 + PBC 状态色）。

### D24 多格式交付（Word 报告 + PDF 取数，v2.0）
- **Word**：`财务经营分析报告.docx`（python-docx，覆盖 D1–D22，可二次编辑归档）。
- **PDF 取数**：见 D18。

### D25 现金流量预测 + 交易级异常检测（v2.1）
- **现金余额链修复（前置）**：v2.0 中 `cash.bank_monthly` 的 3–6 月 `close` 因源表无余额列而缺失；v2.1 在 `augment.py` 中将反推余额（`close = open + net`）回填写回 `bank_monthly`，使下游（cash_chain 勾稽 / 预测 / 看板）拿到完整、闭合的余额链（Mar–Jun opens 经核验由 `open+net` 递推得出，回填一致）。
- **H2 2026 现金预测（三情景）**：月净现金流线性回归趋势 + 近期 3 月运行率夹取，得到基线净流量；叠加「应收回收进度表」注入做乐观（加速回款）/悲观（回款滞后）情景，输出 7–12 月月末余额三曲线。
- **13 周滚动头寸**：周度净流量 = 近期月均 / 4.33；应收回收按 H2 期望的 60% 前置注入（前 6 周加速），输出基线/乐观/悲观三曲线与最低头寸周次。
- **应收回收进度表**：账龄桶（账期内/0-6月/6-12月/>1年）× 回收概率（0.90/0.70/0.45/0.20，与坏账政策一致）→ 期望/乐观/悲观回收额。
- **流动性预警**：以 `max(100万, |近期月均净|×1.5)` 为警戒线，标记基线/悲观情景下跌破月份的预警级别（高/中/低）。
- **交易级异常检测**：对 `cash/revenue/expense/ar.details` 金额做 **IQR(1.5×) + 修正Z分数(MAD, |Mz|>3.5)** 双规则筛查，并统计超过「重要性阈值」的高金额笔数（现金≥100万/开票≥50万/报销≥5万/应收≥50万）供业务核实。
- **输出**：`accounting.cash_forecast.{h2_forecast, rolling_13w, liquidity_alert, ar_collection_schedule, trend}` + `accounting.anomaly.{cash, revenue, expense, ar, summary}`。
  - **红旗**：基线年末余额转负 / 13 周最低头寸转负 / 流动性预警=高 / 高金额异常笔数突增 → 触发现金流专项管控。

### D26 合并报表 / 预算差异 / DCF 估值（v3.0）
- **合并报表 consolidation**（吸收 GAJETOso · corporate-consolidation，MIT）：读 `data.json['group']`（母公司 + 子公司清单），计算商誉（完全/部分权益法 `goodwill_full/goodwill_partial`）、少数股东 NCI（`nci_at_acquisition` / `nci_share_of_profit`）、权益抵销（`consolidated_equity_check`）；单主体公司无集团数据 → 优雅降级（状态=待补 + GAJETOso 算法自检示例 + 待补输入清单）。
- **预算差异 budget_variance**（吸收 GAJETOso · budget-forecast）：以 H1 实际为基，H1 月度收入**线性回归趋势**作基准，做 实际 vs 趋势 差异分析（偏离>±15% 标记显著）；H2 前向预算按运行率投影（避免 volatile 月增长复利失控）；全年预算 = H1 实际 + H2 投影。
- **DCF 估值 dcf**（吸收 GAJETOso · investment-analysis）：以经营现金流（核心利润获现代理）为基，5 年投影 + Gordon 终值，算企业价值(EV) / IRR(≈WACC 自检) / 回收期 / 敏感性（WACC±1pp、growth±2pp）。
- **优雅降级**：无 `group` → 合并报表=待补 + 算法自检；无 `prior_period` → M-score=待补 + 8 比率公式 + 当前期快照。
- **输出**：`accounting.consolidation` / `accounting.budget_variance` / `accounting.dcf`。
- **CAS**：CAS 33 合并财务报表 / 企业会计准则第20号 企业合并；DCF 为估值参考（EV 非股权价值，缺净债务）。

---

## 4. 端到端管线（8 步，每步独立可重跑）

```
①  extract.py           → 读源 Excel 写 data.json（meta/revenue/ar/cash/expense/orders 聚合段）
①b extract_pdf.py       → (v2.0) pdfplumber 扫描 PDF 发票/财报，并入 pdf_invoices（可选输入扩展）
②  augment.py           → 算会计指标(D1–D8) 写 accounting 段
③  augment_details.py   → 抽交易级明细写各段 .details（先备份 data.json → .bak）
③b fraud_check.py       → (v2.0·D19) Benford + 会计恒等式 + 财报排雷 → accounting.fraud
③c pii_mask.py          → (v2.0·D22) PII 检测/脱敏 + 数据血缘 + 审计追踪 → accounting.pii
③d audit_materiality.py → (v2.0·D21) 重要性水平 + ISA 检查清单 → accounting.audit
③e industry_benchmark.py→ (v2.0·D17) 行业对标评级 → accounting.benchmark
③f core_profit.py       → (v2.0·D20) 张氏核心利润/获现率 → accounting.core_profit
③g dupont_altman.py     → (v2.0·D16) 杜邦 + Altman Z → accounting.dupont/altman
③h cash_forecast.py     → (v2.1·D25) H2 现金预测 + 13 周滚动 + 应收回收进度 + 异常检测 → accounting.cash_forecast/anomaly
③i consolidation_budget_dcf.py → (v3.0·D26) 合并报表/预算差异/DCF → accounting.consolidation/budget_variance/dcf
④  build_dashboard.py   → 读 data.json + 本地 chart.umd.min.js → 自包含 dashboard.html（11 页签含「审计与风控」「预测与预警」「规划与估值」+ 钻取）
⑤  build_ppt.py         → 读 data.json → 专业中文 PPT（10 页，含审计与风控页 + 预测与异常页 + 规划与估值页）
⑥  build_pdf.py         → 读 data.json → A4 正式 PDF（九章，含审计与风控扩展 + 预测与异常 + 规划与估值）
⑥b build_report_md.py   → 读 data.json → 顶级团队版 Markdown 会计报告（十三章）
⑥c build_docx.py        → (v2.0·D24) 读 data.json → Word 审计报告（可编辑归档，覆盖 D1–D24 + D25 + D26）
⑥d audit_workpapers.py  → (v2.0·D23) 读 data.json → 9 表审计底稿 Excel（含预测与异常表 + 程序索引PBC）
⑦  validate_skill.py    → 循环校验：结构+勾稽+v2.0段+v2.1段+产物存在性+占位符清除；支持 --loops N / --core
⑧  pack.py              → 打包交付物为 finance-bi-dashboard_打包.zip（分发包，手动跑一次即可）
```

> 运行顺序与每脚本顶部 ★ 项目配置 改法见 **`scripts/00_QUICKSTART.md`**。

---

## 5. 多格式输出规范（交付物）

| 产物 | 用途 | 规范 |
|---|---|---|
| `dashboard.html` | **自包含主交付**（内联 Chart.js，双击离线可用） | 11 页签：总览/收入/应收/现金流/费用/会计视图/高危预警/审计与风控/预测与预警/规划与估值/数据质量；可钻取 |
| `经营分析看板_独立版.html` | 同内容改名便于发送 | 同 dashboard.html |
| `财务经营分析报告.pptx` | 上会汇报 | 微软雅黑；封面/管理层摘要/关键审计事项/财务比率/风险登记册/审计建议/数据质量/审计与风控/预测与异常 |
| `财务经营分析报告.pdf` | 正式归档 | A4；页眉页脚+页码；MD&A/关键审计事项/各维度/风险/建议/数据质量声明/审计与风控扩展/预测与异常/规划与估值 |
| `会计分析报告_顶级团队.md` | 文本归档/复制 | 十三章会计报告（含审计与风控扩展 + 预测与异常 + 规划与估值） |
| `财务经营分析报告.docx` | **v2.0** Word 归档 | python-docx，覆盖 D1–D24 + D25 + D26，可二次编辑 |
| `审计底稿_9表.xlsx` | **v3.0** 审计底稿 | 9 表：风险/重要性/内控/实质性/关联方/持续经营/结论/预测与异常/程序索引PBC |
| `data.json` | 中间数据 | 聚合 + `.details` 明细 + v2.0 段(fraud/pii/audit/benchmark/core_profit/dupont/altman)，可机读复核 |
| `finance-bi-dashboard_打包.zip` | 分发包 | 独立版 html + 报告 md + data.json + chart.umd.min.js + pptx + pdf + docx + xlsx |

---

## 6. 技能自带脚本（`scripts/` 目录，已固化、可直接复制改造）

> 详细运行顺序、依赖、每脚本 ★ 项目配置 改法、全局替换字面量清单见 **`scripts/00_QUICKSTART.md`**。

| 脚本 | 步骤 | 作用 | 换项目要改的 CONFIG |
|---|---|---|---|
| `extract.py` | ① | 读源 Excel → `data.json`（聚合段） | `source_dir`/`output_dir`/`company`/`files.*`/`rev_sheet_month`/`bank_sheet_month`/`ar_history` |
| `augment.py` | ② | 算会计指标(D1–D8) → 回写 `accounting` 段 | `output_dir`/`related_keywords`(关联方)/`watch_region`(关注区域) |
| `augment_details.py` | ③ | 抽交易级明细 → 回写各段 `.details`（先备份 `.bak`） | `source_dir`/`output_dir`/`files.*`/`ar_detail_sheet` |
| `build_dashboard.py` | ④ | 读 `data.json` + 本地 `chart.umd.min.js` → 自包含 `dashboard.html` | `output_dir`（+ 全局替换公司名/钻取关键词字面量） |
| `build_ppt.py` | ⑤ | 读 `data.json` → 专业中文 `财务经营分析报告.pptx` | `output_dir`/`company`/`period`/`template`(配色) |
| `build_pdf.py` | ⑥ | 读 `data.json` → A4 正式 `财务经营分析报告.pdf` | `output_dir`/`company`/`period`/`font_paths` |
| `build_report_md.py` | ⑥b | 读 `data.json` → 顶级团队版 `会计分析报告_顶级团队.md`（十三章） | `output_dir` |
| `fraud_check.py` | ③b·v2 | **D19** Benford+恒等式+排雷 → `accounting.fraud` | `output_dir` |
| `pii_mask.py` | ③c·v2 | **D22** PII 脱敏+血缘+审计追踪 → `accounting.pii` | `output_dir`/`scan_fields` |
| `audit_materiality.py` | ③d·v2 | **D21** 重要性水平+ISA 清单 → `accounting.audit` | `output_dir`/`overall_pct` |
| `industry_benchmark.py` | ③e·v2 | **D17** 行业对标评级 → `accounting.benchmark` | `output_dir`/`industry` |
| `core_profit.py` | ③f·v2 | **D20** 张氏核心利润/获现率 → `accounting.core_profit` | `output_dir` |
| `dupont_altman.py` | ③g·v2 | **D16** 杜邦+Altman Z → `accounting.dupont/altman` | `output_dir` |
| `cash_forecast.py` | ③h·v2.1 | **D25** H2 现金预测+13周滚动+应收回收进度+异常检测 → `accounting.cash_forecast/anomaly` | `output_dir` |
| `consolidation_budget_dcf.py` | ③i·v3.0 | **D26** 合并报表/预算差异/DCF → `accounting.consolidation/budget_variance/dcf` | `output_dir` |
| `build_docx.py` | ⑥c·v2 | **D24** 读 `data.json` → `财务经营分析报告.docx`（python-docx） | `output_dir`/`company`/`period` |
| `audit_workpapers.py` | ⑥d·v3 | **D23** 读 `data.json` → `审计底稿_9表.xlsx`（openpyxl） | `output_dir`/`company`/`period` |
| `extract_pdf.py` | ①b·v2 | **D18** pdfplumber 扫描 PDF 发票 → `pdf_invoices` | `source_dir`/`max_files` |
| `pack.py` | ⑧ | 打包交付物为 `finance-bi-dashboard_打包.zip` | `output_dir`/`scripts_dir` |
| `validate_skill.py` | ⑦ | 综合校验台（结构+勾稽+产物+占位符），`--loops N` 统计通过率 | `output_dir` |
| `validate_dash.js` | ④辅助 | Node 桩校验主脚本 + 钻取函数 | `node validate_dash.js [html路径]` |
| `probe_details.py` | 辅助 | 打印各源文件 sheet 名/行列数，定位结构 | `BASE`（源目录） |
| `assets/chart.umd.min.js` | — | Chart.js 4.4.1 本地版，内联进 HTML 实现离线自包含 | 无需改 |

**复制即用流程**：把 `scripts/` 整个拷到项目目录 → 按 `00_QUICKSTART.md` 改各脚本顶部 CONFIG → 顺序跑 ①②③④⑤⑥⑦。所有路径/文件名/公司名/关联方关键词/关注区域均已参数化，无需改逻辑代码。

---

## 7. 可复用核心代码

### A. 钻取弹窗 JS（贴进 build_dashboard.py 的 `<script>`，放在图表创建之前）
```js
// ===== 钻取明细弹窗（搜索 / 排序 / 分页） =====
const COLS = {
  expense:[{key:'submit',label:'提交日期'},{key:'month',label:'报销月份'},{key:'content',label:'内容'},
    {key:'amount',label:'金额',num:true,money:true},{key:'no',label:'编号'},{key:'remark',label:'备注'},{key:'pay',label:'打款日期'}],
  revenue:[{key:'date',label:'开票日期'},{key:'customer',label:'客户'},{key:'contract_no',label:'合同号'},
    {key:'contract_total',label:'合同总额',num:true,money:true},{key:'invoice_no',label:'发票号'},
    {key:'invoiced',label:'已开票',num:true,money:true},{key:'uninvoiced',label:'未开票',num:true,money:true}],
  ar:[{key:'name',label:'客户'},{key:'rep',label:'职员'},{key:'delivery_date',label:'送货日期'},
    {key:'delivery_amt',label:'送货金额',num:true,money:true},{key:'paid',label:'已收',num:true,money:true},
    {key:'owed',label:'尚欠',num:true,money:true,hl:true},{key:'po_no',label:'采购编号'},{key:'credit_term',label:'账期'},
    {key:'invoice_time',label:'开票时间'},{key:'remark',label:'备注'}],
  orders:[{key:'date',label:'日期'},{key:'region',label:'区域'},{key:'customer',label:'客户/备注'},
    {key:'amount',label:'订单金额',num:true,money:true}],
  cash:[{key:'month',label:'月份'},{key:'day',label:'日'},{key:'name',label:'收/付款名称'},
    {key:'summary',label:'摘要'},{key:'debit',label:'借方',num:true,money:true},{key:'credit',label:'贷方',num:true,money:true},
    {key:'balance',label:'余额',num:true,money:true},{key:'sys_no',label:'系统编号'}]
};
const M = { rows:[], cols:[], filtered:[], sortKey:null, sortDir:1, page:0, size:50 };
const mModal=document.getElementById('modal'), mTitle=document.getElementById('mTitle'),
      mSub=document.getElementById('mSub'), mTable=document.getElementById('mTable'),
      mSearch=document.getElementById('mSearch'), mInfo=document.getElementById('mInfo'),
      mPage=document.getElementById('mPage');
function fmtMoney(v){ return '¥'+Number(v).toLocaleString('zh-CN',{minimumFractionDigits:2,maximumFractionDigits:2}); }
function sumNum(rows,k){ return rows.reduce((a,r)=>a+(Number(r[k])||0),0); }
function renderM(){
  let rows = M.filtered;
  if(M.sortKey){ const k=M.sortKey,d=M.sortDir;
    rows=rows.slice().sort((a,b)=>{ const x=a[k],y=b[k];
      if(typeof x==='number'&&typeof y==='number') return (x-y)*d;
      return String(x==null?'':x).localeCompare(String(y==null?'':y),'zh')*d; }); }
  const total=rows.length, pages=Math.max(1,Math.ceil(total/M.size));
  if(M.page>=pages)M.page=pages-1; if(M.page<0)M.page=0;
  const start=M.page*M.size, pr=rows.slice(start,start+M.size);
  let h='<thead><tr>'; M.cols.forEach(c=>{ const ar=M.sortKey===c.key?(M.sortDir>0?' ▲':' ▼'):'';
    h+=`<th data-k="${c.key}" class="${c.num?'num':''}">${c.label}${ar}</th>`; }); h+='</tr></thead>';
  let b='<tbody>'; pr.forEach(r=>{ b+='<tr>'; M.cols.forEach(c=>{ const v=r[c.key];
    const t=c.num?(c.money?fmtMoney(v):Number(v).toLocaleString('zh-CN',{maximumFractionDigits:2})):(v===''||v==null?'—':v);
    b+=`<td class="${c.num?'num':''}"${c.hl?' style="color:var(--pk);font-weight:700"':''}>${t}</td>`; }); b+='</tr>'; });
  if(!pr.length) b+=`<tr><td colspan="${M.cols.length}" style="text-align:center;color:var(--mut)">无匹配记录</td></tr>`;
  b+='</tbody>'; mTable.innerHTML=h+b;
  const nc=M.cols.filter(c=>c.num);
  const st=nc.map(c=>c.label+'合计 '+(c.money?fmtMoney(sumNum(rows,c.key)):fmt(sumNum(rows,c.key)))).join('　·　');
  mInfo.textContent=`显示 ${total?start+1:0}-${Math.min(start+M.size,total)} / 共 ${total} 行`+(st?'　|　'+st:'');
  mPage.textContent=`第 ${M.page+1} / ${pages} 页`;
  mTable.querySelectorAll('th').forEach(th=>th.onclick=()=>{ const k=th.dataset.k; if(!k)return;
    if(M.sortKey===k)M.sortDir*=-1; else{M.sortKey=k;M.sortDir=1;} renderM(); });
}
function showDetail(title,sub,cols,rows){ M.cols=cols;M.rows=rows;M.filtered=rows;M.sortKey=null;M.sortDir=1;M.page=0;
  mTitle.textContent=title; mSub.innerHTML=sub||''; mSearch.value=''; mModal.classList.add('open'); renderM(); }
function closeM(){ mModal.classList.remove('open'); }
mModal.addEventListener('click',e=>{ if(e.target===mModal)closeM(); });
document.getElementById('mClose').onclick=closeM;
document.addEventListener('keydown',e=>{ if(e.key==='Escape')closeM(); });
mSearch.addEventListener('input',()=>{ const q=mSearch.value.trim().toLowerCase();
  M.filtered=q?M.rows.filter(r=>M.cols.some(c=>String(r[c.key]==null?'':r[c.key]).toLowerCase().includes(q))):M.rows;
  M.page=0; renderM(); });
document.getElementById('mPrev').onclick=()=>{M.page--;renderM();};
document.getElementById('mNext').onclick=()=>{M.page++;renderM();};
// 各面板入口（D.detail 来自 data.json 的 .details 数组）
function openExpenseRep(rep){ const rows=(D.expense.details||[]).filter(r=>r.rep===rep);
  showDetail('报销明细 · '+rep,`共 ${rows.length} 笔 · 合计 ${fmtMoney(sumNum(rows,'amount'))}`,COLS.expense,rows); }
function openRevenueCust(name){ const rows=(D.revenue.details||[]).filter(r=>r.customer.includes(name)||name.includes(r.customer));
  showDetail('开票明细 · '+name,`共 ${rows.length} 笔 · 已开票合计 ${fmtMoney(sumNum(rows,'invoiced'))}`,COLS.revenue,rows); }
function openArCust(name){ const rows=(D.ar.details||[]).filter(r=>r.name.includes(name)||name.includes(r.name));
  showDetail('应收款明细 · '+name,`共 ${rows.length} 笔 · 尚欠合计 ${fmtMoney(sumNum(rows,'owed'))}`,COLS.ar,rows); }
function openOrdersRegion(region){ const rows=(D.orders.details||[]).filter(r=>r.region===region);
  showDetail('订单明细 · 区域 '+region,`共 ${rows.length} 笔 · 合计 ${fmtMoney(sumNum(rows,'amount'))}`,COLS.orders,rows); }
function openCash(){ const rows=D.cash.details||[];
  showDetail('银行账明细（全期）',`共 ${rows.length} 笔`,COLS.cash,rows); }
function openRisk(r){ let rows=[],cols=COLS.ar;
  if(r.id==='R1'){rows=(D.ar.details||[]).filter(x=>x.name.includes('示例关联方'));}
  else if(r.id==='R6'){rows=(D.revenue.details||[]).filter(x=>x.customer.includes('示例客户甲'));cols=COLS.revenue;}
  else if(r.id==='R7'){rows=(D.orders.details||[]).filter(x=>x.region==='示例区域');cols=COLS.orders;}
  else if(r.id==='R5'){rows=D.cash.details||[];cols=COLS.cash;}
  else {rows=(D.ar.details||[]).slice().sort((a,b)=>b.owed-a.owed).slice(0,200);}
  showDetail('风险明细 · '+r.title,`涉及金额 ${fmt(wan(r.amt))} 万元 · 共 ${rows.length} 笔`,cols,rows); }
```
**接线**（在图表创建处加 `onClick`）：
- 收入帕累托 `c_par` / `c_hhi`：`options:Object.assign(dualOpt(),{onClick:(e,els)=>{ if(!els.length)return; openRevenueCust(tc[els[0].index].customer); }})`
- AR TOP `c_topar`：`bar(...,{onClick:(e,els)=>{ if(!els.length)return; openArCust(tc[els[0].index].customer); }})`
- 区域堆叠 `c_reg`：`onClick:(e,els)=>{ if(!els.length)return; openOrdersRegion(ks[els[0].datasetIndex]); }`
- 报销人 `c_exprep`：`onClick:(e,els)=>{ if(!els.length)return; openExpenseRep(brN[els[0].index]); }`（brN=完整报销人名数组）
- 银行流水 `c_bankflow`：`onClick:()=>openCash()`
- 风险卡片：`div.style.cursor='pointer'; div.onclick=()=>openRisk(r);`
- 可点卡片加 class `clickable` + CSS `.clickable h3:after{content:"▸ 点击钻取";...}`，并设置 `cv.style.cursor='pointer'`。

### B. 自包含内联（build_dashboard.py 末尾）
```python
HTML = HTML.replace("__CHARTJS__", CHARTJS).replace("__DATA__", json.dumps(D, ensure_ascii=False))
open(out,"w",encoding="utf-8").write(HTML)   # 交付前 grep -c "__CHARTJS__\|__DATA__" 必须为 0
```

### C. Node 桩运行时校验 harness（validate_dash.js）
抽 `dashboard.html` 中主脚本（从 `const D = ` 到最后一个 `</script>`），用 vm 跑：
```js
const fs=require('fs'), vm=require('vm');
const html=fs.readFileSync('经营看板/dashboard.html','utf-8');
const code=html.slice(html.indexOf('const D = '), html.lastIndexOf('</script>'));
const ctxStub=new Proxy({},{get:()=>()=>{},set:()=>true});
function fakeEl(){ return { textContent:'',innerHTML:'',value:'',width:300,height:200,style:{},dataset:{},
  classList:{add(){},remove(){},contains(){return false;}}, addEventListener(){},appendChild(){},
  querySelectorAll(){return [];}, querySelector(){return fakeEl();},
  get nextElementSibling(){ return {innerHTML:'',textContent:''}; },   // 懒返回，勿套娃递归！
  getContext(){return ctxStub;}, onclick:null }; }
const cache={}; const sb={ console, innerWidth:1280, innerHeight:800, requestAnimationFrame:()=>0,
  setInterval:()=>0, setTimeout:()=>0, Date,Math,JSON,Number,String,Array,Object,
  window:{addEventListener(){},innerWidth:1280,innerHeight:800},
  document:{ getElementById:id=>cache[id]||(cache[id]=fakeEl()), querySelectorAll:()=>[],
    querySelector:()=>fakeEl(), createElement:()=>fakeEl(), addEventListener(){} },
  Chart: class{ constructor(c,cfg){this.ctx=c;this.config=cfg;} static register(){} } };
sb.global=sb; vm.createContext(sb);
vm.runInContext(code, sb, {filename:'dash.js'});   // 无异常即通过
// 再实测钻取：sb.openExpenseRep('张雨薇'); sb.openArCust('示例关联方...'); sb.openCash(); ...
```

### D. PPT/PDF 生成（build_ppt.py / build_pdf.py）
- 均从 `data.json` 读取，纯参数化 CONFIG，使用**微软雅黑**中文字体（PPT 仅设字体名；PDF 用 `msyh.ttc`/`msyhbd.ttc` 注册 TTFont）。
- PPT：python-pptx；PDF：reportlab Platypus（A4、页眉页脚页码、表格+段落）。
- 详细设计见 `docs/使用手册.md` 与脚本内注释。

---

## 8. 扩展预留（v1.0 → v2.0 路线图）

> 本技能采用**插件式扩展**设计：新增维度只需在 `augment.py` 增加 Dn 段、在 `build_*.py` 增加渲染分支，并在下表登记。v1.0 已为以下功能**预留位置**（占位常量/空分支/文档 TODO），后续版本直接填充。

| 预留位 | 类型 | v2.0 状态 | v3.0+ 计划 |
|---|---|---|---|
| `D13 毛利率/成本结构` | 维度 | ✅ 优雅降级（缺 COGS 标注待补） | 接入采购/制造费用表 |
| `D14 流动/速动比率` | 维度 | ✅ 优雅降级（缺流动负债标注待补） | 接入应付/短贷表 |
| `D15 现金流量预测` | 模型 | ✅ 已在 **D25** 实现（v2.1） | 滚动 13 周预测 |
| `D16 杜邦分析 + Altman Z` | 模型 | ✅ 已实现（缺 BS 时降级+公式清单） | 接入三表精确化 |
| `D17 同行业对标` | 外部数据 | ✅ 已实现（内置 5 行业基准库） | 替换为 A 股实测库 |
| `D18 自动化取数` | 集成 | ✅ 已实现（pdfplumber PDF 提取） | 直连财务系统/API |
| `D19 舞弊风险校验` | 模型 | ✅ 已实现（Benford+恒等式+排雷） | 扩充排雷规则库 |
| `D20 核心利润/获现率` | 模型 | ✅ 已实现（张氏框架，代理口径） | 接入完整利润表 |
| `D21 审计重要性水平` | 模型 | ✅ 已实现 | — |
| `D22 数据安全/PII` | 治理 | ✅ 已实现（脱敏+血缘+审计追踪） | 扩充 PII 类别 |
| `D23 审计底稿 9 表` | 产物 | ✅ 已实现 | — |
| `D24 Word 报告` | 产物 | ✅ 已实现（build_docx.py） | — |
| `D25 现金流预测模型` | 模型 | ✅ 已实现（v2.1：H2 三情景 + 13 周滚动 + 应收回收进度 + 异常检测） | 接入周级实际流水精确化 |
| `D26 合并报表/预算差异/DCF 估值` | 模型 | ✅ 已实现（v3.0：合并报表+预算差异+DCF，吸收 GAJETOso） | — |
| 多币种`currency` | 配置 | 已支持 CNY | 接入汇率表 |
| 多主体合并`consolidation` | 配置 | ✅ 已实现（v3.0：D26 consolidation，吸收 GAJETOso corporate-consolidation） | — |

**扩展规范**见 `docs/CONTRIBUTING.md`：新增维度/产物须（1）在 `data.json` 增加可读字段；（2）在 `validate_skill.py` 增加勾稽校验；（3）在本文档 §3 登记 CAS 条款与阈值；（4）在 `CHANGELOG.md` 记录版本。

---

## 9. 避坑清单（必看，踩过一遍）

1. **AR 汇总表陷阱**：`2026-6-30AR.xlsx` 第一个 sheet 是「汇总应收款报告」（按办公室/业务员汇总），**真明细在「应收款明细」主表**。且「示例客户乙/到期应收/质保金」分项表 max_row 会被 Excel 撑到 1048574 → 读时用 `min(ws.max_row,6000)` 封顶；**明细只需取主表**，避免与分项表重复计数（会导致总欠款翻倍）。
2. **列动态定位**：订单报告有的 sheet Date 在 col0、有的在 col1；AR/开票不同 sheet 列序不同 → 一律用「表头名」扫描定位，别写死列号。
3. **账龄=逾期分拆**：账龄三桶(0-6/6-12/>1年)是**逾期额**的分拆，不是总 AR 的分拆。校验：三桶之和=逾期额。
4. **nextElementSibling 递归**：桩 DOM 里 `nextElementSibling` 必须**懒返回**（`get nextElementSibling(){return {innerHTML:''}}`），否则无限递归爆栈。
5. **zip 锁文件**：旧 zip 可能被文件预览进程占用 → `PermissionError`。直接写**新文件名**（如 `_钻取版.zip`）绕过，别硬覆盖。
6. **Excel 日期类型**：openpyxl 读出来是 Python `datetime`，不是 `pd.Timestamp` → 判断用 `isinstance(v,datetime)`。
7. **双轴帕累托图括号**：内嵌大对象易错 → 抽出 `dualOpt(y1Color,y1Max)` 助手复用。
8. **路径分隔符**：native Windows 的 python/node `.exe` **不识别** Git Bash 的 `/c/` 路径 → 传参用 `C:/...` 或 `C:\...`（脚本内统一用 `os.path.join` / `r"..."`）。
9. **PPT/PDF 中文乱码**：务必设置字体为**微软雅黑**（PPT 字体名字符串；PDF 注册 `msyh.ttc` 并用 `subfontIndex=0`）；reportlab 避免 Unicode 上/下标字形，改用 `<sub>`/`<super>` 标签。
10. **openpyxl 读大文件内存爆炸**：`load_workbook(..., data_only=True)` 会把整本工作簿+样式读进内存，大文件峰值可达 **数 GB / 近百秒**。务必用 `read_only=True, data_only=True` 流式读取，每段处理完立即 `wb.close()`。本技能 `augment_details.py` 已采用此写法（5.9GB→20MB、98s→8s）。
11. **venv 与 managed python 别混用（v2.0）**：pandas/openpyxl/python-pptx/reportlab 装在 **analytics venv**（`...\python\envs\analytics\Scripts\python.exe`），而 `python-docx`/`pdfplumber` 需 `pip install` 进**同一 venv**。若误用 managed python（`...\versions\3.13.12\python.exe`）跑 `audit_workpapers.py`/`build_docx.py` 会 `ModuleNotFoundError`。`validate_skill.py` 的 `PY` 已统一指向 analytics venv，换机部署时务必改 `PY` 并在该 venv 装齐依赖。

---

## 10. 交付前校验清单

- [ ] `validate_skill.py --loops 100 --skip-extract --core` 通过率 100/100（结构+勾稽+v2.0段+看板）。
- [ ] `validate_skill.py --loops 100 --skip-extract` 通过率 100/100（全产物：html/pptx/pdf/md/docx/xlsx）。
- [ ] `dashboard.html` 中 `grep -c "__CHARTJS__\|__DATA__"` = 0（确无外链）。
- [ ] `validate_dash.js` 跑通，主脚本无异常 + 6 类钻取函数可调用 + 审计与风控页签渲染。
- [ ] 钻取明细总额与聚合数勾稽（如 AR 明细总欠款 ≈ 看板期末 AR）。
- [ ] PPT/PDF/DOCX/XLSX 可打开、中文正常、含数据质量声明与审计与风控章节。
- [ ] 风险登记册按评分降序，CRITICAL 红 / HIGH 橙 / MEDIUM 黄。
- [ ] zip 包含 独立版 html + 报告 md + data.json + chart.umd.min.js + pptx + pdf + docx + xlsx。

---

## 11. 调用示例

> 「用 finance-bi-dashboard 模板，把 D:\客户资料\XX公司 的财务 Excel 做成经营看板，要科幻风、能点开看每个客户明细、同时出 PPT/PDF/Word 正式版和审计底稿、打包发我」

技能会自动：定位源文件 → 抽聚合+明细 → 算会计指标(D1–D12)与风险 → 跑 v2.0 模块(D16–D24：Benford/PII/重要性/对标/核心利润/杜邦) → 生成自包含科幻钻取 HTML(11页签) + 专业 PPT + 正式 PDF/Word + 9表审计底稿 + MD 报告 → 校验 → 打包。

---

## 12. Git 发布与文档

本技能以**开源模板**形式发布到 Git，配套文档全部留存于 `docs/`：
- `docs/README.md` —— 项目说明、安装、快速开始、目录结构。
- `docs/CHANGELOG.md` —— 版本记录（v1.0.0 / v2.0.0 + 未来版本占位）。
- `docs/CONTRIBUTING.md` —— 贡献规范：如何新增维度/产物、更新阈值、插件式扩展。
- `docs/准则对照表.md` —— CAS 条款 ↔ 指标 ↔ 阈值 完整对照。
- `docs/使用手册.md` —— 端到端操作手册（换项目改哪些 CONFIG、全局替换清单、排错）。
- `docs/合并来源.md` —— **v2.0** 融合的 git 全网外部 skill 清单与对应能力（溯源/致谢）。
- `LICENSE` —— MIT 许可证。

详见各文档。发布前请运行 `validate_skill.py --loops 100` 确认稳定。

---

## 13. 合并来源（v2.0 融合的 git 全网外部 skill）

本技能 v2.0 通过检索 git 全网，融合以下优质开源会计/审计/财务分析 skill 的核心能力（均标注来源、保留其方法论，代码已重写适配 CAS 口径与本项目数据结构）：

| 来源 skill | 融合能力 | 对应维度 |
|---|---|---|
| `lucastusch/Financial-Statement-Validator` | Benford 定律(MAD) + 会计恒等式/报表勾稽 | D19 |
| `yzqzy/financial-report-minesweeper` | 财报排雷 30 条规则阈值 | D19 |
| `CSlawyer1985/financial-statement-analyst` | 盈余操纵/欺诈检测、收入准则红旗 | D19 |
| `openclaw/skills · zhang-financial-analysis-doubao` | 张新民核心利润/获现率/产业链话语权 | D20 |
| `JerBouma/FinanceToolkit`（4600★ MIT） | 杜邦 / Altman Z-Score / 行业基准 | D16/D17 |
| `Nse13/audit-flow` | 审计重要性(materiality) / ISA checklist / 风险领域 | D21 |
| `nigo81/nigo-skills · audit-report-checker` | 7 表审计底稿复核 | D23 |
| `nigo81/nigo-skills · related-party-identification` | 隐形关联方识别（增强 D6） | D6/D23 |
| `nigo81/nigo-skills · chen-yiwei/tianchuan-perspective` | 会计准则实务判断 / 审计方法论 | 方法论 |
| `lgwanai/echart-skill` | PII 脱敏 / 数据血缘 / 审计追踪 / 本地优先 | D22 |
| `eosphoros-ai/DB-GPT · financial-report-analyzer` | pdfplumber PDF 财报提取 | D18 |
| `LinCifeng/PDF_Financial_Report_Analysis` | PDF 正则提取 + 质量报告 | D18 |
| `agennext-agent-skills-finance` | ratio/dcf/budget_variance/forecast（预留 D26） | D26 |
| `hhhh124hhhh/nexus-caiwu-skill` | A 股行业数据（行业库可替换源） | D17 |
| `gaaiyun/excel-skill` | 金融三表联动 / 杜邦分解 / 行业模板 | D16 |
| `GAJETOso/financeskills`（MIT） | corporate-consolidation / budget-forecast / investment-analysis（NPV/IRR/DCF） | D26 |
| `HKUDS/FinMem` · `jindage/financial-analysis` | Beneish M-score 八因子盈余操纵模型 | D19 |
| `a5c/audit-workpapers`（509★） | PBC 提供清单 / tickmark 图例 / 调整与重分类追踪 | D23 |

> 致谢上述开源社区。本技能仅吸收其**方法论与算法思路**，所有代码依据《企业会计准则》口径与本项目数据结构重写，符合 MIT 兼容的开源融合规范。
