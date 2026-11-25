# Datos de OpenPackagedFood (`opf-data`)

**[English](README.md) | [简体中文](README.zh-CN.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [Español](README.es.md) | [Italiano](README.it.md)** · **[🌐 Explorador Web y API estática](https://mickey996icu.github.io/opf-data/)** · **[🖼️ Imágenes de etiquetas (`opf-media`)](https://github.com/mickey996icu/opf-media)** · **[🧰 Herramientas y Skill IA (`open-packaged-food`)](https://github.com/mickey996icu/open-packaged-food)**

Este repositorio es la base de datos abierta canónica de **OpenPackagedFood (`opf` v0.2)**, que aloja registros JSON verificados de alimentos envasados (`products/`), superposiciones de registros oficiales (`overlays/`), la API estática `/v1/` en GitHub Pages sin autenticación y el explorador web en 7 idiomas. Las imágenes recortadas se guardan aparte en [`opf-media`](https://github.com/mickey996icu/opf-media) (`opf-media://sha256/<hex>`).

> **Inicio limpio (`products/.gitkeep`)**: `opf-data` se inicializa sin registros ficticios (`0` productos) y solo admite aportaciones reales respaldadas por recortes de etiquetas o registros públicos CC0.

---

## Referencia rápida de la API estática (`/v1/`)

- **Consulta por GTIN-14**: rellenar con ceros a la izquierda (`0000000000017` → `00000000000017`), particiones `p1 = gtin14[1:4]`, `p2 = gtin14[4:7]`:
  `GET https://mickey996icu.github.io/opf-data/v1/gtin/000/000/00000000000017.json`
- **Directorio por marca**: `/v1/index/brands.json` → `/v1/index/brand/{brand_slug}.json` (incluye `brand_names` multilingües)
- **Catálogo JSONL**: `/v1/index/catalog.jsonl` · **Metadatos**: `/v1/meta.json`
- **Cuatro modos `resolution`**: `single` / `uniform` / `divergent` (sin `normalized` en la raíz) / `inactive`

---

## Cómo contribuir (Fase 1: flujo Git puro para desarrolladores)

> El explorador web está orientado a la consulta de solo lectura y escaneo local para consumidores; los envíos de usuarios generales abrirán en la Fase 2 (`OPF_SUBMIT_URL`) mediante Agentes. La Fase 1 está abierta únicamente a desarrolladores por Git puro.

1. **Vía principal para desarrolladores (Git puro y PR doble)**:
   ```bash
   git clone https://github.com/mickey996icu/open-packaged-food.git
   git clone https://github.com/mickey996icu/opf-data.git
   git clone https://github.com/mickey996icu/opf-media.git
   ./open-packaged-food/bin/opf submit --input draft.json --out-dir opf-data/products \
     --photo nutrition_table=crop_nutrition.jpg --photo ingredients_box=crop_ingredients.jpg \
     --vintage-month 2026-08 --media-out-dir opf-media
   ./open-packaged-food/bin/opf validate opf-data/products
   ```
   > ⚠️ **Orden estricto de fusión**: tras `git commit -s` y `git push` en ambos repos, **fusione primero la PR de `opf-media`** y después la PR de `opf-data` (`opf check-media-refs`).
2. **Automatización opcional (`uvx` + `gh`)**:
   `uvx --from git+https://github.com/mickey996icu/open-packaged-food@v0.2.6 opf pr --input draft.json --photo ...` sigue disponible como atajo opcional (los PR externos asignan `provenance: "external_contributor"` por defecto). Consulte [CONTRIBUTING.md](https://github.com/mickey996icu/open-packaged-food/blob/main/CONTRIBUTING.md), [Guía ODbL](https://github.com/mickey996icu/open-packaged-food/blob/main/docs/ODbL-GUIDE.md) y [Limitaciones de datos](https://github.com/mickey996icu/open-packaged-food/blob/main/docs/DATA-LIMITATIONS.md).
