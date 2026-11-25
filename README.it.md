# Dati OpenPackagedFood (`opf-data`)

**[English](README.md) | [简体中文](README.zh-CN.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [Español](README.es.md) | [Italiano](README.it.md)** · **[🌐 Web Explorer & API statica](https://mickey996icu.github.io/opf-data/)** · **[🖼️ Archivio etichette (`opf-media`)](https://github.com/mickey996icu/opf-media)** · **[🧰 Strumenti e Agent Skill (`open-packaged-food`)](https://github.com/mickey996icu/open-packaged-food)**

Questo repository è il database aperto canonico di **OpenPackagedFood (`opf` v0.2)**: contiene schede prodotto JSON verificate (`products/`), correzioni da registri ufficiali (`overlays/`), l'API statica `/v1/` senza autenticazione su GitHub Pages e il Web Explorer in 7 lingue. I ritagli fotografici delle etichette risiedono separatamente in [`opf-media`](https://github.com/mickey996icu/opf-media) (`opf-media://sha256/<hex>`).

> **Inizializzazione pulita (`products/.gitkeep`)**: `opf-data` parte pulito con `0` record fittizi e accetta esclusivamente prodotti reali supportati da ritagli d'etichetta o registri pubblici CC0.

---

## Riferimento rapido API statica (`/v1/`)

- **Ricerca per GTIN-14**: completare con zeri a sinistra (`0000000000017` → `00000000000017`), shard `p1 = gtin14[1:4]`, `p2 = gtin14[4:7]`:
  `GET https://mickey996icu.github.io/opf-data/v1/gtin/000/000/00000000000017.json`
- **Elenco marche**: `/v1/index/brands.json` → `/v1/index/brand/{brand_slug}.json` (con `brand_names` multilingue)
- **Catalogo JSONL**: `/v1/index/catalog.jsonl` · **Metadati**: `/v1/meta.json`
- **Quattro modalità `resolution`**: `single` / `uniform` / `divergent` (`normalized` omesso al livello principale) / `inactive`

---

## Come contribuire (Fase 1: flusso Git puro per sviluppatori)

> Il Web Explorer è destinato ai consumatori per la consultazione in sola lettura e la scansione locale; l'invio da parte degli utenti finali aprirà nella Fase 2 (`OPF_SUBMIT_URL`) tramite Agenti. La Fase 1 accetta solo contributi di sviluppatori via Git puro.

1. **Percorso principale sviluppatori (Git puro e doppia PR)**:
   ```bash
   git clone https://github.com/mickey996icu/open-packaged-food.git
   git clone https://github.com/mickey996icu/opf-data.git
   git clone https://github.com/mickey996icu/opf-media.git
   ./open-packaged-food/bin/opf submit --input draft.json --out-dir opf-data/products \
     --photo nutrition_table=crop_nutrition.jpg --photo ingredients_box=crop_ingredients.jpg \
     --vintage-month 2026-08 --media-out-dir opf-media
   ./open-packaged-food/bin/opf validate opf-data/products
   ```
   > ⚠️ **Ordine di merge obbligatorio**: dopo `git commit -s` e `git push` in entrambi i repo, **eseguire il merge prima sulla PR `opf-media`**, poi sulla PR `opf-data` (`opf check-media-refs`).
2. **Automazione opzionale (`uvx` + `gh`)**:
   `uvx --from git+https://github.com/mickey996icu/open-packaged-food@v0.2.6 opf pr --input draft.json --photo ...` resta disponibile come scorciatoia opzionale (le PR esterne impostano automaticamente `provenance: "external_contributor"`). Vedi [CONTRIBUTING.md](https://github.com/mickey996icu/open-packaged-food/blob/main/CONTRIBUTING.md), [Guida ODbL](https://github.com/mickey996icu/open-packaged-food/blob/main/docs/ODbL-GUIDE.md) e [Limiti dei dati](https://github.com/mickey996icu/open-packaged-food/blob/main/docs/DATA-LIMITATIONS.md).
