---
description: First-party data analysis — analizza i dati propri del cliente (GA4/Shopify/ads export + recensioni/ticket/survey) in due track: quantitativo (→SA3) e qualitativo (→insight). Skill 38 (SA2).
argument-hint: [cartella dati o "context/campaign/data/"]
---

# /pm-data-analysis — First-Party Data Analysis (SA2)

Esegui la skill **`directives/skills/38_first_party_data_analysis.md`**.

Argomenti: $ARGUMENTS

## Cosa fare
1. Leggi e segui `directives/skills/38_first_party_data_analysis.md`.
2. Inventaria i file forniti dal cliente (default `context/campaign/data/`). Se non ci sono → salta con nota.
3. **Track A — Quantitativo**: trend vendite (escludi BF/CM), top prodotti, funnel, coorti new/returning, AOV reale, CRR, PF → `intermediate/first_party_quant.md` → alimenta SA3.
4. **Track B — Qualitativo**: sentiment + temi + angoli da recensioni/ticket/survey propri → `intermediate/first_party_qual.md` → alimenta `33_insight_synthesis` + SA5/SA7.
5. Solo dati reali, cita il file fonte per ogni cifra. Niente invenzioni.
