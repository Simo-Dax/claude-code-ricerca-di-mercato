# Competitor Ads Dashboard

Dashboard esterna, single-file, per visualizzare e filtrare le ads dei competitor raccolte da `19_ad_spy` (`/pm-competitor-spy`, Meta) e da `62_google_ads_spy` (`/pm-google-spy`, Google Ads Transparency Center). **Un solo posto per i due canali.** **Look & feel Airbnb** (palette Rausch #FF385C, font Nunito Sans, card stile listing, shadow morbide).

**Posizione:** `dashboard/competitor-ads/` — è un **output** del sistema (alimentata da dati di campagna), non una reference.

## Cosa fa

- **Sidebar sinistra**: filtri per **Canale** (Meta / Google), **Competitor**, **Stage of Awareness** (5 stadi Eugene Schwartz: Unaware → Most-Aware), **Funnel** (TOF / MOF / BOF), **Performance Tier** (Proven/Hot/Active/Retired/Short).
- **Destra**: griglia di card stile Airbnb listing. Ogni card = creative dell'ad + brand/logo + tag awareness/funnel + KPI + link all'Ad Library.
- Ricerca testuale + sort (longevità, EU reach, brand). Contatori live a fianco di ogni filtro.

## Come usarla

1. `19_ad_spy` genera le righe Meta e `62_google_ads_spy` quelle Google (`source:"google"`) — schema in `data.sample.json`.
2. Unisci gli array `ads` in un unico `data.json` e copialo in questa cartella.
3. Apri `index.html` in un browser (funziona anche offline; senza `data.json` mostra dati demo).
4. Deploy come link esterno: trascina la cartella su **Netlify Drop**, **Cloudflare Pages** o **Vercel**. Nessun build step.

## ⚠️ Realtà dei dati (importante)

Meta Ad Library, per le ads **commerciali** (non politiche), espone solo:
- ✅ Creative (immagine), data inizio, page, piattaforme (FB/IG)
- ✅ `days_active` (longevità — proxy n.1 di "cosa funziona")
- ✅ `eu_total_reach` (solo per utenti EU, è un numero aggregato)
- ✅ n. di varianti nello stesso gruppo

**NON disponibili** per ads commerciali: **impressions, spend, CTR, ROAS** (solo per ads politiche/social-issue). I KPI reali sono longevità + EU reach + varianti. La longevità è il segnale più forte.

### Google Ads Transparency Center

Per le ads Google i campi cambiano: niente `eu_reach` (è un dato solo Meta, la card mostra **Regioni**), in compenso `shownForDays` → `days_active` con **gli stessi tier di Meta**, più `platform` (SEARCH / YOUTUBE / SHOPPING / MAPS / PLAY) e `regions[]`. ⚠️ Il copy delle text ads Google arriva come **immagine renderizzata**: `62_google_ads_spy` lo trascrive verbatim leggendo l'immagine, non c'è una stringa da pullare.

### Awareness & Funnel = classificati, non pullati
`awareness_stage` e `funnel_stage` NON vengono da Meta. Li **classifica l'LLM** in `19_ad_spy` analizzando creative + copy. È ciò che rende la dashboard strategicamente utile: white space a colpo d'occhio.

## Look & feel (Airbnb)

- **Colori:** Rausch `#FF385C` (primary), Hof `#222222` (ink), Foggy `#717171` (muted), bianco, bordi `#DDDDDD`.
- **Font:** Nunito Sans (sostituto web-safe di Airbnb Cereal/Circular).
- **Componenti:** search pill arrotondata con shadow, card listing con immagine rounded 12px + lift on hover, badge tier come pill bianca con shadow, tag awareness in tinta Rausch, tag funnel colorati.

## Evoluzioni possibili (fase 2)

- Hosting dati su Google Sheet/Airtable + fetch live invece di `data.json` statico
- Aggiornamento schedulato via n8n MCP (re-run `/pm-competitor-spy` settimanale → push nuovo `data.json`)
- Heatmap white-space: matrice awareness × competitor con densità ads
- Video ads Meta (oggi `19_ad_spy` filtra solo static; i teardown vivono in `52_ad_spy_video`)
