---
description: Competitor review mining — gap di mercato dal delta tra recensioni positive e negative dei competitor (Amazon/Trustpilot/G2/App Store). Apify REST. Skill 47 (SA1/SA2 → SA4).
argument-hint: [competitor o categoria]
---

# /pm-review-gap — Competitor Review Mining (gap positivo/negativo)

Esegui la skill nativa **`directives/skills/47_competitor_review_mining.md`**.

Argomenti: $ARGUMENTS

## Cosa fare
1. Leggi e segui `directives/skills/47_competitor_review_mining.md`.
2. Step 0: token Apify da `~/.config/pm-agent/apify.env` (REST diretto, header `Authorization: Bearer`, no MCP). Se manca → `/pm-setup-apify`.
3. Scegli le sorgenti recensioni per business model (Amazon/Trustpilot/G2/Capterra/App Store/Google reviews). Campione bilanciato ≥30 recensioni, tutte le stelle.
4. Estrai per polarità (positive = table stakes/forze; negative = unmet needs/gap), verbatim + frequenza + intensità.
5. Costruisci la **GAP MAP** (gap di esecuzione / gap scoperto / trade-off polarizzante) + top 3-5 gap + table stakes + forze da non attaccare.
6. Output: `intermediate/competitor_review_gap.md` → alimenta `33_insight_synthesis` e `48_segment_pain_prioritization`.
