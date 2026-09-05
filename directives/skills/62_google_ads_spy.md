# SA1 — Google Ads Spy (Ads Transparency Center)

**Agente:** SA1 (Competitor Analysis)
**Output:** `03_Ad_Spy/google/gads-<slug>-<YYYYMMDD>.html` + `.json` (+ righe `source:"google"` per `output/dashboard/competitor-ads/data.json`)
**Prerequisiti:** Apify API key (`/pm-setup-apify`)
**Fratelli:** `19_ad_spy` (Meta static) · `52_ad_spy_video` (Meta video). Questa skill copre il **lato Google**: Search, YouTube, Shopping, Display, Maps, Play.

Meta racconta cosa il competitor spinge **in interruzione**. Google racconta cosa presidia **in intento**. Senza il secondo lato la messaging matrix è mezza cieca: molti brand scrivono su Search la promessa che non osano mettere su Meta.

---

## Step 0 — Token Apify (REST diretto, no MCP)

```bash
[ -f ~/.config/pm-agent/apify.env ] && . ~/.config/pm-agent/apify.env
TOKEN="${APIFY_TOKEN:-}"
```
Se `TOKEN` vuoto → `/pm-setup-apify` e fermati. Token SEMPRE come header `Authorization: Bearer`, mai in URL. Nessun `mcp__apify__*`.

## Step 0.5 — Protezione cartella

Identica a `19_ad_spy` Step 0.5. Cattura `$WORKDIR`, poi `mkdir -p "$WORKDIR/03_Ad_Spy/google"`.

---

## Step 1 — Risolvere il competitor (dominio > nome)

Il Transparency Center indicizza per **advertiser**, non per pagina social. Tre modi di entrare, in ordine di affidabilità:

| Input | Campo actor | Affidabilità |
|---|---|---|
| Dominio esatto (`brand.it`) | `domains` | ⭐ massima — restituisce ogni advertiser che punta a quel dominio |
| ID advertiser (`AR…`) | `advertiserIds` | massima, ma va trovato a mano sulla URL del Transparency Center |
| Nome brand | `queries` | media — passa dall'autocomplete, può agganciare omonimi |

**Regola:** parti sempre dal dominio del sito (quello della landing, non del blog). Usa `queries` solo se il dominio non restituisce nulla. È l'equivalente del brand-lock via `pageAdLibrary.id` di `19_ad_spy`: mai fidarsi del nome nudo.

Un brand grande ha **più advertiser account** (es. entità legale per paese). Il campo `advertiserName` nell'output te lo dice — tienili separati nel report, non fonderli.

---

## Step 2 — Chiamata actor

Actor: **`scrapesage/google-ads-transparency-scraper`** (verificato 2026-09-04: 8 ads su `hellofresh.it`/IT in ~40s, `$0.002` per ad + start).
Fallback se down o vuoto: `s-r/google-ads-transparency` (stesso dato, input `domain` singolo).

```bash
curl -s --max-time 240 -X POST \
  "https://api.apify.com/v2/acts/scrapesage~google-ads-transparency-scraper/run-sync-get-dataset-items?timeout=220" \
  -H "Authorization: Bearer $TOKEN" -H "Content-Type: application/json" \
  -d '{
    "domains": ["COMPETITOR.com"],
    "region": "IT",
    "resultType": "ads",
    "adFormat": "ALL",
    "maxAdsPerSearch": 60,
    "includeDetails": false
  }' -o "03_Ad_Spy/google/raw-<slug>.json"
```

Parametri che contano:
- `region` — ISO2 (`IT`, `DE`, `US`) o `ANYWHERE`. **Usa il mercato del brief**, non ANYWHERE: le creative cambiano per paese e ANYWHERE gonfia il campione di rumore.
- `platforms` — `["SEARCH"]`, `["YOUTUBE"]`, `["SHOPPING"]`, `["MAPS"]`, `["PLAY"]`. Lascia vuoto per tutte, poi segmenta in post. I filtri piattaforma valgono solo per ads mostrate dal 2023-09-04.
- `adFormat` — `TEXT` / `IMAGE` / `VIDEO` / `ALL`.
- `includeDetails: true` — +`$0.003`/ad, aggiunge **tutte le varianti creative** (A/B reali del competitor) e `regionsShown` (ogni paese con ultima data). Attivalo **solo sui top 10-15 ad per longevità**, non su tutto il campione: il costo raddoppia e il valore sta solo sulle ad che girano davvero.
- `onlyNewAds: true` + `monitorStoreName` — modalità monitor: da un secondo run in poi restituisce solo le ad nuove. È il gancio per lo scheduling settimanale (n8n / `/schedule`).

**Parallelo:** N competitor = N chiamate in parallelo (stesso pattern list-mode di `19_ad_spy`), un file raw per slug.

---

## Step 3 — Campi restituiti (e cosa NON c'è)

Per ad: `creativeId`, `advertiserId`, `advertiserName`, `domain`, `format` (TEXT/IMAGE/VIDEO), `firstShown`, `lastShown`, **`shownForDays`**, `previewType`, `imageUrl`, `previewUrl`, `width`/`height`, `adUrl` (link diretto al creative sul Transparency Center), `region`.

✅ Disponibile: creative, longevità, finestra temporale, formato, piattaforma, regioni, varianti (con `includeDetails`).
❌ **NON** disponibile: impression, spend, CTR, conversioni, keyword d'asta. Come su Meta, il segnale forte è **la longevità**: nessuno paga per mesi un'ad che non converte.

⚠️ **Le text ads sono immagini renderizzate.** Il copy di una Search ad non arriva come stringa: arriva come PNG su `tpc.googlesyndication.com/archive/simgad/…`. Per avere headline e description **leggi le immagini con la vision del modello** (batch da 8-10 per volta) e trascrivi verbatim in `headline` / `description`. Non inventare mai il copy: se l'immagine non è leggibile, lascia `null` e segnalo.

Video (YouTube): `previewUrl` porta al player. Per script/hook usa la stessa pipeline di `52_ad_spy_video` (download → fal.ai transcribe → beat sheet), solo sui 3-5 video più longevi.

---

## Step 4 — Scoring tiers (allineati a `19_ad_spy`)

Su `shownForDays`, così Meta e Google si confrontano sulla stessa scala:

| Tier | Criterio |
|---|---|
| 🏆 PROVEN | ≥ 60 giorni |
| 🔥 HOT | ≥ 21 giorni |
| ⚡ ACTIVE | < 21 giorni, `lastShown` negli ultimi 14 gg |
| ✅ RETIRED | `lastShown` più vecchio di 14 gg |

Aggiungi la dimensione che Meta non ha: **`platform_mix`** per competitor (% ads Search / YouTube / Shopping / Display). Dice dove sta davvero il budget Google del competitor.

---

## Step 5 — Classificazione LLM (identica a Meta)

Per ogni ad classifica `awareness_stage` (Unaware → Most-Aware, Schwartz) e `funnel_stage` (TOF/MOF/BOF) leggendo creative + copy trascritto. Google spinge la distribuzione verso Solution/Product-aware (l'utente sta già cercando): è proprio quel delta con Meta a rivelare il white space.

Estrai anche, per le Search ads: **claim ricorrenti**, **offerte/prezzi esposti**, **USP in headline**, **sitelink/estensioni visibili**. Sono le promesse che il competitor è disposto a mettere per iscritto davanti a un utente in intento — il materiale più onesto che troverai.

---

## Step 6 — Output

**A) `03_Ad_Spy/google/gads-<slug>-<data>.json`** — array normalizzato, stesso schema della dashboard con `"source": "google"`:

```json
{
  "id":"CR…", "competitor_id":"<slug>", "source":"google",
  "platform":"SEARCH|YOUTUBE|SHOPPING|MAPS|PLAY|DISPLAY",
  "image":"…", "video":null, "headline":"…", "description":"…", "cta":null,
  "format":"TEXT|IMAGE|VIDEO",
  "awareness_stage":"Solution-Aware", "funnel_stage":"BOF", "tier":"PROVEN",
  "days_active": 142, "first_seen":"2026-01", "last_seen":"2026-08",
  "eu_reach": null, "n_variants": 3, "regions":["IT","ES"],
  "ad_library_url":"https://adstransparency.google.com/advertiser/AR…/creative/CR…?region=IT"
}
```
`eu_reach` resta `null`: è un dato solo Meta. La dashboard lo mostra come `n/d`.

**B) `03_Ad_Spy/google/gads-<slug>-<data>.html`** — swipe file self-contained (CSS inline, zero dipendenze), stessa griglia di `19_ad_spy`, ordinato per longevità, con badge tier e piattaforma.

**C) Merge dashboard** — accoda le ad Google all'array `ads` di `output/dashboard/competitor-ads/data.json`, marcate `source:"google"`. La sidebar ha il filtro **Piattaforma** (Meta / Google): un solo posto per vedere i due mondi affiancati.

---

## Step 7 — Sezione cross-platform (il deliverable che vale)

Chiudi l'HTML con l'analisi che nessuna delle due librerie da sola può dare:

```
## META vs GOOGLE — {Competitor}

| Dimensione | Meta | Google |
|---|---|---|
| N. ads attive | | |
| Ads PROVEN (≥60gg) | | |
| Awareness dominante | | |
| Angolo dominante | | |
| Offerta/prezzo esposto | | |
| Formato prevalente | | |

**Convergenza:** [i messaggi identici sui due canali = le loro convinzioni forti, quelle su cui hanno già speso per validare]
**Divergenza:** [ciò che dicono solo su Search e mai su Meta (o viceversa) = dove sono insicuri, o dove segmentano per intento]
**Buco:** [canale/piattaforma/awareness dove non compaiono affatto]
```

Le tre righe finali entrano dritte nella messaging matrix di SA1 Fase 3 e nella white-space map.

---

## Regole ferree
- Un competitor senza ads su Google **è un dato**, non un errore: scrivilo ("nessuna ad attiva su Transparency Center in {region} al {data}"). Spesso è il white space più grande del report.
- Copy delle text ads solo verbatim da lettura immagine. Zero parafrasi, zero ricostruzioni plausibili.
- Dichiara sempre `region` e data di scrape in testa al report: il Transparency Center è una fotografia, non uno storico stabile.
- `includeDetails` solo sui top ad per longevità (controllo costi).

## Handoff
→ **SA1** Fase 2/3 (schede competitor + messaging matrix cross-canale) e Fase 5 (white space)
→ **SA4** (i claim esposti in Search sono il benchmark della nostra value proposition)
→ **SA7** (`12_google_copy`: le headline PROVEN dei competitor sono il baseline da battere; `28_meta_copy` per il delta di canale)
→ **SA5** (`23_competitor_rebuild` sui video YouTube longevi)

---

## Definition of done
- [ ] Ingresso **dal dominio**, non dal nome brand (il nome porta omonimi e advertiser terzi)
- [ ] Copy delle text ads **trascritto dalla vision** sull'immagine `simgad`, verbatim. Mai ricostruito da titolo o URL: se non è stato letto, si dichiara «N annunci non trascritti» con il numero esatto
- [ ] `shownForDays` → tier allineati a `19_ad_spy`, con mediana e massimo per competitor
- [ ] `platform_mix` per competitor (Search / YouTube / Shopping / Display / Maps)
- [ ] Copertura geo dichiarata per competitor: uno che spinge solo UK e non US è un dato di espansione, non rumore
- [ ] Sezione **META vs GOOGLE** chiusa: convergenza / divergenza / buco di canale, con la lettura strategica di ognuno
- [ ] Merge in `output/dashboard/competitor-ads/data.json` con `source:"google"` su ogni riga
- [ ] Zero ads Google = dato verificato e dichiarato (il competitor non presidia l'intento), non silenzio
