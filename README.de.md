# OpenPackagedFood Datenbank (`opf-data`)

**[English](README.md) | [简体中文](README.zh-CN.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [Español](README.es.md) | [Italiano](README.it.md)** · **[🌐 Web-Explorer & Statische API](https://mickey996icu.github.io/opf-data/)** · **[🖼️ Etikett-Medienspeicher (`opf-media`)](https://github.com/mickey996icu/opf-media)** · **[🧰 CLI, Spezifikation & Agent Skill (`open-packaged-food`)](https://github.com/mickey996icu/open-packaged-food)**

Dieses Repository ist die zentrale offene Datenbank für **OpenPackagedFood (`opf` v0.2)** mit verifizierten JSON-Produktdatensätzen (`products/`), Register-Korrekturoverlays (`overlays/`), der automatisierten statischen `/v1/`-API auf GitHub Pages und dem 7-sprachigen Web-Explorer. Zugeschnittene Etikettfotos liegen getrennt in [`opf-media`](https://github.com/mickey996icu/opf-media) (`opf-media://sha256/<hex>`).

> **Saubere Initialisierung (`products/.gitkeep`)**: `opf-data` startet bewusst sauber mit `0` Platzhalterdatensätzen; aufgenommen werden ausschließlich authentische Einreichungen mit zugeschnittenen Etikett-Belegen oder CC0-Behördendaten.

---

## Statische `/v1/`-API ohne Authentifizierung

- **GTIN-14-Abruf**: auf 14 Ziffern auffüllen (`0000000000017` → `00000000000017`), Shards `p1 = gtin14[1:4]`, `p2 = gtin14[4:7]`:
  `GET https://mickey996icu.github.io/opf-data/v1/gtin/000/000/00000000000017.json`
- **Markenverzeichnis**: `/v1/index/brands.json` → `/v1/index/brand/{brand_slug}.json` (mit mehrsprachigen `brand_names`)
- **Gesamtindex**: `/v1/index/catalog.jsonl` · **Metadaten**: `/v1/meta.json`
- **Vier Compact-Modi (`resolution`)**: `single` / `uniform` / `divergent` (`normalized` oben bewusst weggelassen) / `inactive`

---

## Mitwirken (Phase 1: Reiner Git-Entwickler-Workflow)

> Der Web-Explorer dient Endnutzern zur reinen Lese- und lokalen Scan-Abfrage. Einreichungen für Endnutzer öffnen erst in Phase 2 (`OPF_SUBMIT_URL`) über Agenten; Phase 1 richtet sich ausschließlich an Entwickler.

1. **Primärer Entwicklerpfad (Reines Git & Dual-PR)**:
   ```bash
   git clone https://github.com/mickey996icu/open-packaged-food.git
   git clone https://github.com/mickey996icu/opf-data.git
   git clone https://github.com/mickey996icu/opf-media.git
   ./open-packaged-food/bin/opf submit --input draft.json --out-dir opf-data/products \
     --photo nutrition_table=crop_nutrition.jpg --photo ingredients_box=crop_ingredients.jpg \
     --vintage-month 2026-08 --media-out-dir opf-media
   ./open-packaged-food/bin/opf validate opf-data/products
   ```
   > ⚠️ **Zwingende Merge-Reihenfolge**: Nach `git commit -s` & `git push` in beiden Repos **zuerst den `opf-media`-PR mergen**, erst danach den `opf-data`-PR (`opf check-media-refs`).
2. **Optionale Ein-Schritt-Automatisierung (`uvx` + `gh`)**:
   `uvx --from git+https://github.com/mickey996icu/open-packaged-food@v0.2.4 opf pr --input draft.json --photo ...` bleibt als optionaler Shortcut verfügbar (externe PRs setzen `provenance` automatisch auf `"external_contributor"`). Siehe [CONTRIBUTING.md](https://github.com/mickey996icu/open-packaged-food/blob/main/CONTRIBUTING.md) und [ODbL-Leitfaden](https://github.com/mickey996icu/open-packaged-food/blob/main/docs/ODbL-GUIDE.md).
