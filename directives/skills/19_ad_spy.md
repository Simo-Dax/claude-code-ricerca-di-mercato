# SA1 — Ad Spy 2.0 (Competitor Intelligence)

**Agente:** SA1 (Competitor Analysis)
**Output:** `03_Ad_Spy/adspy-[brand]-[YYYYMMDD].html` + `competitors.json` + `_scratch/format-*.json`+`.txt` (prompt di ricreazione per ogni creative unica, consumati da `24_static_ads`)
**Prerequisiti:** Apify API key configurata
**Reference:** `directives/skills/_shared/format_teardown_recreation.md` (fase EXTRACT)

---

## Tre modalità di input

| Input | Esempio | Modalità |
|---|---|---|
| Brand singolo | `Huel`, `facebook.com/huel` | brand mode → 1 swipe file |
| Lista brand | `Huel, Soylent, Ka'Chava` | list mode → N swipe file in parallelo |
| Nicchia/keyword | `protein powder`, `DTC skincare` | niche mode → ricerca 10 competitor → N swipe file |

---

## Step 0 — Token Apify (REST diretto, no MCP)

Questa skill scrapa il Meta Ad Library via **Apify REST API diretta** (allineato all'upstream v2.0: niente MCP — gli actor via MCP laggano). **Non chiamare alcun `mcp__apify__*`.**

```bash
# Token Apify dalla config locale (scritta da /pm-setup-apify), fallback su env
[ -f ~/.config/pm-agent/apify.env ] && . ~/.config/pm-agent/apify.env
TOKEN="${APIFY_TOKEN:-}"
```

Se `TOKEN` vuoto → chiedi di eseguire `/pm-setup-apify` e fermati. Token SEMPRE come header `Authorization: Bearer`, **mai in URL**. Non procedere senza token.

---

## Step 0.5 — Protezione cartella

```bash
PWD_ABS="$(pwd)"
TARGET="${PWD_ABS}"
PROTECTED=0
case "$PWD_ABS" in
  "$HOME"|"$HOME/"|"/"|"/tmp"|"/tmp/"|"$HOME/Downloads"|"$HOME/Desktop")
    PROTECTED=1 ;;
esac
if [ "$PROTECTED" = "1" ] && [ ! -d "$TARGET" ]; then
  echo "PROTECTED:$PWD_ABS"
elif [ ! -f "$TARGET/_meta/folder-confirmed.flag" ] && [ ! -d "$TARGET" ]; then
  echo "FIRSTRUN:$TARGET"
else
  mkdir -p "$TARGET/03_Ad_Spy" "$TARGET/_meta"
  echo "READY:$TARGET"
fi
```

Cattura il path risolto come `$WORKDIR`.

---

## Step 1 — Raccolta input

Chiedi:
1. **Cosa si vuole spiare?** (brand/lista/nicchia)
2. **Paese?** (default: US)
3. **Ads per brand?** (default: 30, max: 100)

**Nota disponibilità dati reach:** Meta pubblica reach solo per mercati EU/UK/BR. Per US/CA/AU il segnale principale è la durata di pubblicazione. Informa l'utente prima di iniziare.

---

## Step 2 — Risoluzione Page ID (OBBLIGATORIO per ogni brand)

Questo step non si salta mai. Ogni brand passa per il Pages scraper per ottenere il vero `pageAdLibrary.id`.

**2A — Modalità nicchia:** ricerca i top N competitor via subagent:
```
Ricerca i top {N} brand competitor nella nicchia "{nicchia}".
Restituisci SOLO array JSON: [{"name": "...", "fb_url": "https://facebook.com/..."}].
Verifica che ogni fb_url punti alla pagina ufficiale del brand.
```

**2B — Costruisci lista URL Facebook:**
```json
[{"url": "https://www.facebook.com/{slug}/"}]
```

**2C — Chiama Pages scraper Apify (REST diretto, no MCP):**
Il Facebook Pages scraper è **obbligatorio** per ogni brand: risolve il vero `pageAdLibrary.id` prima di scrapare gli ad, così i risultati sono garantiti dal brand esatto. Chiamata via REST (`apify~facebook-pages-scraper`), token in header `Authorization: Bearer`, mai in URL:
```bash
curl -sS -X POST "https://api.apify.com/v2/acts/apify~facebook-pages-scraper/run-sync-get-dataset-items?timeout=120" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{"startUrls":[{"url":"https://www.facebook.com/{slug}/"}]}'
```
(`${TOKEN}` risolto allo Step 0.) Non usare `mcp__apify__*`. Il Page ID da usare nello Step 4 è `pageAdLibrary.id` (campo nidificato, **diverso** dal `pageId` FB). Se uno slug torna errore "content isn't available" (pagina con slug sbagliato/ristretto), ritenta con slug alternativi (es. `7shifts` → `7shiftsinc`) prima di scartarlo.

---

## Step 3 — Validazione brand-lock

| Condizione | Verdetto | Azione |
|---|---|---|
| `error: not_available` | HARD FAIL | Pagina ristretta, chiedi URL diretto Ad Library |
| `error: no_items` | HARD FAIL | Slug inesistente |
| `categories` vuoto E `pageAdLibrary.id` assente | HARD FAIL | È un profilo personale, non una Page |
| `pageAdLibrary.id` presente + `categories` include "Page" | PASS | Procedi |
| `pageAdLibrary.id` presente MA title non corrisponde al brand | SOFT WARN | Chiedi conferma utente |

Salva brand validati in `$WORKDIR/03_Ad_Spy/competitors.json`.

---

## Step 4 — Dispatch agenti paralleli

**Actor:** `curious_coder~facebook-ads-library-scraper` (REST `run-sync-get-dataset-items`). **Un brand per chiamata** — `count` è un cap **globale** dell'actor: passare più URL in una sola run esaurisce il count sul primo brand. Quindi una chiamata curl per brand, lanciate in parallelo (`run_in_background`).

1. **Scraping** — URL brand-locked: `view_all_page_id={PAGE_ID}` (= il `pageAdLibrary.id` dallo Step 2C, mai bare slug). **NON mettere `media_type=image` nell'URL** → azzera i risultati (`ADS_NOT_FOUND`); il filtro statiche si fa in post (punto 3). Usa `active_status=all` (poi `is_active` distingue attivi/ritirati nello scoring).
```bash
curl -sS -X POST "https://api.apify.com/v2/acts/curious_coder~facebook-ads-library-scraper/run-sync-get-dataset-items?timeout=300" \
  -H "Authorization: Bearer ${TOKEN}" -H "Content-Type: application/json" \
  -d '{"urls":[{"url":"https://www.facebook.com/ads/library/?active_status=all&ad_type=all&country={COUNTRY}&search_type=page&view_all_page_id={PAGE_ID}"}],"count":{COUNT},"scrapeAdDetails":true}'
```
Campi utili per ad: `page_name`, `ad_archive_id`, `is_active`, `start_date`/`end_date` (unix), `collation_count` (= n_variants), `reach_estimate` (eu_reach, spesso null fuori EU), `publisher_platform`, `snapshot.{title,body.text,cta_text,display_format,images[].resized_image_url,cards[],videos}`.

2. **Controllo contaminazione** — verifica che tutti gli ads abbiano lo stesso `page_name`

3. **Filtro solo immagini statiche** — scarta gli ad con `snapshot.videos` o `display_format=VIDEO`. **DCO** (`display_format=DCO`): l'immagine sta in `snapshot.cards[].resized_image_url` (non in `images[]`); fallback `cards[].video_preview_image_url`. Tieni gli ad con almeno un'immagine statica.

4. **Scoring** (per mercati non EU/UK/BR — basato su durata, `reach_available=false`):
   - 🏆 PROVEN WINNER: `is_active=true` AND `days_running >= 60`
   - 🔥 HOT RUNNER: `is_active=true` AND `days_running >= 21`
   - ⚡ ACTIVE AD: `is_active=true` AND `days_running < 21`
   - ✅ RETIRED WINNER: `is_active=false` AND `days_running >= 60`
   - ⬜ SHORT RUN: `is_active=false` AND `days_running < 60`

   **Per mercati EU/UK/BR** (`aaa_info.eu_total_reach`/`uk_total_reach`/`br_total_reach` popolato su almeno un ad → `reach_available=true`): scoring blended reach + durata, così un lancio nuovo ad alto reach e un vecchio slow-burner emergono entrambi:
   - 🏆 PROVEN WINNER: `is_active=true` AND (`reach >= 100000` OR `days_running >= 60`)
   - 🔥 HOT RUNNER: `is_active=true` AND (`reach >= 25000` OR `days_running >= 21`)
   - ⚡ ACTIVE AD: `is_active=true` AND `reach < 25000` AND `days_running < 21`
   - ✅ RETIRED WINNER: `is_active=false` AND (`reach >= 100000` OR `days_running >= 60`)
   - ⬜ SHORT RUN: `is_active=false` AND `reach < 100000` AND `days_running < 60`

   Dentro ogni tier, ordina per reach desc poi days_running desc. **Non fabbricare mai reach** — se `aaa_info` è null anche in un run `reach_available=true` (capita ad-per-ad), tratta quell'ad come i mercati non-EU (solo durata) e non stimare/interpolare dai fratelli.

   Nella card HTML di ogni ad EU/UK/BR con `aaa_info` popolato, aggiungi un blocco "🎯 Audience & Reach": reach formattato con separatore migliaia + regione (`"1.240.000 (EU)"`), fascia età dominante e skew di genere da `age_country_gender_reach_breakdown[]`, top country. Quando `reach_available=false` per l'intero brand, mostra un banner in testa allo swipe file: *"Meta non pubblica reach/impression per ads commerciali fuori da EU/UK/Brasile. Il segnale winner più forte per questo brand è la durata di pubblicazione + stato attivo."* — mai ometterlo, evita che l'utente pensi che lo scrape sia fallito.

5. **Analisi** — per ogni 🏆/🔥/⚡: 2-3 frasi su hook, awareness level, perché funziona

6. **Download immagini base64 + su disco:**
```bash
mkdir -p "$WORKDIR/03_Ad_Spy/_creatives/{SLUG}"
curl -s -L --max-time 10 \
  -H "User-Agent: Mozilla/5.0..." \
  -H "Referer: https://www.facebook.com/" \
  -o "$WORKDIR/03_Ad_Spy/_creatives/{SLUG}/{ad_archive_id}.jpg" \
  "<URL>"
```
Salva **sia** il base64 inline per la card HTML **sia** il file su disco (`_creatives/{SLUG}/{ad_archive_id}.jpg`) — il file locale è l'unico input ammesso per il teardown allo step 6.5 (gli URL Ad Library scadono in pochi giorni, non sono mai fonte di teardown). Tieni una mappa `ad_images[ad_archive_id] = base64` viva fino allo Step 7 (la card HTML la legge).

6.5. **Teardown a livello di ricreazione (ogni creative unica trovata)** — reverse-engineering, non riassunto: ogni ad statico scrapato diventa un prompt che, incollato senza immagine di riferimento, rigenera essenzialmente lo stesso ad.
   - **Dedup + split.** Normalizza headline+body (lowercase, spazi collassati); solo la prima occorrenza di ogni creative unica procede, le altre righe si registrano come duplicati. Ordina le unique in ordine tier (🏆→🔥→⚡→✅→⬜), tetto **40 unique per brand per run** (oltre è coda a basso segnale).
   - **In-agente vs backlog.** Le unique con card nel report (fino a 20) si torn-down QUI, in questo agente. Le unique restanti (fino a 20 in più, nessuna card) vanno in una lista `teardown_backlog` nel JSON di ritorno — l'orchestratore le smaltisce in un'ondata parallela separata dopo che tutti gli agenti brand sono tornati (batch da 10 creative, stesso brand, max 5 agenti in volo, un solo messaggio).
   - **Skip se già bancato.** Se `$WORKDIR/03_Ad_Spy/_scratch/format-{SLUG}-{ad_archive_id}-*.json` esiste già a `schema_version: 2`, non ri-fare il teardown: leggi il `condensed_prompt` esistente da disco e popola comunque `shells[ad_archive_id]` per lo Step 7.
   - **Esegui F-EXTRACT.** Carica `directives/skills/_shared/format_teardown_recreation.md`, esegui F.2-F.6 sul jpg locale appena scaricato (mai sull'URL), passando headline/body/cta/tier/analisi dello step 5 come variabili. Scrivi `format-{SLUG}-{ad_archive_id}-{YYYYMMDD}.json` + `.txt` in `$WORKDIR/03_Ad_Spy/_scratch/`, `status: extracted_not_transformed`, `created_by_skill: "19_ad_spy"`.
   - **Self-check** (F.6): JSON riparsa, `condensed_prompt_char_count` ≤3800 e reale, ogni stringa del `verbatim_text_ledger` presente nel `condensed_prompt` (scansione whitespace-normalizzata), zero trattini lunghi. Fallito due volte → droppa lo shell, registra in `teardown_failures`.
   - **Mappa in memoria:** `shells[ad_archive_id] = {file, condensed_prompt}` — la card HTML dello Step 7 legge da qui; i duplicati risolvono tramite lo shell dell'originale.

7. **Build HTML** — **stesso Design System chiaro navy/slate di `18_voc_research`/`52_ad_spy_video`** (non più dark theme — allinea i 3 deliverable HTML come stessa famiglia prodotto):
   - CSS variabili: `--bg:#FFFFFF; --surface-light:#F4F6FA; --surface-mid:#E8EDF5; --navy:#162441; --slate:#8A9BBC; --body:#2C3E50; --green:#1A6B3C (winner/reach); --blue:#1A5276 (link/info); --amber:#8B6914 (disclaimer)`. Font: system sans-serif stack (`-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif`).
   - Header navy pieno (`--navy` bg, testo bianco): brand + kicker + sub-riga con conteggio ads/winner + reach totale (o "reach n/a" se `reach_available=false`, vedi Step 4.4).
   - Barra filtri sticky, sfondo bianco, bordo `--surface-mid`: filtro per tier badge, per formato, per stato attivo/ritirato.
   - Card grid su sfondo bianco, ogni card `--surface-light` bg, bordo 1px `--surface-mid`, badge tier in alto (colori: PROVEN/RETIRED = `--green`, HOT = `--amber`, ACTIVE = `--blue`, SHORT = `--slate`), immagine statica inline base64, copy sotto (headline/body/CTA), blocco Audience & Reach quando disponibile (Step 4).
   - Banner disclaimer reach (Step 4) quando `reach_available=false`, in testa alla pagina, `--amber` come da Step 4.
   - Bottom row card: bottone **[📋 Copy for /rebuild]** + bottone **[📐 Copy format shell]** (renderizza SOLO quando `shells[ad_archive_id]` esiste — copia il `condensed_prompt` verbatim con l'header "PROMPT DI RICREAZIONE, rigenera l'ad sorgente com'è, esegui `24_static_ads` per ribrandizzarlo prima di spedirlo") + data di inizio.
   - Ogni voce `ADS_DATA` porta anche `format_shell` (il `condensed_prompt` dello shell bancato — un duplicato eredita quello dell'originale) e `shell_file` (il nome file in `_scratch/`); entrambi null solo se il teardown di quell'ad è fallito il self-check.
   - Self-contained: zero `<link>`/`<script src>` esterni, tutto CSS inline + immagini base64.

8. **Salva** in `$WORKDIR/03_Ad_Spy/adspy-{SLUG}-{YYYYMMDD}.html`

---

## Step 4.7 — Ondata teardown backlog (banca le creative oltre il report)

Dopo che tutti gli agenti brand sono tornati, raccogli il `teardown_backlog` di ogni brand. Se tutti i backlog sono vuoti, salta allo Step 5. Altrimenti:

> 📐 Sto bancando altri {TOTALE} format shell oltre le card del report ({N} brand). Gira in background mentre preparo il riepilogo.

Dispatch: **un agente per batch di massimo 10 voci backlog, tutte dello stesso brand, massimo 5 agenti in volo, un solo messaggio parallelo.** Ogni agente carica `directives/skills/_shared/format_teardown_recreation.md`, esegue F-EXTRACT (F.2-F.6) sull'immagine locale già salvata in `_creatives/{SLUG}/{ad_archive_id}.jpg` (mai un URL), scrive `format-{SLUG}-{ad_archive_id}-{YYYYMMDD}.json`+`.txt`, stesso self-check dello Step 6.5. Un agente morto/in timeout non è un fallimento del run: le sue voci si registrano come `teardown_failures`.

Merge dei `written`/`failures` di ogni agente nel record del brand prima di stampare lo Step 5.

---

## Step 5 — Riepilogo aggregato

```
✅ Ad Spy 2.0 Completato

Brand verificati e processati:
  🏆 Ka'Chava   → 28 ads, 12 winner, 24 format shell bancati → adspy-kachava-{data}.html
  🏆 Huel       → 18 ads, 7 winner, 18 format shell bancati → adspy-huel-{data}.html

Non brand-lockati:
  ❌ Brand X — pagina ristretta. Vuoi riprovare? Incolla l'URL Ad Library.

I format shell sono bancati per ogni ad del report — esegui 24_static_ads per ribrandizzarli sul tuo brand (design tenuto, identità scambiata). Ogni card ha anche un bottone 📐 per vedere il prompt strutturale grezzo. Prossimo step (rebuild singolo): apri un 🏆 → clicca "📋 Copy for /pm-competitor-rebuild" → esegui skill 23_competitor_rebuild
```

Unisci la lista teardown di ogni brand (shell del report + shell backlog, nuovi e riusati) dentro il relativo record in `competitors.json` come unione deduplicata, mai una sostituzione — gli shell dei run precedenti non si perdono mai.

---

## Step 6 — Export dati per Competitor Ads Dashboard

Oltre allo swipe file HTML, emetti `data.json` per la dashboard esterna (`output/dashboard/competitor-ads/`).

Per ogni ad nello swipe file, classifica via LLM (analizzando creative + copy):
- **`awareness_stage`** — uno dei 5 stadi Eugene Schwartz: `Unaware` / `Problem-Aware` / `Solution-Aware` / `Product-Aware` / `Most-Aware`
- **`funnel_stage`** — `TOF` (awareness/educazione) / `MOF` (consideration/proof) / `BOF` (offerta/prezzo/urgency)

Mappa il badge scoring al campo `tier`: PROVEN / HOT / ACTIVE / RETIRED / SHORT.

Estrai i KPI realmente disponibili da Meta Ad Library (ads commerciali): `days_active`, `eu_reach` (se presente, altrimenti `null`), `n_variants`, `first_seen`, `platforms`, `format`, `ad_library_url`. **NON** inventare impressions/spend/CTR — non esistono per ads commerciali.

Scrivi `$WORKDIR/03_Ad_Spy/data.json` seguendo lo schema in `output/dashboard/competitor-ads/data.sample.json`. L'utente copia questo file in `output/dashboard/competitor-ads/data.json` e apre `index.html` (o lo deploya su Netlify/Cloudflare/Vercel).

**La dashboard deve mostrare le creative vere, non dei riquadri vuoti.** Perciò, per ogni riga:

- **`image` = `data:` URI**, generato dal file locale in `_creatives/` (miniatura ridimensionata, lato lungo ~600px, JPEG qualità ~72). Mai il link alla CDN di Meta: scade in poche ore e la CSP degli artifact blocca comunque le immagini esterne.
- **`ratio`** = larghezza/altezza reale della creative: è ciò che permette alla scheda annuncio di aprirla nel suo formato originale invece di deformarla.
- **`media_kind`** = `image` | `video` | `text` | `none`. Per i video: `image` è il **primo fotogramma** estratto con ffmpeg e `media_full` è l'URL del video, che la scheda offre come "Apri il video originale".
- **`url`** = link all'annuncio nella libreria ufficiale. **Obbligatorio su ogni riga**: se l'anteprima non c'è, il link resta l'unica prova verificabile.
- **`body`** = copy verbatim, senza parafrasi; `cta` come appare nell'annuncio.

Prima di consegnare: apri `index.html` nel browser, controlla che le anteprime carichino, che una card si apra al clic e che i conteggi dei filtri cambino. Un HTML consegnato senza averlo aperto non è consegnato.

---

## Regole critiche

- **Pages scraper obbligatorio** per ogni brand, nessuna eccezione
- **Mai token Apify in URL** — sempre `Authorization: Bearer` header
- **Solo immagini statiche** — scarta tutto con video
- **`scrapeAdDetails: true` obbligatorio** — senza, i dati sono incompleti
- **Controllo contaminazione in ogni agente** — mix di page_name = fallimento, nessuno swipe file
- **Dispatch parallelo in UN SOLO messaggio**
- **Teardown solo da immagine locale** — mai da URL Ad Library (scadono in giorni); il file in `_creatives/` è l'unico input ammesso per il format shell
- **Un prompt di ricreazione non è un ad da spedire** — è intelligence + base di rebrand; `status` resta `extracted_not_transformed` per sempre, solo `24_static_ads` lo ribrandizza
- **Shell immutabili** — mai sovrascrivere un `format-*.json` esistente; un redo scrive un nuovo file con suffisso `-2`

---

## Definition of done
- [ ] Ogni brand risolto via Pages scraper → `pageAdLibrary.id` (brand-lock verificato, zero ad di omonimi)
- [ ] **Zero ads dichiarato come dato, non come errore.** Se un competitor non ha ads su Meta, verifica con una seconda ricerca per keyword nella Ad Library (GB *e* US) e scrivi «nessuna presenza Meta rilevata al {data}, verificato per keyword su {geo}». Un buco non verificato è un buco nella ricerca, non nel mercato del competitor.
- [ ] **Troncamenti dichiarati.** Un brand che si ferma al cap (`limitPerSource`) non ha quel volume reale: va marcato «troncato» ovunque compaia un conteggio, altrimenti il confronto fra competitor è falso.
- [ ] Tier assegnati su `days_running` reale (🏆 ≥60gg / 🔥 ≥21gg / ⚡ <21gg / ✅ RETIRED / ⬜ SHORT RUN), non su stime
- [ ] Mediana **e** massimo di longevità per brand (la sola media nasconde la coda lunga che è il segnale vero)
- [ ] Format shell bancati in `03_Ad_Spy/_scratch/format-*.json` — bloccante per `24_static_ads`
- [ ] Export `data.json` dashboard con `source:"meta"`, `url` e `image` come `data:` URI su ogni riga — dashboard aperta nel browser e verificata (anteprime, apertura card, filtri)
- [ ] Il report dice cosa Meta **non** copre: è il canale dell'interruzione, non dell'intento. Senza `62_google_ads_spy` la lettura è mezza cieca e va detto in testa al file.
