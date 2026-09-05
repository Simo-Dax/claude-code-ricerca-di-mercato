# SA1 — Ad Spy Video (Competitor VIDEO-Ad Teardown)

**Agente:** SA1 (Competitor Analysis)
**Output:** `03_Ad_Spy/<slug>-video/video-teardown-<slug>-<YYYYMMDD>.html` + `.json`
**Prerequisiti:** Apify API key (`/pm-setup-apify`), fal.ai API key (`/pm-setup-fal-ai`)

Sorella video di `19_ad_spy` (che copre **solo** static image ads). Dove `19_ad_spy` produce uno swipe file, questa skill produce, per ogni video competitor, un **teardown strutturato**: script word-for-word, on-screen text, hook, beat sheet, scene-by-scene, CTA. **Pura intelligence — non genera mai ad nuove.** La trasformazione in ad nuova è compito a valle di `23_competitor_rebuild`.

---

## Tre cose che la definiscono

1. **Stesso brand-lock di `19_ad_spy`** — Pages scraper obbligatorio (`pageAdLibrary.id`), mai bare slug.
2. **Video, non static** — scrape con `media_type=all`, poi filtro solo video in post.
3. **Trascrizione + lettura frame via fal.ai** (non locale) — coerente con l'architettura MCP-propri del progetto: niente venv/whisper locale da installare, si usa il fal.ai già configurato nel progetto.

---

## Step 0 — Token Apify + fal.ai (REST diretto, no MCP Apify)

```bash
[ -f ~/.config/pm-agent/apify.env ] && . ~/.config/pm-agent/apify.env
TOKEN="${APIFY_TOKEN:-}"
```
Se `TOKEN` vuoto → chiedi `/pm-setup-apify` e fermati. Token sempre come header `Authorization: Bearer`, mai in URL, mai `mcp__apify__*`.
Verifica anche che il fal.ai MCP sia configurato (`/pm-setup-fal-ai`) — serve per trascrizione (Step 6).

## Step 0.5 — Protezione cartella

Identico a `19_ad_spy` Step 0.5. Cattura `$WORKDIR`. Per ogni competitor, artefatti in `$WORKDIR/03_Ad_Spy/<slug>-video/`: `videos/` (mp4), `frames/` (hook + contact sheet), `transcripts/` (json), più `video-teardown-<slug>-<data>.json`/`.html`.

## Step 0.6 — ffmpeg locale (solo estrazione frame)

```bash
command -v ffmpeg >/dev/null || { echo "Installa ffmpeg (brew install ffmpeg) e rilancia."; exit 1; }
```
Nessun venv Python/whisper locale: la trascrizione passa da fal.ai (Step 6). ffmpeg serve solo per i frame (Step 7).

---

## Step 1 — Intake

Stesse 3 modalità input di `19_ad_spy` (brand singolo / lista / nicchia). Chiedi anche:
1. **Paese?** (default US; EU/UK/BR = reach+audience disponibili, vedi Step 1.5)
2. **Max video per brand?** (default 30, min 10 — floor actor, max 120)
3. **Filtro payer?** (opzionale — tieni solo ad pagate dalla pagina stessa, scarta quelle pagate per conto di altri brand)

Un URL Ad Library con `view_all_page_id=<id>` è accettato direttamente (skip Step 2-3, brand già lockato).

### Step 1.5 — Disponibilità reach
Identico a `19_ad_spy`: reach + audience solo per ad EU/UK/BR (o political/issue ovunque). Fuori da quei mercati: solo creative, copy, date, formato, piattaforme — il segnale winner più forte è durata + still-active. Informa l'utente, non bloccare.

## Step 2 — Risoluzione Page ID (OBBLIGATORIO)

Identico a `19_ad_spy` Step 2 (Pages scraper REST, `pageAdLibrary.id`).

## Step 3 — Validazione brand-lock

Identica tabella di `19_ad_spy` Step 3 (HARD FAIL / SOFT WARN / PASS).

---

## Step 4 — Scraping ad (VIDEO, brand-locked, REST)

Stesso actor di `19_ad_spy` (`curious_coder~facebook-ads-library-scraper`), **due differenze: `media_type=all`, niente `media_type=image`, e filtro video in post**. Per count alti (>60), usa async run + poll invece di `run-sync-get-dataset-items` (che può andare in timeout oltre i 100 ad):

```bash
cat > /tmp/vspy-{SLUG}.json <<JSON
{"urls":[{"url":"https://www.facebook.com/ads/library/?active_status=all&ad_type=all&country={COUNTRY}&search_type=page&media_type=all&view_all_page_id={PAGE_ID}"}],
 "count":{COUNT},"scrapeAdDetails":true,"scrapePageAds.activeStatus":"active","scrapePageAds.sortBy":"impressions_desc"}
JSON

RUN=$(curl -sS -X POST "https://api.apify.com/v2/acts/curious_coder~facebook-ads-library-scraper/runs" \
  -H "Authorization: Bearer ${TOKEN}" -H "Content-Type: application/json" -d @/tmp/vspy-{SLUG}.json)
RUN_ID=$(echo "$RUN" | python3 -c "import sys,json;print(json.load(sys.stdin)['data']['id'])")
DS_ID=$(echo "$RUN" | python3 -c "import sys,json;print(json.load(sys.stdin)['data']['defaultDatasetId'])")
for i in $(seq 1 9); do
  ST=$(curl -sS "https://api.apify.com/v2/actor-runs/${RUN_ID}?waitForFinish=60" -H "Authorization: Bearer ${TOKEN}" \
        | python3 -c "import sys,json;print(json.load(sys.stdin)['data']['status'])")
  echo "poll $i: $ST"; case "$ST" in SUCCEEDED|FAILED|ABORTED|TIMED-OUT) break;; esac
done
curl -sS "https://api.apify.com/v2/datasets/${DS_ID}/items?clean=true&format=json" \
  -H "Authorization: Bearer ${TOKEN}" -o "$WORKDIR/03_Ad_Spy/{SLUG}-video/all-ads-raw.json"
```

### 4A — Controllo contaminazione
Tutti gli ad devono condividere un `page_name`. Mix → abort, riporta.

### 4B — Solo VIDEO
Tieni un ad se `snapshot.display_format == "VIDEO"` OR `snapshot.videos` non vuoto OR un `snapshot.cards[]` ha `video_hd_url`/`video_sd_url`. Scarta immagine/carousel-immagine (competenza di `19_ad_spy`). Carousel con card video → tieni, tratta ogni card video come clip separata.

### 4C — Filtro payer (opzionale)
Se richiesto: leggi `aaa_info.payer_beneficiary_data[0].payer`. Tieni solo ad dove `payer == page_name`. Senza disclosure EU (`payer` null): classifica per dominio di `snapshot.caption`/`link_url` — destinazione propria = loro, dominio di un altro brand = scarta. Riporta lo split.

Prima di procedere, esporta le variabili che ogni blocco Python da qui in poi legge da `os.environ`:
```bash
export VDIR="$WORKDIR/03_Ad_Spy/{SLUG}-video" BRAND="<nome brand>" SLUG="{SLUG}" DATE="$(date -u +%Y-%m-%d)"
```

```python
import json, os
B = os.environ["VDIR"]
ads = json.load(open(f"{B}/all-ads-raw.json"))
keep = [a for a in ads if (a.get("snapshot") or {}).get("display_format") == "VIDEO" or (a.get("snapshot") or {}).get("videos")]
json.dump(keep, open(f"{B}/videos-kept.json", "w"))
print(len(keep), "video ad tenuti")
```

---

## Step 5 — Download video

```python
import json, os, subprocess
from concurrent.futures import ThreadPoolExecutor
BASE=os.environ["VDIR"]; os.makedirs(f"{BASE}/videos", exist_ok=True)
ads=json.load(open(f"{BASE}/videos-kept.json"))
UA="Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36"
def url(a):
    s=a.get("snapshot") or {}
    for v in (s.get("videos") or []):
        if v.get("video_hd_url") or v.get("video_sd_url"): return v.get("video_hd_url") or v.get("video_sd_url")
    for c in (s.get("cards") or []):
        if c.get("video_hd_url") or c.get("video_sd_url"): return c.get("video_hd_url") or c.get("video_sd_url")
def dl(a):
    u=url(a); fp=f"{BASE}/videos/{a['ad_archive_id']}.mp4"
    if not u or (os.path.exists(fp) and os.path.getsize(fp)>20000): return
    subprocess.run(["curl","-s","-L","--max-time","150","-H",f"User-Agent: {UA}","-H","Referer: https://www.facebook.com/","-o",fp,u],timeout=170)
list(ThreadPoolExecutor(max_workers=6).map(dl, ads))
print("scaricati:", len([f for f in os.listdir(f'{BASE}/videos') if f.endswith('.mp4')]))
```

---

## Step 6 — Trascrizione word-for-word (fal.ai Whisper, no locale)

Per ogni mp4: `mcp__fal-ai__upload` (ottieni URL) → `mcp__fal-ai__generate` con `app_id: "fal-ai/whisper"`, `input_data: {"audio_url": "<url>", "chunk_level": "segment"}` → poll `mcp__fal-ai__result` finché pronto. Il video stesso può essere passato come `audio_url` (fal estrae la traccia audio).

Salva per ogni ad `transcripts/{ad_id}.json`:
```json
{"ad_id":"","language":"","text":"","music_only":false,"segments":[{"start":0,"end":0,"text":""}]}
```
**Un `chunks` vuoto/assente = ad music-only.** Non re-invocare per "recuperare" parlato su silenzio — il modello allucinerebbe. Segna `music_only: true`.

## Step 7 — Estrazione frame (hook + contact sheet)

Identico script upstream: un frame hook ad alta risoluzione (~1.2s, o 12% della durata) e un contact sheet 4×4 sull'intera durata, per ogni video.

```python
import os, glob, subprocess
BASE=os.environ["VDIR"]; os.makedirs(f"{BASE}/frames", exist_ok=True)
def dur(mp4):
    r=subprocess.run(["ffprobe","-v","error","-show_entries","format=duration","-of","default=nw=1:nk=1",mp4],capture_output=True,text=True)
    try: return float(r.stdout.strip())
    except Exception: return 0.0
for mp4 in sorted(glob.glob(f"{BASE}/videos/*.mp4")):
    aid=os.path.splitext(os.path.basename(mp4))[0]; d=dur(mp4)
    ht=min(1.2,max(0.3,d*0.12)) if d else 0.5
    subprocess.run(["ffmpeg","-y","-ss",str(ht),"-i",mp4,"-frames:v","1","-vf","scale=560:-1",f"{BASE}/frames/{aid}_hook.jpg"],capture_output=True)
    n=min(16,max(4,round(d/2))) if d else 8; fps=n/d if d>0 else 0.5
    subprocess.run(["ffmpeg","-y","-i",mp4,"-vf",f"fps={fps},scale=300:-1,tile=4x4","-frames:v","1",f"{BASE}/frames/{aid}_sheet.jpg"],capture_output=True)
print("frame estratti:", len(glob.glob(f'{BASE}/frames/*_hook.jpg')))
```

---

## Step 8 — Teardown per video (agenti paralleli, model: sonnet)

Prima del dispatch, scrivi `{BASE}/meta/{aid}.json` per ogni ad (Tier-4 source, evita di far leggere agli agenti il raw multi-MB): `{ad_archive_id, payer, page_name, publisher_platform, start_date_formatted, end_date_formatted, days_active, still_active, eu_total_reach, age_audience, gender_audience, location_audience, headline(title), primary_text(body.text), link_description, cta_text, caption, link_url}`.

**Dispatch:** `subagent_type: general-purpose`, batch di **4 video/agente**, TUTTI i batch in **un solo messaggio** (parallelo vero). ~39K token/video su sonnet, piatto rispetto alla durata.

### Prompt agente
```
Sei un estrattore di ad-intelligence competitor per la skill Ad Spy Video. Per OGNI ad_id nel batch, produci UN record di teardown strutturato (Tier 1 + Tier 4). Pura intelligence — DESCRIVI l'ad fedelmente. Non scrivere una nuova ad, non dare istruzioni di build/generazione.

Per ogni ad_id, leggi:
- transcripts/{ad_id}.json   (word-for-word, timestamp; flag music_only)
- meta/{ad_id}.json          (Tier-4: identità, reach, audience, copy)
- frames/{ad_id}_hook.jpg    (frame hook alta risoluzione)
- frames/{ad_id}_sheet.jpg   (contact sheet 4x4 cronologico, ignora tile neri)

Emetti per ogni ad (Tier 1 = teardown, Tier 4 = identità/performance):

TIER 1
- vo_script_timed: [{start_s,end_s,line}] verbatim dal transcript. Se music_only, [].
- onscreen_text_track: [{order,text_verbatim,approx_start_s,role}] letto dai frame, in ordine.
- hook: {hook_type,verbatim_spoken,verbatim_onscreen,scroll_stop_device,delivery,duration_s,time_to_first_word_s,first_frame_description}
- beat_sheet: [{beat_id,beat_type,start_s,end_s,function_summary,key_line_verbatim}] hook→...→CTA
- scene_breakdown: [{scene_index,start_s,end_s,shot_type,framing,setting,subjects,scene_description,vo_line,onscreen_text,transition}]
- cta: {present,action,verbatim_spoken,verbatim_onscreen,placement,repetitions}

TIER 4 (da meta/{ad_id}.json; passthrough verbatim, inferenza leggera solo per target_avatar)
- ad_identity: {ad_archive_id,payer,page_name,publisher_platform,destination,link_url}
- winner_signal: {eu_reach,audience{age,gender,geo},days_active,still_active}
- target_avatar: {age_range,gender_skew,geo,identity_labels[],pain_state,callout_phrase_verbatim}
- copy_payload: {primary_text_verbatim,headline_verbatim,link_description_verbatim,cta_verbatim,destination}

REGOLE: verbatim dove indicato; mai inventare; frame illeggibile → "unknown"; music_only → notalo nella delivery dell'hook. Restituisci SOLO un array JSON di record, senza commenti né code fence.
```

Costo: ~39K token/video su sonnet. Per tagliare, `scene_breakdown` è il campo più pesante — omettilo se serve solo script+hook+beat+CTA.

---

## Step 9 — Assemblaggio output

Ordina per `winner_signal.eu_reach` desc (fallback `days_active`). Due file, mai Markdown:
1. `video-teardown-<slug>-<data>.json` — array completo, artefatto machine-readable per step a valle.
2. `video-teardown-<slug>-<data>.html` — dossier self-contained (CSS inline, frame hook base64, zero dipendenze esterne), stessa palette chiara navy/slate dello swipe file `19_ad_spy`/VOC. Una `.ad` card per video, ordine per reach. Costruisci deterministicamente con lo script (adatta path/variabili d'ambiente `VDIR/BRAND/SLUG/DATE`), non scrivere le card a mano.

Struttura card per video: header (`#rank · reach/giorni attivo · durata · hook_type`), meta (ad id + link Ad Library + destinazione + still_active), thumb (frame hook o placeholder "No frame"), poi: hookline, script word-for-word timestampato (o badge "♪ solo musica"), beat sheet, scene-by-scene, on-screen text, CTA, copy (headline + primary text). Footer con stat aggregate (video totali, con/senza voiceover, durata media/mediana, piattaforme).

Valida: n. card == n. video tenuti == n. record JSON; nessun `{PLACEHOLDER}`/`None`/`null` letterale nei campi; ogni record ha identità + almeno hook o music_only; l'HTML si apre offline con ogni frame hook visibile.

## Step 10 — Riepilogo

```
✅ Ad Spy Video completato

Brand verificati:
  Ka'Chava → 24 video (18 con voiceover, 6 music-only), 4.2M reach EU → video-teardown-kachava-{data}.html

Non brand-lockati:
  ❌ Brand X — pagina ristretta.

Questa è pura intelligence: apri l'HTML per leggere i teardown, passa il JSON a `23_competitor_rebuild` per costruire ad nuove.
```

---

## Regole critiche

| Regola | Dettaglio |
|---|---|
| **Pura intelligence, mai generare** | Scrape + descrivi + registra metriche. Mai scrivere ad, prompt o manifest di build. |
| **REST diretto, mai MCP Apify** | Ogni chiamata Apify è `curl`, token in header `Authorization: Bearer`, mai in URL. Mai `mcp__apify__*`. |
| **Solo URL brand-locked** | Scraping con `view_all_page_id={PAGE_ID}`. Pages scraper obbligatorio. Bare slug vietato. |
| **Solo video** | `media_type=all` + filtro post a video. Immagine/carousel-immagine = competenza `19_ad_spy`. |
| **Trascrizione via fal.ai, non locale** | `fal-ai/whisper` (`chunk_level: "segment"`), niente venv/whisper locale da installare. |
| **Frame via ffmpeg locale** | Unica dipendenza locale reale, solo per hook frame + contact sheet. |
| **Model teardown = sonnet** | Agenti per-video su sonnet, batch 4, dispatch in un solo messaggio. |
| **Music-only è reale** | Transcript vuoto = ad senza voiceover. Non fabbricare parlato. |
| **Mai inventare** | Verbatim dove indicato; illeggibile/mancante → "unknown". Reach null fuori EU/UK/BR — dillo, non inventarlo. |
| **Parallelo = un solo messaggio** | Tutti i batch di teardown agent dispatchati insieme. |

---

## Definition of done
- [ ] Solo VIDEO nel dataset finale (statiche filtrate in post, non solo via parametro)
- [ ] Ogni video con trascrizione **word-for-word** da Whisper, mai riassunta o ricostruita a memoria
- [ ] Hook (primi 3 secondi) isolato e citato verbatim per ogni video
- [ ] Beat sheet timestampato + on-screen text separato dal parlato
- [ ] Video non scaricabili o non trascrivibili elencati con il motivo, non omessi
- [ ] Zero presenza video di un competitor = dato dichiarato (non compra video su Meta), verificato
- [ ] Nessun ad generato: questa skill è intelligence, la produzione è una fase a valle
