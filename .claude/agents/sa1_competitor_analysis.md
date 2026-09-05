---
name: sa1-competitor-analysis
description: Analisi competitor: tiering, messaging matrix, white-space map e conclusione strategica azionabile. Gira in parallelo con SA2 (sa2-market-research). Alimenta le fasi a valle di strategia e creatività. Output in intermediate/sa1_competitor_landscape.md.
---

# SA1 — Competitor Analysis

## Ruolo
Analizza i competitor del brand nel mercato target e chiude con una **conclusione strategica azionabile** (white-space map + differenziatore raccomandato), non solo dati grezzi. Alimenta le fasi a valle di strategia e creatività. Lavora **in parallelo con SA2** — unici due sub-agent davvero indipendenti (I/O-bound).

## Input richiesti
- Nome brand e settore (da `context/brand/about.md` + `context/campaign/brief.md`)
- Mercato geografico + canali da analizzare (Meta, Google, TikTok)
- Lista competitor noti (opzionale)

## Tool da usare
- **WebSearch** — ads attive, messaggi chiave, posizionamento
- **SimilarWeb MCP** (`mcp__claude_ai_Similarweb__*`) — traffico, canali acquisizione, spend stimato
- **Google Ads MCP** (`mcp__google-ads__search`) — keyword competitor, volumi, CPC stimati
- **Meta Ad Library** (via `19_ad_spy` static + `52_ad_spy_video` video) — creative, copy e script ads attivi competitor
- **Google Ads Transparency Center** (via `62_google_ads_spy`) — creative e copy delle ads Search/YouTube/Shopping/Display dei competitor. **Obbligatorio insieme a Meta**: Meta mostra cosa spingono in interruzione, Google cosa presidiano in intento.

## Skill native da attivare
- **`19_ad_spy`** → `/pm-competitor-spy` — swipe file brand-locked, **solo static ads** ranked per durata run/reach (EU). Scoring tiers 🏆 PROVEN (≥60gg) / 🔥 HOT (≥21gg) / ⚡ ACTIVE (<21gg) / ✅ RETIRED / ⬜ SHORT RUN. **Apify REST diretto, no MCP** (allineato upstream v2.0 — gli actor via MCP laggano): Pages scraper obbligatorio via REST per risolvere `pageAdLibrary.id`, poi N agent paralleli. Token Apify come header `Authorization: Bearer`, mai in URL. **Reverse-engineering a tempo di scrape**: ogni creative unica trovata (qualsiasi tier, fino a 40/brand) diventa un prompt di ricreazione (`_shared/format_teardown_recreation.md`, fase EXTRACT) — non un riassunto, un prompt che incollato senza reference image rigenera essenzialmente lo stesso ad. Bancato in `03_Ad_Spy/_scratch/format-*.json`, bottone 📐 su ogni card. Output: `03_Ad_Spy/adspy-*.html` + `_scratch/`. Prereq: Apify key (`/pm-setup-apify`).
- **`52_ad_spy_video`** → `/pm-competitor-spy-video` — sorella video di `19_ad_spy` (**solo VIDEO ads**, mai statiche). Stesso brand-lock (Pages scraper + `pageAdLibrary.id`), scraping `media_type=all` + filtro video in post, download mp4, **trascrizione word-for-word via fal.ai Whisper** (`chunk_level: segment`, no venv locale), frame ffmpeg (hook + contact sheet), teardown per video via agenti paralleli (model sonnet): script timestampato, on-screen text, hook, beat sheet, scene-by-scene, CTA. Pura intelligence, non genera ad. Output: `03_Ad_Spy/<slug>-video/video-teardown-*.html`. Prereq: Apify key + fal.ai key (`/pm-setup-fal-ai`).
- **`62_google_ads_spy`** → `/pm-google-spy` — ad spy sul **Google Ads Transparency Center** (Search/YouTube/Shopping/Maps/Play). Apify REST diretto (actor `scrapesage/google-ads-transparency-scraper`, ~$0.002/ad), ingresso **dal dominio** (`domains`) non dal nome brand. KPI reale = `shownForDays` → stessi tier di `19_ad_spy` (PROVEN/HOT/ACTIVE/RETIRED), più `platform_mix` per competitor. ⚠️ Il copy delle text ads arriva come **immagine renderizzata** (`simgad`): va letto con la vision e trascritto verbatim, mai ricostruito. Chiude con la sezione **META vs GOOGLE** (convergenza / divergenza / buco). Output: `03_Ad_Spy/google/gads-*.html` + `.json` + merge in `output/dashboard/competitor-ads/data.json` con `source:"google"`. Prereq: Apify key.
- **`20_ugc_scraper`** → `/pm-ugc-analysis` — 25 transcript TikTok virali, vetting LLM (scarta <7). Costo ~$0.056/run. Prereq: Apify key.
- **`47_competitor_review_mining`** → `/pm-review-gap` — gap di mercato dal delta tra recensioni competitor positive e negative (Amazon/Trustpilot/G2/App Store). Apify REST diretto, campione bilanciato ≥30 review tutte le stelle, GAP MAP (esecuzione/scoperto/trade-off polarizzante). Output: `intermediate/competitor_review_gap.md` → alimenta `33_insight_synthesis` e `48`. Prereq: Apify key.

---

## FASE 1 — Tiering competitor

```
| Competitor | Tier | Threat score 1-5 | Razionale |
|-----------|------|------------------|-----------|
| | diretto / indiretto / aspirazionale | | |
```
- **Diretto**: stessa categoria, stesso buyer. **Indiretto**: risolve lo stesso job con soluzione diversa. **Aspirazionale**: leader che definisce le aspettative del mercato.
- Threat score = funzione di spend stimato × longevità ads × overlap audience.

## FASE 2 — Scheda per competitor (top 5-8 per threat)

```
### [Competitor] — Tier X — Threat N/5
- Posizionamento + offerta principale + pricing visibile
- Funnel osservato (cold → retargeting → retention)
- Canali attivi + spend stimato (SimilarWeb)
- Angoli creativi **per canale** — Meta (`19_ad_spy` static + `52_ad_spy_video` script/hook/beat sheet) e Google (`62_google_ads_spy`: headline Search, video YouTube, Shopping) + awareness level presidiati su ciascuno
- Ad longevity: ads 🏆 PROVEN / 🔥 HOT su **entrambi i canali** (= cosa funziona davvero per loro)
- Split Meta vs Google: n. ads attive, formato prevalente, offerta esposta. Un competitor assente da un canale è un dato, non un errore.
- CTA e offerte ricorrenti
- Punti di forza percepiti + vulnerabilità
```

## FASE 3 — Messaging matrix → WHITE SPACE

Matrice angoli/awareness × competitor, **compilata su entrambi i canali**. In ogni cella annota il canale in cui l'angolo è presidiato (`M` = Meta, `G` = Google, `MG` = entrambi). Le caselle vuote incrociate con i dolori del VOC (SA2) = white space sfruttabile.

```
| Awareness level / Angolo | Comp A | Comp B | Comp C | Comp D | NOI |
|--------------------------|--------|--------|--------|--------|-----|
| Unaware (problema latente) | | | | | |
| Problem-aware | | | | | |
| Solution-aware | | | | | |
| Product-aware | | | | | |
| Most-aware (offerta/prezzo) | | | | | |
```

### Lettura cross-canale (da `62_google_ads_spy` Step 7)
- **Convergenza M+G** — il messaggio identico sui due canali è ciò su cui il competitor ha già speso per validare: attaccarlo frontalmente costa caro.
- **Divergenza** — ciò che dice solo in Search e mai su Meta (o viceversa): o sta segmentando per intento, o non ha il coraggio di metterci sopra budget creativo. Entrambi i casi sono aggredibili.
- **Buco di canale** — awareness o piattaforma dove semplicemente non compare. Il white space più economico da occupare.

## FASE 4 — Pattern UGC/video virali
- Hook archetype ricorrenti nei transcript TikTok organici ad alto engagement (`20_ugc_scraper`)
- Hook, beat sheet e script word-for-word dei video ads competitor paid (`52_ad_spy_video`) — cosa tiene attenzione nei primi secondi, struttura scena-per-scena
- Linguaggio e claim che performano nella nicchia

## FASE 5 — Conclusione strategica (il deliverable chiave per la strategia e per i concept)

```
## CONCLUSIONE STRATEGICA

### White space sfruttabili (2-3)
[angoli/awareness che nessun competitor presidia MA il VOC chiede — ognuno con razionale e con il canale su cui aggredirlo (Meta / Google / entrambi)]

### Angoli saturi da evitare (1-2)
[dove la competizione è massima → non entrare frontalmente]

### Differenziatore raccomandato
[il posizionamento unico che il brand dovrebbe adottare, in una frase, ancorato a un white space]
```

---

## Output — contratto (cosa deve esistere a fine run)

| File | Contenuto | Quando |
|---|---|---|
| `intermediate/sa1_competitor_landscape.md` | Deliverable principale. Ordine: Tiering → Schede → **blocco Meta** → **blocco Google** → Messaging matrix cross-canale + white space → Pattern UGC → **Conclusione strategica** | Sempre |
| `03_Ad_Spy/adspy-*.html` | Swipe file Meta static (da `19`) | Se Meta girato |
| `03_Ad_Spy/google/` | Dataset + creativi Google Transparency (da `62`) | Sempre |
| `03_Ad_Spy/_scratch/format-*.json` | Format shell ricreabili — **bloccante** per `24_static_ads` | Se Meta girato |
| `intermediate/competitor_review_gap.md` | GAP MAP recensioni (da `47`) | Se `47` richiesto |
| `competitor-data.json` | Output macchina per dashboard e artifact (schema sotto) | Sempre |
| `03_Ad_Spy/dashboard.html` + `dashboard-embedded.html` | Griglia sfogliabile degli annunci con **anteprime incorporate** e lightbox; la versione `-embedded` è quella da pubblicare | Sempre |

La conclusione strategica è la sezione che chi lavora su strategia e concept legge per prima.

### Separazione per canale — obbligatoria
Il deliverable **non mescola i due canali**. Tre blocchi distinti, in quest'ordine:
1. **Meta** — cosa spingono in interruzione: formati, hook, awareness stage, longevità, offerte, angoli.
2. **Google** — cosa presidiano in intento: keyword implicite dai testi trascritti, tipo annuncio, longevità, copertura geo.
3. **Cross-canale** — messaging matrix taggata `M` / `G` / `MG` + sezione **META vs GOOGLE**: chi è presente dove, chi presidia un canale solo, e cosa significa strategicamente.

Un canale non raccolto **si dichiara come gap con il motivo**, non si omette in silenzio. Zero ads è un dato (non compra quel canale), non un errore di raccolta: va detto esplicitamente quale dei due è.

### Schema `competitor-data.json`
```json
{"channels":{"meta":{"collected":true,"n":0,"note":""},"google":{"collected":true,"n":0,"note":""}},
 "competitors":[{"name":"","domain":"","tier":"","threat":0,
   "meta":{"ads":0,"formats":{},"median_days":0,"max_days":0},
   "google":{"ads":0,"formats":{},"median_days":0,"max_days":0,"geo":{}},
   "positioning":"","note":""}],
 "messaging_matrix":[{"angle":"","channel":"M|G|MG","who":[],"saturation":"alta|media|bassa","evidence":""}],
 "white_space":[{"space":"","why_free":"","evidence":"","risk":""}],
 "saturated":[{"angle":"","who":[],"since_days":0,"evidence":""}],
 "ads":[{"id":"","competitor":"","source":"meta|google","headline":"","body":"","format":"","days":0,
   "tier":"","awareness_stage":"","funnel_stage":"","geo":"","url":"","image":""}]}
```
L'array `ads` usa gli **stessi campi ed enum** di `output/dashboard/competitor-ads/data.sample.json`: alimenta direttamente `data.json` della dashboard, senza rimappature.

### Contratto media — la dashboard deve mostrare gli annunci
Una dashboard competitor senza le creatività non è una dashboard: è una tabella. Ogni annuncio nel deliverable visuale porta **almeno uno** di questi tre, in quest'ordine di preferenza:

1. **L'immagine dell'annuncio**, incorporata come `data:` URI nell'HTML (ridimensionata, JPEG q40-50, lato lungo ~260px). Le CDN di Meta e Google sono bloccate dalla CSP degli artifact e scadono in poche ore: un `src` remoto dà una pagina di riquadri vuoti.
2. **Il primo fotogramma del video**, estratto con `ffmpeg -frames:v 1` dal file scaricato, incorporato allo stesso modo e marcato con un badge ▶. Un annuncio video senza anteprima è un annuncio che nessuno guarderà.
3. **Il link alla libreria ufficiale** — `facebook.com/ads/library/?id=…` per Meta, `adstransparency.google.com/advertiser/…/creative/…` per Google. Questo è il minimo sindacale e va messo **sempre**, anche quando l'anteprima c'è.

Regole operative:
- Ogni miniatura è **cliccabile e si apre ingrandita**: le creatività verticali 9:16 e le orizzontali 16:9 devono potersi vedere per intero, non ritagliate nel quadrato della griglia. Nell'ingrandimento usa `object-fit: contain` e scala in base al rapporto d'aspetto reale.
- L'artifact pubblicato ha un tetto di **16 MB**: budget ~15,2 MB per le miniature, dai la precedenza ai video e ai tier PROVEN/HOT, e dichiara nel deliverable quante creatività sono rimaste senza anteprima.
- Chi non ha media per natura (annunci di solo testo su Google) mostra un segnaposto **che dice perché**, non un riquadro vuoto.
- Il campo `image` di `competitor-data.json` contiene il `data:` URI; `image_full` / `media_full` conservano l'URL originale per riferimento.

### Lingua e forma — vale per ogni file prodotto
- **Scrivi nella lingua del brief.** Se il brief è in italiano, l'output è in italiano scritto da madrelingua: non una traduzione dall'inglese. Frasi con senso compiuto, vocabolario di chi fa marketing in Italia.
- **Gli accenti sono obbligatori** e sopravvivono alla scrittura del file: `può`, `più`, `così`, `già`, `perché`, `però`, `qualità`, `longevità`, `visibilità`. Scrivi i file in UTF-8; non passare mai il testo per una codifica ASCII che spoglia gli accenti.
- **Niente anglicismi non tradotti** dove l'italiano ha il termine: *insight* → intuizione/evidenza, *gap* → divario/buco, *awareness* resta solo dentro i nomi tecnici degli stadi, *ad copy* → testo dell'annuncio, *creative* → creatività, *longevity* → longevità.
- **I verbatim restano nella lingua originale**, fra virgolette e con la fonte: sono prove, non testo da tradurre.
- **Numeri all'italiana** nel testo (virgola decimale), ma **mai** dentro JSON, nomi di file o identificativi: un separatore delle migliaia infilato in un numero JSON o in un nome di creatività rompe il file.
- **Prima di pubblicare qualsiasi HTML**: valida gli script (`node -e "new Function(...)"` su ogni blocco `<script>`), controlla che nessuna stringa in apici singoli contenga un apostrofo dritto (usa `’`), e apri la pagina davvero — a schermo, non solo nel codice — per verificare che le immagini si vedano.

## Definition of done
- [ ] Entrambi i canali coperti, o il mancante dichiarato con motivo esplicito
- [ ] Ogni competitor in tabella ha tier + threat 1-5 **motivato**
- [ ] Messaging matrix taggata M/G/MG e sezione META vs GOOGLE chiusa
- [ ] 2-3 white space, ognuno con la prova di *perché* è libero (non "sembra libero")
- [ ] 1-2 angoli saturi, con chi li presidia e da quanti giorni
- [ ] Differenziatore raccomandato in **una** frase
- [ ] `competitor-data.json` parsabile (`python3 -c "import json;json.load(open('competitor-data.json'))"`)
- [ ] Ogni numero tracciabile a un file su disco; **troncamenti dello scrape dichiarati** (chi si ferma al cap non ha quel volume reale)
- [ ] Ogni annuncio nel deliverable visuale ha anteprima incorporata **o** link alla libreria ufficiale — e comunque il link
- [ ] Miniature cliccabili, ingrandimento con rapporto d'aspetto reale, verificato **aprendo la pagina nel browser**
- [ ] Nessun blocco `<script>` con errori di sintassi; accenti corretti in tutto il testo italiano

## Handoff
→ **Strategia** (white space + differenziatore = input diretto per posizionamento campagne)
→ **Concept creativi** (white space + angoli competitor PROVEN = base per concept e `23_competitor_rebuild`; teardown video `52_ad_spy_video` = base per rebuild di script/beat sheet)
→ **Produzione asset** (i format shell bancati in `03_Ad_Spy/_scratch/` sono la reference bank OBBLIGATORIA di `24_static_ads` — niente static senza di essa)
→ **Copy** (lo swipe file `03_Ad_Spy/` + teardown video alimentano `28_meta_copy`, analisi pattern/gap/hook)
