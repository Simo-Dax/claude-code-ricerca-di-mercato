---
description: Ad spy competitor su Google Ads Transparency Center — Search/YouTube/Shopping/Display, ranked per longevità + confronto cross-canale con Meta. Skill 62 (SA1). Richiede Apify.
argument-hint: [dominio competitor o lista domini] [paese ISO2] [n. ads]
---

# /pm-google-spy — Ad Spy Competitor Google

Esegui la skill nativa **`directives/skills/62_google_ads_spy.md`** (SA1 — Competitor Analysis).

Argomenti: $ARGUMENTS

## Cosa fare
1. Leggi e segui integralmente `directives/skills/62_google_ads_spy.md`.
2. Prerequisito: Apify API key (`/pm-setup-apify`). Se assente, fermati e indirizza lì.
3. Token Apify SEMPRE come header `Authorization: Bearer`, mai in URL. Nessun `mcp__apify__*`.
4. Entra **dal dominio** (`domains`), non dal nome brand. Nome solo come fallback.
5. Il copy delle text ads va letto con la vision dalle immagini `simgad` e trascritto verbatim. Mai ricostruito.
6. `includeDetails: true` solo sui top 10-15 ad per longevità (controllo costi).
7. Output: `03_Ad_Spy/google/gads-*.html` + `.json` con tier PROVEN/HOT/ACTIVE/RETIRED, `platform_mix` per competitor e la sezione finale **META vs GOOGLE**.
8. Accoda le righe con `source:"google"` a `output/dashboard/competitor-ads/data.json`.

Da lanciare **insieme** a `/pm-competitor-spy` (Meta static) e `/pm-competitor-spy-video`: i tre insieme fanno la messaging matrix cross-canale di SA1.
