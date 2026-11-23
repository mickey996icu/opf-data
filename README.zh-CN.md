# OpenPackagedFood 数据仓库（`opf-data`）

**[English](README.md) | [简体中文](README.zh-CN.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [Español](README.es.md) | [Italiano](README.it.md)** · **[🌐 在线检索网页与复杂筛选台](https://mickey996icu.github.io/opf-data/)** · **[🖼️ 标签图片媒体仓 (`opf-media`)](https://github.com/mickey996icu/opf-media)** · **[🧰 规范、CLI 工具链与 Agent Skill (`open-packaged-food`)](https://github.com/mickey996icu/open-packaged-food)**

本仓库是 **OpenPackagedFood (`opf` v0.2)** 的官方开放数据库仓库，负责托管按目录拆分的经核验工业预包装食品 JSON 记录（`products/`）、官方数据源勘误补丁（`overlays/`），以及由 GitHub Actions 自动编译发布的零鉴权静态 API（`/v1/`）与中英双语检索网页。所有标签裁剪证据图均隔离托管于独立的 [`opf-media`](https://github.com/mickey996icu/opf-media) 仓库（通过 `opf-media://sha256/<hex>` 逻辑 URI 解耦，避免 Git 历史膨胀）。

> **零虚构纯净初始化（`products/.gitkeep`）**：`opf-data` 仓库以 `0` 条占位记录纯净初始化，不预先填充任何无实拍佐证的商业品牌数据；`products/` 下仅接纳经实拍标签裁剪图或 CC0 官方开放库核验的真实商品投稿。

---

## 在线入口与三仓库体系

- **在线检索网页与静态 API 根地址**：[https://mickey996icu.github.io/opf-data/](https://mickey996icu.github.io/opf-data/)（面向普通用户的只读检索与纯静态本地扫码网页，支持 7 语与浅色/深色主题）
- **配套媒体仓（裁剪 WebP 证据图）**：[`mickey996icu/opf-media`](https://github.com/mickey996icu/opf-media)
- **规范、CLI 工具链与 AI Agent Skill**：[`mickey996icu/open-packaged-food`](https://github.com/mickey996icu/open-packaged-food)
- **开源许可协议**：[**ODbL-1.0**（数据库）/ **DbCL-1.0**（内容）](LICENSE-DATA) · [**CC BY-SA 4.0**（裁剪标签证据图）](LICENSE-MEDIA)

---

## 零鉴权静态 API（`/v1/`）速查手册

所有接口均由 GitHub Pages CDN 纯静态托管，无需 API Key，单次 HTTP `GET` 即可读取：

### 1. 条形码（GTIN-8 / 12 / 13 / 14）分片规则与 `curl` 示例

1. 将商品条形码左侧补 `0` 至 **14 位**（`gtin14`，例如 `0000000000017` $\rightarrow$ `00000000000017`）；
2. 取两位分片索引：`p1 = gtin14[1:4]`（即 `000`），`p2 = gtin14[4:7]`（即 `000`）；
3. 直接请求 **Compact 端点** `/v1/gtin/{p1}/{p2}/{gtin14}.json`：

```bash
curl -s https://mickey996icu.github.io/opf-data/v1/gtin/000/000/00000000000017.json
```

### 2. Compact API 四态消歧返回值（`resolution`）

| `resolution` | 含义 | 数据使用规则 |
| :--- | :--- | :--- |
| `"single"` | 当前仅 1 个有效版本在售 | 直接读取顶层 `normalized`、`ingredients_text` 与 `evidence_summary.highest_active`。 |
| `"uniform"` | 多个轨道并存但 `core_fingerprint` 完全一致 | 顶层 `normalized` 适用于全部 `active_tracks`；`origins_summary` / `parties_summary` 汇总全部合法产地与工厂。 |
| `"divergent"` | 在售版本存在成分/营养实质差异 | **顶层故意省略 `normalized` 与 `ingredients_text`**；严禁用区间/均值入账，必须按 `disambiguation.tracks_guide[]` 对照包装选定唯一 `variants[]` 版本。 |
| `"inactive"` | 当前无有效在售版本 | `active_edition_ids` 为 `[]`（仅存 `discontinued` / `stale` / `superseded` / `retracted` 历史记录）。 |

若存在并发合并跳转（`edition_redirects`），可按 `<old_ulid>` 查找对应的规范 `<canonical_ulid>`。

### 3. 完整历史与索引端点

- **全局元数据与统计**：`/v1/meta.json`（含 `opf_schema: "0.2"`、`media_base_url`、`counts_by_resolution`、`counts_by_evidence_level`、`counts_by_market`）
- **完整版商品文档（`.full.json`）**：`/v1/gtin/{p1}/{p2}/{gtin14}.full.json`（包含原始 `core.nutrition_declared`、带网页高亮区间的 `ingredients_tree_annotated`、`evidence[]` 只增不改佐证明细，以及供 `<edition_id>@<rev>` 重建的双向 RFC 6902 `revisions[]`）
- **无全球条码 pre-packaged 商品（`opf:`）**：`/v1/opf/{brand}/{product}/{spec}.json` 与 `.full.json`
- **品牌索引目录**：`/v1/index/brands.json` $\rightarrow$ `/v1/index/brand/{brand_slug}.json`
- **全量行式检索索引**：`/v1/index/catalog.jsonl`

---

## 如何贡献（第一阶段纯 Git 开发者主路径）

> **普通用户与开发者边界**：
> - [在线检索网页](https://mickey996icu.github.io/opf-data/)仅面向普通消费者提供**只读检索与纯前端本地扫码查询**；
> - 面向普通用户的投稿将在**第二阶段**部署 `OPF_SUBMIT_URL` HTTP Relay 后经由 AI Agent 开放；
> - **第一阶段仅开放开发者贡献通道**，统一走纯 Git 三仓 PR 协作流程（详见 [`CONTRIBUTING.zh-CN.md`](https://github.com/mickey996icu/open-packaged-food/blob/main/CONTRIBUTING.zh-CN.md)）。

### 主路径：纯 Git 双仓 PR 流程

1. **克隆三仓**（`open-packaged-food`、`opf-data`、`opf-media`）：
   ```bash
   git clone https://github.com/mickey996icu/open-packaged-food.git
   git clone https://github.com/mickey996icu/opf-data.git
   git clone https://github.com/mickey996icu/opf-media.git
   ```
2. **本地生成去隐私 WebP（`<= 1000px` 无 EXIF）与产品 JSON 并完成校验**：
   ```bash
   ./open-packaged-food/bin/opf submit \
     --input draft.json \
     --out-dir opf-data/products \
     --photo nutrition_table=crop_nutrition.jpg \
     --photo ingredients_box=crop_ingredients.jpg \
     --vintage-month 2026-08 \
     --media-out-dir opf-media
   ./open-packaged-food/bin/opf validate opf-data/products
   ```
3. **签名提交并推送双仓分支，点击终端提示的 GitHub 链接创建双 PR**：
   ```bash
   git -C opf-media switch -c contrib/media-label && git -C opf-media add -A && git -C opf-media commit -s -m "media(label): add cropped label evidence" && git -C opf-media push -u origin HEAD
   git -C opf-data switch -c contrib/product-record && git -C opf-data add -A && git -C opf-data commit -s -m "feat(product): add packaged food record" && git -C opf-data push -u origin HEAD
   ```

> ⚠️ **双仓库 PR 合入顺序红线**：本仓 CI 会运行 `opf check-media-refs` 检查 `evidence[].media_ref`。当同时产生媒体 PR 与数据 PR 时，**必须先合入 `opf-media` 的 Companion Media PR**，确认图片进入 `opf-media/main` 后再合入 `opf-data` PR！

### 可选一键自动化通道（`uvx` + `gh`）

已安装 `uvx` 且完成 `gh auth login` 的开发者，也可选用 `opf pr` 一键自动化提交双 PR：

```bash
uvx --from git+https://github.com/mickey996icu/open-packaged-food@v0.2.0 \
  opf pr --input draft.json \
  --photo nutrition_table=crop_nutrition.jpg \
  --photo ingredients_box=crop_ingredients.jpg \
  --vintage-month 2026-08
```

---

## 目录存储结构（`products/`，Schema `0.2`）

```text
opf-data/
├── products/
│   ├── gtin/{p1}/{p2}/{gtin14}/
│   │   ├── product.json          # 商品别名与非默认 Track 区分指引（tracks[]）
│   │   └── editions/<ulid>.json  # 永久 ULID EditionRecord 或 EditionRedirect 跳转桩
│   └── opf/{brand}/{product}/{spec}/
│       ├── product.json
│       └── editions/<ulid>.json
├── overlays/                     # 官方数据源勘误补丁
└── .github/
    ├── ISSUE_TEMPLATE/           # 数据勘误与官方公开数据库导入提案模板
    └── workflows/                # CI 校验 (validate + check-media-refs) 与 GitHub Pages 构建部署
```
