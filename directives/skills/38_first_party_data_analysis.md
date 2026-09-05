# SA2/SA3 — First-Party Data Analysis (quantitativa + qualitativa)

**Agente:** SA2 (Research) — alimenta SA3 (Financial) e `33_insight_synthesis`
**Input:** export dati first-party del cliente (in `context/campaign/data/` o cartella fornita)
**Output:** `output/{brand}_{campaign}_{date}/intermediate/first_party_quant.md` + `.../first_party_qual.md`
**Origine:** colma il gap "quant/qual analyst" del metodo Learnn (fase 1) sui dati PROPRI del cliente.

> Differenza vs `18_voc_research`: la VOC analizza il linguaggio cliente **pubblico** (review, forum, community). Questa skill analizza i **dati first-party del cliente** (i suoi numeri e le sue conversazioni interne). Insieme danno il quadro completo.

---

## Quando si usa
All'inizio della fase Research, **se il cliente fornisce dati first-party**. Se non li fornisce → salta con nota, non inventare. Più dati reali = insight e financial più veri (meno stime dal brief).

## Input tipici (qualsiasi sottoinsieme)
- **Quantitativi**: export GA4 (traffico, funnel, coorti), Shopify (Sales over time, Customers over time, AOV, ordini, returning), export Google/Meta Ads (spend, ROAS, keyword), gestionale.
- **Qualitativi**: recensioni del brand, ticket customer care, chat log, survey/NPS, interviste, email clienti.

Chiedi all'utente dove sono i file (default `context/campaign/data/`). Inventaria prima (tipo, fonte, periodo, colonne) come fa un data-collector.

---

## TRACK A — Analisi QUANTITATIVA (owner: SA2 → alimenta SA3)

Estrai SOLO da numeri presenti nei file. Cita sempre il file fonte. Per ogni dato mancante scrivi "non disponibile" + quale export servirebbe.

1. **Trend vendite nel tempo** — identifica stagionalità. **ESCLUDI novembre/dicembre** (Black Friday/Cyber Monday sporcano il dato): trattali come picco a parte.
2. **Top 10 prodotti** per fatturato + trend.
3. **Funnel** — drop-off rate tra step (se GA4 disponibile).
4. **Coorti** — new vs returning, retention rate (CRR), purchase frequency (PF).
5. **AOV** reale (su net sales), per new e returning.
6. **Canali/keyword** — performance se export disponibili.
7. **Lettura strategica** — cosa significano i pattern per il marketing (non solo numeri).

Output → `intermediate/first_party_quant.md` con tabelle recap + "Implicazioni strategiche".
**→ SA3** usa questi numeri reali (AOV, margin, CRR, PF, cohorts) come **baseline del financial framework** al posto delle stime del brief. **→ 33** li usa come fonte "quantitativa" degli 8 building block.

---

## TRACK B — Analisi QUALITATIVA (owner: SA2 → alimenta 33 + SA5/SA7)

Da recensioni/ticket/chat/survey PROPRI del cliente (distinti dalla VOC pubblica):
1. **Sentiment** — % positivo/neutro/negativo, tono prevalente, lessico dominante (parole reali ricorrenti).
2. **Analisi tematica** — temi principali per frequenza, con citazioni testuali brevi e reali.
3. **Aspetti positivi** (potenziali USP) e **negativi/critiche** (potenziali pain/obiezioni).
4. **Tabella angoli di comunicazione**: beneficio, prodotto, angolo, emotional appeal, hook, pain, desiderio, citazione reale, idea ad statica, idea ad video.

Regole: usa le **parole reali** dei clienti, non parafrasi. Non gonfiare il sentiment se i dati sono pochi (dillo).

Output → `intermediate/first_party_qual.md`. **→ 33** come fonte "qualitativa". **→ 18_voc_research** si integra (VOC pubblica + qual interna = mappa emotiva completa). **→ SA5/SA7** angoli + citazioni reali.

---

## Chi fa cosa (mapping)
- **SA2** esegue entrambi i track (è l'agente di ricerca/analista). Research SA2 = mercato pubblico + `18_voc` (qual pubblica) + `38` (quant + qual first-party).
- **SA3** consuma `first_party_quant.md` come baseline finanziaria reale.
- **`33_insight_synthesis`** unisce quant (38A + benchmark) + qual (38B + 18 VOC) + macro (SA1+SA2 mercato) → 8 building block con fonte citata.

## Regole critiche
- **Solo dati reali forniti.** Niente invenzioni. Se mancano → nota + export consigliato.
- **Cita il file fonte** per ogni cifra/citazione.
- **Escludi BF/CM** dai trend normali.
- **Distingui** dato del brand da dato dei competitor.
- Se nessun dato first-party fornito → salta, la pipeline procede con brief + ricerca pubblica.
