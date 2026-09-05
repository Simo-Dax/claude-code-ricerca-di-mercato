---
description: Ad spy competitor Meta — swipe file HTML brand-locked con i soli static ads, ranked per durata run/reach. Skill nativa 19_ad_spy (SA1). Richiede Apify.
argument-hint: [brand o lista brand o keyword nicchia] [paese] [n. ads]
---

# /pm-competitor-spy — Ad Spy Competitor Meta

Esegui la skill nativa **`directives/skills/19_ad_spy.md`** (SA1 — Competitor Analysis).

Argomenti: $ARGUMENTS

## Cosa fare
1. Leggi e segui integralmente `directives/skills/19_ad_spy.md`.
2. Prerequisito: Apify API key (`/pm-setup-apify`). Se assente, fermati e indirizza lì.
3. Token Apify SEMPRE come header `Authorization: Bearer`, mai in URL.
4. Prima il pages scraper per validare il Page ID, poi N agent paralleli per N brand.
5. Output: `03_Ad_Spy/adspy-*.html` con scoring tiers (PROVEN/HOT/ACTIVE/RETIRED/SHORT RUN).

Lo swipe file alimenta SA5 (`/pm-competitor-rebuild`) e SA7 (`/pm-meta-copy`).
