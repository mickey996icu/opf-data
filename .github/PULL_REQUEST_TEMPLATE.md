## 变更类型 / Change Type

- [ ] 🍱 新增商品 / New product (`new-product`)
- [ ] 🔄 现有商品新包装/新配方版本 / New edition (`new-edition`)
- [ ] ✏️ 现有版本字段补全或勘误 / Attribute enrichment or errata (`enrichment / errata`)
- [ ] 📚 法规插件 / 添加剂词典 / 工具链更新 / Spec, profile, or tooling update (`spec / tooling`)

## 自检清单 / Pre-Merge Checklist

- [ ] **本地校验 / Validation**：已运行 `opf validate` 且全部通过（GS1 校验位、配料树无损还原 round-trip 校验、确定性百克换算与 `@rev` 双向补丁一致）。 / Passed `opf validate` (GS1 check digit, lossless ingredient round-trip, deterministic normalization, bidirectional patch replay).
- [ ] **双仓媒体门禁与顺序 / Media Gate & Merge Order**：已确认 `opf check-media-refs` 无孤立 `opf-media://` 引用；若同时伴随媒体 PR，**先合入 `opf-media` PR，再合入 `opf-data` PR**。 / Confirmed `opf check-media-refs` passes; if a companion `opf-media` PR exists, **merge `opf-media` FIRST**, then **`opf-data` SECOND**.
- [ ] **功能裁剪与隐私门禁 / Functional-Crop & Privacy Gate**：仅上传功能性标签文字裁剪图（7 类标准 `region`，严禁整盒正面封面大图），且 JSON 与图片均**不包含**购买日期、价格、精确到日的生产/到期批号、人脸、手部或订单小票。 / Cropped functional label regions only (no full front-of-pack cover art; zero purchase dates, prices, day-precision lot/expiry dates, faces, fingers, or receipts).
- [ ] **原创与许可声明 / DCO & Open Licenses**：数据与图片合法采集整理，代码遵循 `AGPL-3.0-or-later`，结构化数据遵循 `ODbL-1.0`，裁剪标签图遵循 `CC BY-SA 4.0`。 / Confirm DCO: code under `AGPL-3.0-or-later`, data under `ODbL-1.0`, cropped label media under `CC BY-SA 4.0`.
