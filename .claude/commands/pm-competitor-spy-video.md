---
description: Ad spy competitor Meta — teardown strutturato per ogni VIDEO ad (script word-for-word, on-screen text, hook, beat sheet, CTA). Sorella video di /pm-competitor-spy (solo static). Skill nativa 52_ad_spy_video (SA1). Richiede Apify + fal.ai.
argument-hint: [brand o lista brand o keyword nicchia] [paese] [n. video]
---

# /pm-competitor-spy-video — Ad Spy Video Competitor Meta

Esegui la skill nativa **`directives/skills/52_ad_spy_video.md`** (SA1 — Competitor Analysis).

Argomenti: $ARGUMENTS

## Cosa fare
1. Leggi e segui integralmente `directives/skills/52_ad_spy_video.md`.
2. Prerequisiti: Apify API key (`/pm-setup-apify`) + fal.ai API key (`/pm-setup-fal-ai`). Se assenti, fermati e indirizza lì.
3. Token Apify SEMPRE come header `Authorization: Bearer`, mai in URL. Nessun `mcp__apify__*`.
4. Pages scraper per validare il Page ID → scraping `media_type=all` + filtro video in post → download mp4 → trascrizione fal.ai Whisper → frame ffmpeg → teardown agenti paralleli (model sonnet, batch 4).
5. Output: `03_Ad_Spy/<slug>-video/video-teardown-*.html` + `.json`.

Pura competitor intelligence — non genera ad. Il teardown alimenta SA5 (`/pm-competitor-rebuild`) e SA7 per pattern/hook di script.
