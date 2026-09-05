# SA1 — UGC Scraper 2.0 (TikTok Viral Research)

**Agente:** SA1 (Competitor Analysis)
**Output:** `05_UGC/scraper/[niche-slug]/ugc-winners-v2-[YYYY-MM-DD].txt`
**Prerequisiti:** Apify API key configurata
**Costo per run:** ~$0.056 (6 query scraptik + 25 trascrizioni)

---

## Input richiesti

```
1. Documento VOC — carica o incolla. Usato per costruire le 6 query e validare la rilevanza.
2. Prodotto o nicchia — es. "AI ad generator per DTC founders", "siero retinolo"

Opzionali:
- Paese (default: US)
- Query personalizzate (se non fornite, le genero dal VOC)
```

Se il VOC non è fornito, chiedi. Non inventare mai contenuto VOC.

**Auto-discovery:** prima di chiedere il VOC, controlla se esiste già:
```bash
ls -t "$WORKDIR/01_VOC_Research/"*.html "$WORKDIR/01_VOC_Research/"*.md 2>/dev/null | head -n 1
```

---

## Step 1 — Leggi token Apify

```bash
# Token Apify dalla config locale (scritta da /pm-setup-apify), fallback su env
[ -f ~/.config/pm-agent/apify.env ] && . ~/.config/pm-agent/apify.env
TOKEN="${APIFY_TOKEN:-}"
```

Se TOKEN vuoto → chiedi di eseguire `/pm-setup-apify`. Token SEMPRE come `Authorization: Bearer` header.

---

## Step 2 — Costruisci 6 query dal VOC

Leggi il VOC ed estrai 6 query che coprono questi slot:

| Slot | Tipo | Esempio |
|---|---|---|
| 1 | Dolore in linguaggio cliente | "ads stopped working" |
| 2 | Soluzione/workflow AI | "ai ads that actually work" |
| 3 | Identità ICP | "dtc founder meta ads" |
| 4 | Problem-aware grezzo | "meta ads broken" |
| 5 | Workflow/how-to | "ai ugc ads tutorial" |
| 6 | Fiducia peer/formato | "media buyer day in the life" |

Query: 2-4 parole. Mostra le 6 query all'utente e chiedi conferma prima di procedere.

---

## Step 3 — Scraping via Apify REST API (scraptik)

**USA SEMPRE la REST API direttamente** (non MCP tools — laggano).

Prepara input per ogni query (scrivi in `/tmp/ugc-scrape/in-{i}.json`):
```json
{
  "searchPosts_keyword": "<query>",
  "searchPosts_count": 20,
  "searchPosts_publishTime": 90,
  "searchPosts_sortType": 1,
  "searchPosts_region": "US"
}
```

**Parametri critici:**
- `searchPosts_sortType: 1` = Most Liked (SEMPRE, mai 0=Relevance)
- `searchPosts_publishTime: 90` = ultimi 90 giorni (OBBLIGATORIO)

Lancia 6 query in parallelo:
```bash
for i in 0 1 2 3 4 5; do
  curl -sS -X POST "https://api.apify.com/v2/acts/scraptik~tiktok-api/run-sync-get-dataset-items?timeout=120" \
    -H "Authorization: Bearer ${TOKEN}" \
    -H "Content-Type: application/json" \
    -d @"/tmp/ugc-scrape/in-${i}.json" \
    -o "/tmp/ugc-scrape/out-${i}.json" &
done
wait
```

**Schema risposta:** dati sono in `[0].search_item_list[].aweme_info` (MAI `aweme_list`, è sempre vuoto).

Deduplica per `aweme_info.aweme_id`. Rendimento tipico: 120 raw → 100-115 unici.

---

## Step 4 — Filtro hard + scoring

**Filtro hard** — scarta se:
- views < 10.000
- durata < 5s o > 180s
- shares == 0 E comments < 5
- channel/username mancante
- uploadedAt == 0 o età > 90 giorni

**Formula scoring:** `FinalScore = ViewPower × CreatorUnderdog × EngagementQuality × Recency`

**Follower floor:** tag breakout solo se `views/followers >= 50 AND followers >= 100`.

Tieni top 80 per il vetting LLM.

---

## Step 5 — Vetting rilevanza LLM (OBBLIGATORIO)

Per ogni candidato, valuta 0-10 quanto il titolo (caption) + hashtag corrispondono alla nicchia VOC:

| Score | Significato |
|---|---|
| 10 | Perfetto — direttamente sulla nicchia esatta |
| 8-9 | Molto vicino — topic adiacente, audience giusta |
| 6-7 | Tangenziale — dominio giusto ma non ICP specifico |
| 4-5 | Debole — keyword match ma contesto sbagliato |
| 1-3 | Off-topic |
| 0 | Completamente non correlato |

**Scarta tutto < 7.** Max 2 video per creator (diversità di angolo). Seleziona i 25 gold.

Mostra i 25 candidati all'utente (username, score rilevanza, views) e chiedi conferma prima di trascrivere.

---

## Step 6 — Trascrizione gold picks

```bash
curl -sS -X POST "https://api.apify.com/v2/acts/scrape-creators~best-tiktok-transcripts-scraper/run-sync-get-dataset-items?timeout=300" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  -d @/tmp/ugc-scrape/transcripts-input.json
```

**Costo:** 25 × $0.002 = $0.050. **Mai trascrivere prima del vetting** — spreco di budget.

---

## Step 7 — Costruzione swipe file

Salva in: `$WORKDIR/05_UGC/scraper/<niche-slug>/ugc-winners-v2-<YYYY-MM-DD>.txt`

Ogni blocco winner include:
- Rank, score REL, score finale, flag breakout
- Metadata creator (username, followers, views/follower ratio, verificato)
- Metriche engagement (views, likes, shares, commenti, save, durata)
- Contesto (età, hashtag, query di ricerca)
- Caption TikTok
- Fonte trascrizione + trascrizione completa
- Hook line (prime ~12 parole del parlato)

Ordinamento: rilevanza DESC, poi score finale DESC.

---

## Costi riepilogo

| Step | Calls | Unit | Subtotale |
|---|---|---|---|
| Scraping scraptik | 6 richieste | $0.001 | $0.006 |
| Trascrizioni | 25 URL | $0.002 | $0.050 |
| Vetting LLM | 0 API | $0 | $0 |
| **TOTALE per run** | | | **~$0.056** |

Piano free Apify ($5/mese) = **~90 run/mese**.

---

## Regole critiche

- **Non saltare il vetting rilevanza** — TikTok porta rumore
- **Mai usare MCP tools per gli actor** — usa REST API direttamente
- **`searchPosts_sortType: 1` sempre** — mai 0 (Rilevanza)
- **`searchPosts_publishTime: 90` sempre** — mai 0 (All Time)
- **Mai inventare trascrizioni** — se null, scrivi "trascrizione non disponibile"
- **Mai trascrivere prima del vetting** — spreco di budget
