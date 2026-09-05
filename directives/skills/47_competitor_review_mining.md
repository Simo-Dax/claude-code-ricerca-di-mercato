# Competitor Review Mining — Gap Positivo/Negativo (market gap finder)

**Fase:** research strategica. Gira con SA1∥SA2, alimenta `33_insight_synthesis`.
**Agente:** SA1 (competitor) / SA2 (market) — input alla strategia SA4.
**Input:** lista competitor (da SA1 `03_Ad_Spy/competitors.json` o brief) + categoria prodotto
**Output:** `intermediate/competitor_review_gap.md` (gap map) → feed a `33` (dim 2 Alternative, dim 5 Pain, white space) e a `48_segment_pain_prioritization`
**Tool dati:** Apify REST diretto (review scraper: Amazon, Trustpilot, G2, App Store/Play, Google reviews) — stesso pattern di `19_ad_spy` (token header `Authorization: Bearer`, mai in URL, no MCP)

---

## Perché esiste

Le recensioni dei competitor sono la **mappa più onesta del mercato**: dove i clienti sono già contenti (table stakes da pareggiare) e dove sono delusi (gap = la nostra opportunità). Il valore non è la singola recensione ma il **delta tra ciò che le recensioni positive lodano e ciò che le negative attaccano**. Quel delta è il white space difendibile.

> Differenza da `18_voc_research`: il VOC estrae il linguaggio dei clienti **del nostro brand/categoria** per scrivere copy. Qui scaviamo le recensioni **dei competitor** per trovare i **gap di mercato** che orientano posizionamento e offerta.

---

## Step 0 — Token Apify (REST diretto, no MCP)

```bash
[ -f ~/.config/pm-agent/apify.env ] && . ~/.config/pm-agent/apify.env
TOKEN="${APIFY_TOKEN:-}"
```
Se `TOKEN` vuoto → `/pm-setup-apify` e fermati. Token sempre come header `Authorization: Bearer`. Se non c'è accesso scraper (categoria senza recensioni pubbliche, B2B di nicchia) → fallback ricerca web manuale (G2/Capterra/Reddit/forum) dichiarando la fonte.

---

## Step 1 — Sorgenti recensioni per business model

Scegli la fonte giusta per il modello del brand:
- **eCommerce/DTC fisico** → Amazon, Trustpilot, recensioni sito, Reddit
- **SaaS/software** → G2, Capterra, TrustRadius, Reddit, ProductHunt
- **App** → App Store + Google Play
- **Servizi locali** → Google reviews, Trustpilot, Yelp
- **B2B** → G2, Capterra, LinkedIn, case study competitor (lettura critica)

Per ogni competitor raccogli un campione bilanciato: punta a **≥30 recensioni**, con copertura di **tutte le stelle** (non solo le 5★ o le 1★). Servono sia le positive sia le negative per il delta.

---

## Step 2 — Estrazione per polarità (per competitor)

Per ogni competitor classifica i temi ricorrenti in due colonne:

| Polarità | Cosa estrai |
|---|---|
| **POSITIVO** (4-5★) | Cosa lodano ripetutamente → table stakes (da pareggiare) + punti di forza reali (da non attaccare frontalmente) |
| **NEGATIVO** (1-2★) | Cosa attaccano ripetutamente → unmet needs, frizioni, promesse non mantenute → **i gap** |

Per ogni tema registra: **frase verbatim** (citazione esatta), **frequenza** (quante recensioni lo toccano), **intensità** (quanto è forte la frustrazione/entusiasmo). Niente parafrasi sui verbatim.

---

## Step 3 — La GAP MAP (il deliverable centrale)

Il gap di mercato vive in 3 zone. Costruisci la tabella:

| Tema | Competitor lodato (POS) | Competitor attaccato (NEG) | Tipo di gap | Opportunità per noi |
|---|---|---|---|---|
| [es. velocità onboarding] | "[verbatim 5★]" | "[verbatim 1★]" | **Gap di esecuzione** | il competitor promette ma fallisce → noi manteniamo |
| [es. supporto clienti] | — | "[verbatim 1★ ricorrente]" | **Gap scoperto** | nessuno lo presidia bene → white space |
| [es. prezzo] | "[verbatim]" | "[verbatim]" | **Trade-off polarizzante** | segmento diverso lo valuta opposto → leva di segmentazione |

3 tipi di gap:
1. **Gap di esecuzione** — il competitor lo promette ma le negative dicono che fallisce → noi possiamo mantenerlo davvero.
2. **Gap scoperto** — un dolore ricorrente nelle negative che nessun competitor presidia → white space puro (i più forti).
3. **Trade-off polarizzante** — stesso attributo lodato da alcuni e odiato da altri → segna un confine di segmento (alimenta `48`).

---

## Step 4 — Sintesi strategica

Chiudi con:
- **Top 3-5 gap** ordinati per opportunità (frequenza nelle negative × assenza di presidio competitor).
- **Table stakes** da pareggiare obbligatoriamente (ciò che TUTTE le positive danno per scontato — se manca, parti svantaggiato).
- **Da NON attaccare** — i punti di forza reali dei competitor (lodati e mantenuti): attaccarli frontalmente è perdente, aggira.
- **Ponte agli insight:** ogni gap mappa su una dimensione di `33` (Alternative/Pain/Categoria) e segnala dove spostare il posizionamento.

---

## Output → `intermediate/competitor_review_gap.md`

```markdown
# Competitor Review Gap — {Categoria} — {Data}

## Competitor analizzati + sorgenti + n. recensioni
## Estrazione per polarità (per competitor)
## GAP MAP (tabella Step 3)
## Top 3-5 gap (ordinati per opportunità)
## Table stakes obbligatori
## Punti di forza competitor da NON attaccare
## Ponte a 33_insight_synthesis (gap → dimensione)
```

---

## Regole ferree
- **Verbatim, mai parafrasi** sui temi (citazione esatta + fonte).
- **Campione bilanciato** — sia positive sia negative, tutte le stelle. Solo 1★ = lamentele estreme non rappresentative; solo 5★ = fan, niente gap.
- **Il gap è il delta**, non la singola recensione. L'opportunità sta tra lode e attacco.
- **Distingui** gap di esecuzione (promesso-non-mantenuto) da gap scoperto (mai presidiato).
- **Niente invenzioni** — se una categoria non ha recensioni sufficienti, dillo e passa a ricerca web qualitativa dichiarata.

---

## Handoff
`competitor_review_gap.md` →
- **`33_insight_synthesis`**: i gap alimentano dim 2 (Alternative), dim 5 (Pain), Categoria, white space.
- **`48_segment_pain_prioritization`**: i trade-off polarizzanti segnano i confini di segmento; i gap diventano pain da scorare.
- **`32_brand_strategy`**: i gap scoperti orientano USP e offer design; i table stakes definiscono il minimo competitivo.
