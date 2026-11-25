# Données OpenPackagedFood (`opf-data`)

**[English](README.md) | [简体中文](README.zh-CN.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [Español](README.es.md) | [Italiano](README.it.md)** · **[🌐 Explorateur Web & API statique](https://mickey996icu.github.io/opf-data/)** · **[🖼️ Médias d'étiquettes (`opf-media`)](https://github.com/mickey996icu/opf-media)** · **[🧰 Outils & Agent Skill (`open-packaged-food`)](https://github.com/mickey996icu/open-packaged-food)**

Ce dépôt héberge la base ouverte de référence d'**OpenPackagedFood (`opf` v0.2)** : fiches produits JSON vérifiées (`products/`), correctifs de registres officiels (`overlays/`), API statique zéro-authentification `/v1/` sur GitHub Pages et explorateur web en 7 langues. Les recadrages d'étiquettes sont isolés dans [`opf-media`](https://github.com/mickey996icu/opf-media) (`opf-media://sha256/<hex>`).

> **Initialisation vierge (`products/.gitkeep`)** : `opf-data` démarre proprement avec `0` fiche fictive et n'accepte que des produits réels étayés par des recadrages d'étiquettes ou des registres publics CC0.

---

## Référence rapide de l'API statique (`/v1/`)

- **Accès GTIN-14** : compléter à gauche avec des zéros (`0000000000017` → `00000000000017`), fragments `p1 = gtin14[1:4]`, `p2 = gtin14[4:7]` :
  `GET https://mickey996icu.github.io/opf-data/v1/gtin/000/000/00000000000017.json`
- **Annuaire par marque** : `/v1/index/brands.json` → `/v1/index/brand/{brand_slug}.json` (avec `brand_names` multilingues)
- **Catalogue JSONL** : `/v1/index/catalog.jsonl` · **Métadonnées** : `/v1/meta.json`
- **Quatre modes `resolution`** : `single` / `uniform` / `divergent` (`normalized` omis au sommet) / `inactive`

---

## Contribuer (Phase 1 : flux développeur en Git pur)

> L'explorateur web est dédié à la consultation en lecture seule et au scan local pour le grand public ; la soumission grand public ouvrira en Phase 2 via `OPF_SUBMIT_URL`. La Phase 1 est réservée aux développeurs en Git pur.

1. **Voie principale développeur (Git pur & double PR)** :
   ```bash
   git clone https://github.com/mickey996icu/open-packaged-food.git
   git clone https://github.com/mickey996icu/opf-data.git
   git clone https://github.com/mickey996icu/opf-media.git
   ./open-packaged-food/bin/opf submit --input draft.json --out-dir opf-data/products \
     --photo nutrition_table=crop_nutrition.jpg --photo ingredients_box=crop_ingredients.jpg \
     --vintage-month 2026-08 --media-out-dir opf-media
   ./open-packaged-food/bin/opf validate opf-data/products
   ```
   > ⚠️ **Ordre de fusion impératif** : après `git commit -s` et `git push`, **fusionnez d'abord la PR `opf-media`**, puis la PR `opf-data` (`opf check-media-refs`).
2. **Automatisation optionnelle (`uvx` + `gh`)** :
   `uvx --from git+https://github.com/mickey996icu/open-packaged-food@v0.2.5 opf pr --input draft.json --photo ...` reste disponible comme raccourci optionnel (les PR externes utilisent automatiquement `provenance: "external_contributor"`). Voir [CONTRIBUTING.md](https://github.com/mickey996icu/open-packaged-food/blob/main/CONTRIBUTING.md), [Guide ODbL](https://github.com/mickey996icu/open-packaged-food/blob/main/docs/ODbL-GUIDE.md) et [Limites des données](https://github.com/mickey996icu/open-packaged-food/blob/main/docs/DATA-LIMITATIONS.md).
