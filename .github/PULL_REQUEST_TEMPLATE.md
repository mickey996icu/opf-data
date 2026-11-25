## 变更类型 / Change Type

- [ ] 🍱 新增商品 / New product (`new-product`)
- [ ] 🔄 现有商品新包装/新配方版本 / New edition (`new-edition`)
- [ ] ✏️ 现有版本字段补全或勘误 / Attribute enrichment or errata (`enrichment / errata`)
- [ ] 📚 法规插件 / 添加剂词典 / 工具链更新 / Spec, profile, or tooling update (`spec / tooling`)

## 自检清单 / Pre-Merge Checklist

- [ ] **本地校验与审计 / Validation & Audit**：已运行 `opf validate` 与 `opf audit --media-dir` 且无 `ERROR`（GS1 校验位、配料树无损还原 round-trip 校验、确定性百克换算、`@rev` 双向补丁、SC/厂址及营养逻辑一致）。 / Passed `opf validate` & `opf audit --media-dir` with zero errors (GS1 check digit, lossless ingredient round-trip, deterministic normalization, bidirectional patch replay, and SC/address/nutrition consistency).
- [ ] **双仓媒体门禁与顺序 / Media Gate & Merge Order**：已确认 `opf check-media-refs` 无孤立 `opf-media://` 引用；若同时伴随媒体 PR，**先合入 `opf-media` PR，再合入 `opf-data` PR**。 / Confirmed `opf check-media-refs` passes; if a companion `opf-media` PR exists, **merge `opf-media` FIRST**, then **`opf-data` SECOND**.
- [ ] **功能裁剪、逐字依图与隐私门禁 / Functional-Crop, Verbatim Evidence & Privacy Gate**：仅上传清晰可读的功能性标签文字裁剪图（7 类标准 `region` 名副其实，严禁整盒正面封面大图），`claims_text`/配料/标准/厂家与裁剪图逐字对应无无据改写，且 JSON 与图片均**不包含**购买日期、价格、精确到日的生产/到期批号、人脸、手部或订单小票。 / Legible cropped functional label regions only (accurate `region`, no full front-of-pack cover art; verbatim alignment for claims/ingredients/standards/parties; zero purchase dates, prices, day-precision lot/expiry dates, faces, fingers, or receipts).
- [ ] **原创与许可声明 / DCO & Open Licenses**：数据与图片合法采集整理，结构化数据遵循 `ODbL-1.0`，裁剪标签图遵循 `CC BY-SA 4.0`（上游工具链与规范按 `Apache-2.0` 入站，详见 [REUSE.toml](https://github.com/mickey996icu/open-packaged-food/blob/main/REUSE.toml) 与 [ODbL 指南](https://github.com/mickey996icu/open-packaged-food/blob/main/docs/ODbL-GUIDE.zh-CN.md)）。 / Confirm DCO: data under `ODbL-1.0`, cropped label media under `CC BY-SA 4.0` (tooling/spec inbound under `Apache-2.0`).
- [ ] **双层来源标注与防洗白 / Provenance Compliance**：外部 PR 新增的 `evidence[]` 与 `revisions[]` 均已标注 `provenance: "external_contributor"`，且未擅自改动既有条目的 `provenance`。 / New `evidence[]` and `revisions[]` entries carry `provenance: "external_contributor"` and existing `provenance` tags remain unaltered.
