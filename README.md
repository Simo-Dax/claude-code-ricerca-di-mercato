# Market Research Kit per Claude Code

Ricerca di mercato e competitor analysis **agentiche**: dalla voce dei clienti e dalle ads che i competitor stanno realmente pagando, ai building block della comunicazione — mappati, con le fonti, pronti a diventare strategia e creative.

Due sub-agent, undici comandi, zero dipendenze da plugin di terzi.

---

## Cosa produce

| Output | Da dove | Dove finisce |
|---|---|---|
| **Documento VOC** — 30+ citazioni verbatim con fonte, Jobs-to-be-Done, Forces of Progress | `/pm-dati-qualitativi` | `01_VOC_Research/voc-*.html` |
| **Persona stack** — 3-5 persona psicografiche, Cinque F, objection stack, % del budget creativo | `/pm-personas` | `01_VOC_Research/personas-*.html` |
| **Swipe file competitor** — ads Meta e Google ranked per longevità, con awareness e funnel classificati | `/pm-competitor-spy` + `/pm-google-spy` | `03_Ad_Spy/` + dashboard |
| **Gap map** — cosa i clienti dei competitor lodano e cosa attaccano | `/pm-review-gap` | `intermediate/competitor_review_gap.md` |
| **Pain matrix e segmenti** — frequency × frustration, segmenti per contesto e trigger | `/pm-segments` | `intermediate/segment_pain_matrix.md` |
| **Insight** — 7 dimensioni con fonte, più le decisioni che richiedono un umano | `/pm-insight` | `intermediate/insight.md` |

---

## Quickstart

1. **Installa Claude Code** e apri un terminale.
2. **Scarica il kit** e crea la cartella di lavoro del brand:
   ```bash
   git clone <url-repo> market-research-kit
   mkdir -p ~/lavoro/nomebrand && cp -R market-research-kit/. ~/lavoro/nomebrand/
   cd ~/lavoro/nomebrand
   ```
   > Le skill scrivono **nella cartella in cui apri Claude Code**. Una cartella per brand.
3. **Compila il contesto**: `context/brand/business_profile.md` e `context/campaign/brief.md`. Cinque minuti qui valgono un'ora dopo.
4. **Configura le chiavi** (opzionale ma consigliato): `cp .mcp.json.example .mcp.json`, metti le tue chiavi, poi lancia `claude` e `/pm-setup-apify`. Il dettaglio di ogni tool sta in [TOOLS.md](TOOLS.md).
5. **Parti**:
   ```
   /pm-dati-qualitativi
   /pm-competitor-spy
   /pm-google-spy
   /pm-review-gap
   /pm-personas
   /pm-segments
   /pm-insight
   ```
6. **Apri la dashboard**: `dashboard/competitor-ads/index.html` nel browser, dopo aver copiato lì il `data.json` generato.

---

## Cosa c'è nel repo

```
.claude/agents/          SA1 competitor + SA2 market research — i due system prompt
.claude/agents/_template/ lo scheletro per scrivere un tuo agente
.claude/commands/        i comandi /pm-* che lanciano le skill
directives/skills/       le procedure vere: una cartella o un file per skill
context/brand/           chi sei: business profile, tono di voce (da compilare)
context/campaign/        il brief della ricerca (da compilare)
context/references/      ads, copy e landing di riferimento (vuote, le riempi tu)
output/                  dove scrivono le skill — scaffold vuoto, una cartella per brand
dashboard/               dashboard competitor, si apre in locale sul file data.json
CLAUDE.md                l'orchestratore: le regole che valgono per ogni task
TOOLS.md                 tutti i tool usati dagli agenti, con costo e alternativa
.mcp.json.example        gli MCP da configurare — copialo in .mcp.json e metti le tue chiavi
```

Il kit è **solo il ramo ricerca**: due agenti, SA1 competitor e SA2 mercato. Finisce dove
finisce la ricerca — con gli insight validati da te. Se vuoi aggiungere un tuo agente per i
passi successivi, in `.claude/agents/_template/` c'è lo scheletro con frontmatter e SOP.

---

## Prerequisiti e costi reali

| Serve | Per cosa | Costo |
|---|---|---|
| Claude Code | tutto | abbonamento Claude |
| Apify API key | ad spy Meta e Google, review mining, UGC scraper | pochi centesimi per run (~$0.002 per ad) |
| fal.ai API key | trascrizione dei video competitor | a consumo, opzionale |
| ffmpeg | frame dei video competitor | gratis, opzionale |

Senza chiavi funzionano comunque VOC, persona stack, segment/pain e insight: usano solo la ricerca web.

---

## I due limiti che devi conoscere prima di iniziare

**1. Le librerie pubbliche non danno le performance.** Meta Ad Library e Google Ads Transparency Center, per le ads commerciali, non espongono impression, spend, CTR o ROAS. Il segnale forte è **la longevità**: nessuno paga per mesi un'ad che non converte. Il kit tiene la scala esplicita — PROVEN (≥60 giorni), HOT (≥21), ACTIVE, RETIRED — e non finge di sapere quello che non sa.

**2. L'awareness stage non è un dato, è una classificazione.** Non arriva dalle piattaforme: la assegna il modello leggendo creative e copy. È ciò che rende la matrice strategicamente utile, ed è anche la parte che vale la pena rivedere a mano.

---

## Il gate umano

`/pm-insight` non produce una verità: produce una **bozza** e si ferma, elencando le 3-5 decisioni più incerte. Nulla prosegue senza un OK esplicito.

Non è una cautela di facciata. La ricerca è il punto in cui un modello linguistico è più bravo a sembrare giusto che a esserlo. L'AI accelera la raccolta e la sintesi; su chi sia il segmento prioritario e quale pain valga la pena aggredire decide chi conosce il mercato.

---

## Licenza

MIT — vedi [LICENSE](LICENSE). Usalo, modificalo, distribuiscilo. Nessuna garanzia.
