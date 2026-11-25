# OpenPackagedFood データリポジトリ (`opf-data`)

**[English](README.md) | [简体中文](README.zh-CN.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [Español](README.es.md) | [Italiano](README.it.md)** · **[🌐 Web エクスプローラー & 静的 API](https://mickey996icu.github.io/opf-data/)** · **[🖼️ ラベル画像倉 (`opf-media`)](https://github.com/mickey996icu/opf-media)** · **[🧰 CLI・仕様・Agent Skill (`open-packaged-food`)](https://github.com/mickey996icu/open-packaged-food)**

本リポジトリは **OpenPackagedFood (`opf` v0.2)** の公式オープンデータベースです。ディレクトリ構造化された商品 JSON レコード（`products/`）、公的レジストリ補正オーバーレイ（`overlays/`）、GitHub Pages 上の認証不要静的 API（`/v1/`）および 7 言語対応 Web エクスプローラーを提供します。ラベルの切り抜き画像バイナリは [`opf-media`](https://github.com/mickey996icu/opf-media) に分離保管され、`opf-media://sha256/<hex>` で参照されます。

> **クリーン初期化 (`products/.gitkeep`)**: 本リポジトリは `0` 件のダミーなし初期状態で公開されており、コミュニティによる実物ラベル写真または CC0 公的レジストリ裏付けのある実在データのみを受け入れます。

---

## 認証不要 静的 API (`/v1/`) クイックリファレンス

- **GTIN-14 ルックアップ**: 14 桁ゼロ埋め (`0000000000017` → `00000000000017`)、`p1 = gtin14[1:4]`、`p2 = gtin14[4:7]`
  `GET https://mickey996icu.github.io/opf-data/v1/gtin/000/000/00000000000017.json`
- **ブランド別インデックス**: `/v1/index/brands.json` → `/v1/index/brand/{brand_slug}.json`
- **全量行指向インデックス**: `/v1/index/catalog.jsonl` / **メタデータ**: `/v1/meta.json`
- **4 種の `resolution`**: `single` / `uniform` / `divergent`（トップレベル `normalized` 省略、比較のみ可） / `inactive`

---

## コントリビュート方法（第 1 段階：開発者純 Git 主経路）

> Web UI は一般ユーザー向けの読み取り専用検索・静的バーコードスキャン専用です。一般ユーザーの投稿は第 2 段階（`OPF_SUBMIT_URL`）で Agent 経由により公開予定であり、第 1 段階は開発者向け純 Git PR のみ受け付けています。

1. **開発者向け純 Git デュアル PR（主経路）**:
   ```bash
   git clone https://github.com/mickey996icu/open-packaged-food.git
   git clone https://github.com/mickey996icu/opf-data.git
   git clone https://github.com/mickey996icu/opf-media.git
   ./open-packaged-food/bin/opf submit --input draft.json --out-dir opf-data/products \
     --photo nutrition_table=crop_nutrition.jpg --photo ingredients_box=crop_ingredients.jpg \
     --vintage-month 2026-08 --media-out-dir opf-media
   ./open-packaged-food/bin/opf validate opf-data/products
   ```
   > ⚠️ 各リポジトリでブランチを作成して `git commit -s`・`git push` 後、**`opf-media` PR を必ず先にマージ**し、続いて `opf-data` PR をマージしてください（`opf check-media-refs` CI チェックのため）。
2. **任意のワンステップ自動化 (`uvx` + `gh`)**：
   `uvx --from git+https://github.com/mickey996icu/open-packaged-food@v0.2.6 opf pr --input draft.json --photo ...` もオプションで利用できます（外部 PR の `provenance` は自動で `"external_contributor"` になります）。詳細は [CONTRIBUTING.md](https://github.com/mickey996icu/open-packaged-food/blob/main/CONTRIBUTING.md)、[ODbL ガイド](https://github.com/mickey996icu/open-packaged-food/blob/main/docs/ODbL-GUIDE.md) および [データ限界事項](https://github.com/mickey996icu/open-packaged-food/blob/main/docs/DATA-LIMITATIONS.md) をご参照ください。
