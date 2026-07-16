# Finance BI Dashboard · 会计级财务经营分析技能

> 版本：**v3.0.0** ｜ 许可证：MIT ｜ 合规基线：《企业会计准则》（CAS）+《中国注册会计师审计准则》
> v2.0 融合 git 全网 12+ 优质会计/审计/财务分析开源 skill（详见 [`合并来源.md`](./合并来源.md)），分析维度 12 → **24（D1–D24）**。

把混杂的财务/业务资料库（Excel/PDF 开票记录、对账、应收 AR、员工报销、银行账、每日订单报告）
**一键转成多格式、可分发、可钻取、附风险登记册与数据质量声明的会计级交付物**。

---

## 1. 功能特性

- **多格式输出**：自包含 BI 控制台 HTML（11 页签含「审计与风控」「预测与预警」「规划与估值」，内联 Chart.js，离线双击可用、可钻取交易级明细）+ 专业中文 PPT + A4 正式 PDF + **Word 报告** + **9 表审计底稿 Excel** + 会计报告 Markdown + `data.json`。
- **中国准则合规**：**二十六大数据维度（D1–D26）**，每个维度附注 CAS 条款、计提政策、阈值与红旗，详见 [`准则对照表.md`](./准则对照表.md)。
- **v2.0 新增能力**：
  - **D19 舞弊校验**：Benford 定律(MAD) + 会计恒等式勾稽 + 财报排雷规则 + 红旗清单。
  - **D22 数据安全**：PII 检测/脱敏 + 数据血缘 + 审计追踪（符合《个人信息保护法》）。
  - **D21 审计重要性**：整体/实际执行/明显微小临界值 + ISA 检查清单。
  - **D17 行业对标**：内置 5 行业基准库，自动评级。
  - **D20 核心利润/获现率**（张氏框架）、**D16 杜邦 + Altman Z 破产风险**。
  - **D18 PDF 取数**（pdfplumber）、**D23 审计底稿 9 表**、**D24 Word 报告**。
- **优雅降级**：缺科目（如 COGS/负债/资产负债表）时自动标注待补、输出公式+缺失输入清单，不报错。
- **风险导向**：自动生成风险登记册与对应审计建议，管理层可直接落地。
- **钻取明细**：HTML 看板支持点击任意图表下钻到交易级明细（搜索/排序/分页弹窗）。
- **质量门禁**：`validate_skill.py` 支持 `--loops N` / `--core` 循环校验，统计通过率。

---

## 2. 目录结构

```
finance-bi-dashboard/
├── SKILL.md                      # 技能主文档（维度/管线/扩展/坑位/校验清单）
├── LICENSE                       # MIT 许可证
├── docs/
│   ├── README.md                 # 本文件
│   ├── CHANGELOG.md              # 版本记录（含未来版本占位）
│   ├── CONTRIBUTING.md           # 贡献规范（扩展维度/产物/阈值）
│   ├── 准则对照表.md             # CAS 条款 ↔ 指标 ↔ 阈值 完整对照
│   ├── 合并来源.md               # v2.0 融合的外部 skill 溯源与致谢
│   └── 使用手册.md               # 端到端操作手册
└── scripts/
    ├── 00_QUICKSTART.md          # 快速开始（运行顺序 + 每脚本 CONFIG 改法）
    ├── extract.py                # ① 抽取源 Excel → data.json
    ├── extract_pdf.py            # ①b (v2.0) PDF 发票提取 → pdf_invoices
    ├── augment.py                # ② 算会计指标(D1–D8) → accounting 段
    ├── augment_details.py        # ③ 抽交易级明细 → .details（先备份 .bak）
    ├── fraud_check.py            # ③b (v2.0·D19) Benford+恒等式+排雷
    ├── pii_mask.py               # ③c (v2.0·D22) PII 脱敏+血缘+审计追踪
    ├── audit_materiality.py      # ③d (v2.0·D21) 重要性水平+ISA 清单
    ├── industry_benchmark.py     # ③e (v2.0·D17) 行业对标评级
    ├── core_profit.py            # ③f (v2.0·D20) 张氏核心利润/获现率
    ├── dupont_altman.py          # ③g (v2.0·D16) 杜邦+Altman Z
    ├── build_dashboard.py        # ④ 生成自包含 dashboard.html（11 页签 + 钻取）
    ├── build_ppt.py              # ⑤ 生成专业中文 PPT（10 页）
    ├── build_pdf.py              # ⑥ 生成 A4 正式 PDF（九章）
    ├── build_report_md.py        # ⑥b 生成会计报告 MD（十三章）
    ├── build_docx.py             # ⑥c (v3.0) 生成 Word 报告（13 节）
    ├── audit_workpapers.py       # ⑥d (v3.0) 生成 9 表审计底稿 Excel
    ├── validate_skill.py         # ⑦ 综合校验台（--loops N / --core）
    ├── validate_dash.js          # ④辅助：Node 桩校验主脚本 + 钻取函数
    ├── probe_details.py          # 辅助：打印源文件 sheet 结构
    └── assets/
        └── chart.umd.min.js      # Chart.js 4.4.1 本地版（内联进 HTML）
```

---

## 3. 环境依赖

- **Python 3.12+**，建议使用虚拟环境（已在 `analytics` 环境验证）：
  `pandas`、`openpyxl`、`python-pptx`、`reportlab`、`matplotlib`、`python-docx`、`pdfplumber`
- **Node.js**（仅 `validate_dash.js` 钻取校验需要）：`node`
- **中文字体**：Windows 自带 `微软雅黑`（PDF 用 `C:\Windows\Fonts\msyh.ttc` / `msyhbd.ttc`）。
- 安装命令示例：
  ```bash
  python -m venv analytics && analytics\Scripts\activate
  pip install pandas openpyxl python-pptx reportlab matplotlib python-docx pdfplumber
  ```

---

## 4. 快速开始

```bash
# 1) 把 scripts/ 拷到项目目录，按 scripts/00_QUICKSTART.md 改各脚本顶部 CONFIG
# 2) 顺序运行（均在 scripts/ 目录、用同一 Python venv）：
python extract.py            # ①
python augment.py            # ②
python augment_details.py    # ③
python fraud_check.py        # ③b D19 舞弊校验
python pii_mask.py           # ③c D22 数据安全
python audit_materiality.py  # ③d D21 重要性
python industry_benchmark.py # ③e D17 行业对标
python core_profit.py        # ③f D20 核心利润
python dupont_altman.py      # ③g D16 杜邦/Altman
python build_dashboard.py    # ④ 看板（9 页签）
python build_ppt.py          # ⑤ PPT
python build_pdf.py          # ⑥ PDF
python build_report_md.py    # ⑥b MD 报告
python build_docx.py         # ⑥c Word 报告
python audit_workpapers.py   # ⑥d 9 表审计底稿
python validate_skill.py --loops 100 --skip-extract --core   # ⑦ 快回归
python validate_skill.py --loops 100 --skip-extract          # ⑦ 全产物回归
```

产出位于 `CONFIG.output_dir`：`dashboard.html`、`财务经营分析报告.pptx`、`.pdf`、`.docx`、`审计底稿_9表.xlsx`、`会计分析报告_顶级团队.md`、`data.json`。

---

## 5. 合规与免责

- 本技能定位为**管理用经营分析 / 管理建议书**，依据《企业会计准则》口径编制。
- **不构成法定审计报告**；正式审计意见应以具备资质的会计师事务所出具为准。
- 所有计提比例、阈值与红旗为**行业常见保守口径**，正式场景须以被审计单位会计政策与企业会计准则具体应用指南为准。
- 每产物均附**数据质量声明**，列明资料覆盖范围、缺失项与假设。

详见 [`SKILL.md`](../SKILL.md)、[`准则对照表.md`](./准则对照表.md)、[`使用手册.md`](./使用手册.md)。
