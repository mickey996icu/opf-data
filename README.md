# OpenPackagedFood Data (`opf-data`)

**[English](README.md) | [简体中文](README.zh-CN.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [Español](README.es.md) | [Italiano](README.it.md)** · **[🌐 Live Web Explorer & Static API](https://mickey996icu.github.io/opf-data/)** · **[🖼️ Label Media Store (`opf-media`)](https://github.com/mickey996icu/opf-media)** · **[🧰 Tooling, Spec & Agent Skill (`open-packaged-food`)](https://github.com/mickey996icu/open-packaged-food)**

This repository is the canonical open database for **OpenPackagedFood (`opf` v0.2)**, hosting directory-structured, verified industrial packaged food JSON records (`products/`), official registry errata overlays (`overlays/`), and the automated GitHub Pages zero-auth static API (`/v1/`) and bilingual web database explorer. Cropped label evidence binaries live in the separate [`opf-media`](https://github.com/mickey996icu/opf-media) repository and are referenced solely via location-independent `opf-media://sha256/<hex>` URIs.

> **Clean Initialization (`products/.gitkeep`)**: `opf-data` initializes cleanly with `0` pre-populated placeholder records and never ships synthetic or unphotographed commercial seed data; only authentic community-verified submissions (or CC0 official registry imports) are admitted under `products/`.

---

## Live Endpoints & Three-Repository Ecosystem

- **Online Web Explorer & Static API Root**: [https://mickey996icu.github.io/opf-data/](https://mickey996icu.github.io/opf-data/) (read-only 7-language explorer with pure-static client-side barcode/QR scanning, Light & Dark themes)
- **Cropped Label Media Store**: [`mickey996icu/opf-media`](https://github.com/mickey996icu/opf-media)
- **Specification, CLI & Agent Skill Repository**: [`mickey996icu/open-packaged-food`](https://github.com/mickey996icu/open-packaged-food)
- **Licensing**: [**ODbL-1.0** (Database) / **DbCL-1.0** (Contents)](LICENSE-DATA) · [**CC BY-SA 4.0** (Cropped Label Evidence Media)](LICENSE-MEDIA)

---

## Zero-Auth Static API (`/v1/`) Quick Reference

Every endpoint is compiled and served statically via GitHub Pages CDN—no API keys required, readable with a single HTTP `GET`:

### 1. Barcode (GTIN-8 / 12 / 13 / 14) Sharding & `curl` Example

1. Left-pad the barcode with zeros to **14 digits** (`gtin14`, e.g., `0000000000017` $\rightarrow$ `00000000000017`).
2. Compute shard directories: `p1 = gtin14[1:4]` (`000`) and `p2 = gtin14[4:7]` (`000`).
3. Fetch the **Compact** endpoint `/v1/gtin/{p1}/{p2}/{gtin14}.json`:

```bash
curl -s https://mickey996icu.github.io/opf-data/v1/gtin/000/000/00000000000017.json
```

### 2. Four Compact API Resolution Modes (`resolution`)

| `resolution` | Meaning | How to Consume |
| :--- | :--- | :--- |
| `"single"` | Exactly one active edition exists | Read top-level `normalized`, `ingredients_text`, and `evidence_summary.highest_active` directly. |
| `"uniform"` | Multiple active tracks share an identical `core_fingerprint` | Top-level `normalized` applies across all `active_tracks`; read `origins_summary` and `parties_summary` for all valid plants. |
| `"divergent"` | Coexisting active variants differ in recipe or nutrition | **Top-level `normalized` and `ingredients_text` are intentionally omitted**; never log ranges/averages—disambiguate via `disambiguation.tracks_guide[]` and pick one concrete edition from `variants[]`. |
| `"inactive"` | No active market edition remains | `active_edition_ids` is `[]` (only `discontinued`, `stale`, `superseded`, or `retracted` editions exist). |

If duplicate concurrent submissions were consolidated, consult `edition_redirects` (`<old_ulid>` $\rightarrow$ `<canonical_ulid>`).

### 3. Full History & Index Endpoints

- **Metadata & Counts**: `/v1/meta.json` (`opf_schema: "0.2"`, `media_base_url`, `counts_by_resolution`, `counts_by_evidence_level`, `counts_by_market`)
- **Full Product Document (`.full.json`)**: `/v1/gtin/{p1}/{p2}/{gtin14}.full.json` (verbatim `core.nutrition_declared`, UI highlight `ingredients_tree_annotated`, append-only `evidence[]`, and bidirectional RFC 6902 `revisions[]` supporting `<edition_id>@<rev>`)
- **Non-GTIN Pre-Packaged Lookup (`opf:`)**: `/v1/opf/{brand}/{product}/{spec}.json` (and `.full.json`)
- **Brand Directory**: `/v1/index/brands.json` $\rightarrow$ `/v1/index/brand/{brand_slug}.json`
- **Full JSONL Catalog**: `/v1/index/catalog.jsonl`

---

## How to Contribute (Phase 1 Pure-Git Developer Workflow)

> **User vs. Developer Boundary**:
> - The [Web Explorer](https://mickey996icu.github.io/opf-data/) is a **read-only lookup & local barcode/QR scanner** for everyday shoppers.
> - Everyday end-user submissions open in **Phase 2** via AI Agents once `OPF_SUBMIT_URL` HTTP Relay is enabled.
> - **Phase 1 accepts developer contributions only** via the standard three-repo pure-Git workflow (see [`CONTRIBUTING.md`](https://github.com/mickey996icu/open-packaged-food/blob/main/CONTRIBUTING.md)).

### Primary Path: Pure-Git Dual-PR Flow

1. **Clone the three repositories** (`open-packaged-food`, `opf-data`, `opf-media`):
   ```bash
   git clone https://github.com/mickey996icu/open-packaged-food.git
   git clone https://github.com/mickey996icu/opf-data.git
   git clone https://github.com/mickey996icu/opf-media.git
   ```
2. **Transcode functional label crops (`<= 1000px` EXIF-free WebP) & validate**:
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
3. **Commit (`git commit -s`), push both branches, and open PRs from the terminal links**:
   ```bash
   git -C opf-media switch -c contrib/media-label && git -C opf-media add -A && git -C opf-media commit -s -m "media(label): add cropped label evidence" && git -C opf-media push -u origin HEAD
   git -C opf-data switch -c contrib/product-record && git -C opf-data add -A && git -C opf-data commit -s -m "feat(product): add packaged food record" && git -C opf-data push -u origin HEAD
   ```

> ⚠️ **Mandatory Dual-PR Merge Order**: This repo's CI runs `opf check-media-refs` against `opf-media/main`. Whenever a submission creates both media and data PRs, **merge the companion `opf-media` PR FIRST**, then merge the **`opf-data` PR SECOND**.

### Optional One-Step Automation (`uvx` + `gh`)

Developers who already have `uvx` and an authenticated `gh` session (`gh auth login`) may optionally run `opf pr` instead of manual git branching/pushing:

```bash
uvx --from git+https://github.com/mickey996icu/open-packaged-food@v0.2.4 \
  opf pr --input draft.json \
  --photo nutrition_table=crop_nutrition.jpg \
  --photo ingredients_box=crop_ingredients.jpg \
  --vintage-month 2026-08
```

---

## Source Directory Layout (`products/`, Schema `0.2`)

```text
opf-data/
├── products/
│   ├── gtin/{p1}/{p2}/{gtin14}/
│   │   ├── product.json          # Search aliases & non-default TrackDefinition discriminators
│   │   └── editions/<ulid>.json  # Permanent ULID EditionRecord or EditionRedirect stub
│   └── opf/{brand}/{product}/{spec}/
│       ├── product.json
│       └── editions/<ulid>.json
├── overlays/                     # Upstream registry errata overlays
└── .github/
    ├── ISSUE_TEMPLATE/           # Data errata & official registry import proposals
    └── workflows/                # CI checks (validate + check-media-refs) & Pages deploy
```
