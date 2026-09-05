---
description: Scraping TikTok UGC virali — 25 transcript per swipe file con vetting LLM di rilevanza. Skill nativa 20_ugc_scraper (SA1). Richiede Apify. Costo ~$0.056/run.
argument-hint: [keyword nicchia] [paese]
---

# /pm-ugc-analysis — UGC Scraper TikTok

Esegui la skill nativa **`directives/skills/20_ugc_scraper/SKILL.md`** (SA1 — Competitor Analysis).

Argomenti: $ARGUMENTS

## Cosa fare
1. Leggi e segui integralmente `directives/skills/20_ugc_scraper/SKILL.md`.
2. Prerequisito: Apify API key (`/pm-setup-apify`).
3. 6 query × 20 risultati via actor scraptik (`searchPosts_sortType: 1` Most Liked obbligatorio, `searchPosts_publishTime: 90`).
4. Vetting LLM di rilevanza (0-10, scarta <7), poi trascrivi le 25 gold pick via scrape-creators.
5. Output: 25 transcript per swipe file.

Conferma il costo (~$0.056/run) prima di lanciare le chiamate Apify.
