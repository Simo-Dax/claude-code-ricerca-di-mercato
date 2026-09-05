# Tool usati dagli agenti

Non solo i comandi `/pm-*`: sotto ogni comando ci sono **tool reali**. Questa è la lista
completa di cosa tocca il ramo research (SA1 competitor + SA2 market), con costo e alternativa
quando il tool non c'è.

Legenda costo: **incluso** = arriva con Claude Code · **a consumo** = paghi per esecuzione ·
**gratis** = account gratuito sufficiente.

---

## 1. Tool nativi di Claude Code — incluso

| Tool | A cosa serve nel ramo research | Chi lo usa |
|---|---|---|
| `Read` / `Write` / `Edit` | Legge il contesto brand, scrive i deliverable in `intermediate/` e negli HTML | tutte le skill |
| `Bash` | Lancia gli script Python delle skill, aggrega i JSON, monta le dashboard | 19, 52, 62, 47, 38 |
| `Glob` / `Grep` | Trova i file di una raccolta precedente senza riscaricarla | tutte |
| `WebSearch` | Trova recensioni, thread, articoli di settore, pagine dei competitor | 18, 47, 63 |
| `WebFetch` | Apre una pagina specifica e ne estrae il testo (recensioni, pricing, changelog) | 18, 47 |
| `Task` | Fa girare SA1 e SA2 in parallelo, ciascuno in una finestra di contesto sua | orchestratore |
| `TodoWrite` | Tiene la lista dei passi di una raccolta lunga | SA1, SA2 |

> **Il grosso della ricerca qualitativa gira su `WebSearch` + `WebFetch` + il ragionamento del
> modello.** Nessun tool di review-mining a pagamento è richiesto: le recensioni pubbliche
> (G2, Capterra, Trustpilot, app store, forum) si leggono e si codificano a mano dall'agente.

---

## 2. MCP server — configurati in `.mcp.json`

| MCP | Cosa dà | Costo | Usato da |
|---|---|---|---|
| **Apify** (`@apify/actors-mcp-server`) | Esegue gli actor di scraping: Meta Ad Library, pagine Facebook, Google Ads Transparency, TikTok | **a consumo** (piano gratuito ~5$/mese di credito) | 19, 20, 52, 62 |
| **Playwright** (`@playwright/mcp`) | Browser vero: apre le pagine che bloccano il fetch semplice, fa screenshot delle creative | gratis | 19, 47, 62 |
| **fal.ai** (`fal-ai-mcp-server`) | Whisper per trascrivere i video dei competitor parola per parola | **a consumo** (centesimi per video) | 52, 20 |
| **Google Ads** (server ufficiale via `uv`) | Query GAQL sull'account: volumi, termini di ricerca, costi reali | gratis con account Ads | opzionale, valida i volumi degli spazi liberi |
| **SimilarWeb** | Traffico e canali dei competitor: quanto pesa davvero il paid | freemium | opzionale, SA1 |

Le chiavi non stanno mai nel repo: si mettono in `.mcp.json` locale (che è in `.gitignore`) o
nelle variabili d'ambiente. Parti da `.mcp.json.example`.

---

## 3. Actor Apify usati

| Actor | Cosa raccoglie | Skill |
|---|---|---|
| `curious_coder~facebook-ads-library-scraper` | Annunci Meta di un inserzionista: creative, testi, date di attivazione, formato | 19, 52 |
| `apify~facebook-pages-scraper` | Risolve il nome del brand nell'ID pagina, per bloccare la raccolta sull'inserzionista giusto | 19 |
| `scrapesage~google-ads-transparency-scraper` | Annunci Google per dominio: ricerca, YouTube, shopping, con giorni di attività | 62 |
| `scraptik~tiktok-api` / `creators~best-tiktok-transcripts-scraper` | Contenuti organici virali della nicchia e loro trascrizione | 20 |

> **Attenzione al limite mensile.** Quando il credito Apify finisce, i conteggi diventano
> soglie minime, non totali: va scritto nel deliverable, non nascosto.

---

## 4. Fonti pubbliche interrogate senza tool a pagamento

| Fonte | Cosa ne esce | Come |
|---|---|---|
| **Meta Ad Library** (`facebook.com/ads/library`) | Cosa spingono i competitor in interruzione | Apify, o a mano dal browser |
| **Google Ads Transparency Center** (`adstransparency.google.com`) | Cosa presidiano in intento, e da quanti giorni | Apify, o a mano |
| **G2 / Capterra / Trustpilot** | Recensioni verificate: lodi, difetti, motivi di abbandono | `WebSearch` + `WebFetch` |
| **Reddit, forum di settore, gruppi** | Linguaggio informale, obiezioni non filtrate dal marketing | `WebSearch` (spesso bloccano lo scraping: dichiaralo come lacuna) |
| **TikTok / YouTube** | Ganci e formati che la nicchia guarda davvero | Apify |
| **Sito e pricing page dei competitor** | Promessa dichiarata, da confrontare con quella pubblicitaria | `WebFetch` |

---

## 5. Cosa NON serve

- **Nessun tool di ad-spy a pagamento.** L'evidence layer sta tutto su librerie pubbliche.
- **Nessun tool di review-mining a pagamento.** Le recensioni si leggono e si codificano
  con il modello.
- **Nessun plugin di terzi.** Gli MCP sono i server ufficiali, configurati nel progetto.

Il costo variabile reale di una ricerca completa è quello di Apify e di fal.ai: pochi euro
per brand, quando servono video e librerie grandi. Il resto è incluso in Claude Code.
