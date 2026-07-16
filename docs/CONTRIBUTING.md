# 贡献规范（Contributing）

本技能以**插件式扩展**设计，欢迎贡献新维度、新产物与阈值优化。所有变更须满足「可追溯、可校验、可发布」三原则。

---

## 1. 新增一个分析维度（Dn）

1. **数据**：在 `augment.py` 的 `accounting` 段增加 `Dn` 字段（或复用已有数据）。确保写入 `data.json`。
2. **勾稽**：在 `validate_skill.py` 的 `checks()` 中增加该维度的勾稽断言（容忍已知数据残差）。
3. **渲染**：在 `build_dashboard.py` / `build_ppt.py` / `build_pdf.py` 中增加对应展示分支（HTML 页签 / PPT 页 / PDF 章节）。
4. **登记**：在 `SKILL.md` §3 登记该维度的**公式 / 数据源 / 阈值与红旗 / CAS 条款 / 输出字段**，并在 `docs/准则对照表.md` 增加一行。
5. **版本**：在 `docs/CHANGELOG.md` 对应版本登记变更。
6. **校验**：运行 `python validate_skill.py --loops 100 --skip-extract` 确认通过。

> 示例：新增 D13 毛利率，需先接入采购/制造费用表（`extract.py` 增加读取），再在 `augment.py` 计算，最后三处渲染。
> 若数据源暂缺，按 v1.0 做法在 SKILL.md §8 预留位登记 TODO，并在数据质量声明中披露。

---

## 2. 调整阈值 / 计提政策

- 所有阈值集中管理，便于审计复核：
  - 坏账比例：`augment.py` 的 `prov_policy = {"le_1y":0.05,"gt_1y":0.30}`。
  - HHI 分级：`<1500 竞争 / 1500–2500 中度 / >2500 高度`。
  - 风险评分模型：`augment.py` 的 `lvl(score)` 阈值（80/55/35）。
  - DSO / AR 比 / 现金覆盖 等红旗阈值见 `build_*.py` 与 `SKILL.md` §3。
- 调整后必须同步更新 `SKILL.md` §3 与 `docs/准则对照表.md`，并重新运行校验台。
- 阈值变动若影响输出结构（如新增状态列），须升**次版本**。

---

## 3. 新增一个产物（build_xxx.py）

1. 在 `scripts/` 新建 `build_xxx.py`，读取 `data.json`，顶部 `CONFIG` 参数化（输出目录/公司/期间/字体或配色）。
2. 在 `SKILL.md` §5（多格式输出规范）与 §6（脚本表）登记；在 `docs/README.md` 目录结构更新。
3. 在 `validate_skill.py` 的 `pipeline` 列表加入该脚本，并在 `checks()` 增加产物存在性/完整性断言（如 PDF 头 `%PDF-`、PPTX `zip.testzip()`）。
4. 在 `docs/CHANGELOG.md` 登记。
5. 运行 `python validate_skill.py --loops 100 --skip-extract` 确认稳定。

---

## 4. 编码与提交约定

- **路径**：脚本内一律用 `os.path.join` 或 `r"..."`，避免 Git Bash `/c/` 路径传给 native `.exe`（见 SKILL.md §9 坑位 8）。
- **中文字体**：PPT 设 `微软雅黑` 字体名；PDF 注册 `msyh.ttc`/`msyhbd.ttc`（`subfontIndex=0`），避免 Unicode 上下标字形，改用 `<sub>`/`<super>`。
- **幂等**：每脚本可独立重跑，不依赖前次副作用（`augment_details.py` 先备份 `data.json → .bak`）。
- **CONFIG 优先**：项目差异（公司名/源文件/关联方关键词/关注区域）只改顶部 `CONFIG`，不改逻辑代码。
- **提交信息**：遵循 `type(scope): 简述`，如 `feat(dimension): 新增 D13 毛利率`、`fix(validate): D5 容忍银行账残差`。
- **测试门禁**：任何改动须通过 `validate_skill.py --loops 100`（发布前两轮全绿）。

---

## 5. 发布检查清单

- [ ] `docs/CHANGELOG.md` 已更新版本与变更。
- [ ] `SKILL.md` §3 / §8 已登记新维度或预留位。
- [ ] `docs/准则对照表.md` 已同步。
- [ ] `validate_skill.py` 已覆盖新勾稽/产物。
- [ ] `python validate_skill.py --loops 100` 与 `--loops 100 --skip-extract` 均全绿。
- [ ] `LICENSE` 与 `docs/README.md` 完整。
