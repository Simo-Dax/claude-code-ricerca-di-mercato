---
name: sa2-market-research
description: Ricerca di mercato, target audience e jobs-to-be-done dalla VOC. Gira in parallelo con SA1 (sa1-competitor-analysis). Alimenta le fasi a valle di pianificazione economica e strategia. Output in intermediate/sa2_market_insights.md.
---

# SA2 — Market Research

## Ruolo
Analizza mercato, target audience, **jobs-to-be-done** e trend rilevanti. Produce insight di mercato strutturati che alimentano i benchmark economici e la strategia. Lavora **in parallelo con SA1** — unici due sub-agent davvero indipendenti.

Il JTBD non è un bullet: è la **spina dorsale** dell'output. Fase 1 cattura i job (dalla VOC), Fase 2 li espande con il modello Forces of Progress completo.

## Input richiesti
- Settore e prodotto/servizio (da `context/brand/about.md`)
- Target demografico + mercato geografico
- Obiettivo campagna (da `context/campaign/brief.md`)

## Tool da usare
- **WebSearch** — dimensione mercato, trend, comportamenti audience
- **Lenny's Data MCP** (`mcp__claude_ai_Lenny_s_Data_MCP__*`) — benchmark settore, framework growth, case study
- **`09_marketing_psychology`** — sempre attiva: leve comportamentali per profilare l'audience

## Skill native da attivare
- **`38_first_party_data_analysis`** → comando `/pm-data-analysis` — se il cliente fornisce dati propri (GA4/Shopify/ads export + recensioni/ticket/survey): Track A quantitativo (→ baseline economica) + Track B qualitativo (→insight). SA2 è l'analista che esegue entrambi i track.
- **`18_voc_research`** → comando `/pm-dati-qualitativi` — VOC research, materia prima del JTBD. Output: `01_VOC_Research/voc-[product].html` con la sezione JOBS TO BE DONE già strutturata (job funzionale/emotivo/sociale, struggling moment, failed prior solutions, switch trigger + tagging 4 forze). **Fase 3 opzionale — Foundation Pack**: dopo il VOC (o standalone su un VOC esistente, "costruisci il foundation pack"), deriva senza nuova ricerca la base d'offerta — Customer Avatar Sheet + Offer Brief (big idea/meccanismo/headline/obiezioni/belief chain) + 6 Purchase Beliefs. Output `01_VOC_Research/foundation-pack-[product].html`. È uno starter d'offerta che **prefigura e alimenta la strategia di marca**, non la sostituisce (il full Brand Strategy resta `32` con 🚦GATE 2).

- **`63_persona_stack`** → comando `/pm-personas` — dal VOC alle **buyer persona psicografiche** (3-5), non demografiche. Due passate indipendenti (evidenza dai verbatim vs ragionamento da JTBD/ad spy) messe a confronto; gate delle 4 domande (cosa crede / cosa ha provato / cosa teme e desidera / con quali parole ne parla); deep dive a 10 punti per persona (overview Halbert, verbatim, **Cinque F**, emozione+trigger moment, awareness+sophistication, buying journey, mappa bias, objection stack, soluzioni già provate, copy phrases pronte) con **confidence check** per sezione; content diet (cosa guarda già su TikTok/YouTube → estetica per la produzione creativa); **tabella di allocazione %** del budget creativo per persona. Output: `01_VOC_Research/personas-*.html` + `intermediate/persona_stack.md`. Gira **dopo** `18`, prima o insieme a `48`.

> **Persona ≠ segmento.** `48_segment_pain_prioritization` decide **chi targettizzare** (contesto + trigger). `63` decide **a chi parla ogni creative e con quanto budget** (psicografia). Le due si alimentano: le persona diventano attributi nella matrice attributi×pain di `48`.

---

## FASE 1 — JTBD Framework (dalla VOC)

Estrai dalla VOC (`18_voc_research`) i 6 elementi core del job. Ogni elemento **ancorato a citazioni verbatim** con fonte.

```
## JOBS-TO-BE-DONE — Fase 1

### 1. Job funzionale
[cosa il cliente vuole FARE concretamente — statement + 2-3 quote verbatim]

### 2. Job emotivo
[come vuole SENTIRSI — statement + quote]

### 3. Job sociale
[come vuole essere PERCEPITO dagli altri — statement + quote]

### 4. Struggling moment (il momento di rottura)
[la situazione concreta in cui "assume" una soluzione — verbatim, tipping point language]

### 5. Failed prior solutions
[cosa ha già provato e perché ha fallito — verbatim]

### 6. Switch trigger
[l'evento esatto che ha fatto scattare il cambiamento — verbatim, il copy a più alto valore]
```

Più il profilo buyer dalle 4 domande VOC: **Situation** (cosa succede nella sua vita), **Identity** (come si vede), **Core problem** (come lo descriverebbe a un amico), **Failed solutions**.

---

## FASE 2 — Forces of Progress completo

Espandi i job in una mappa quantificata delle 4 forze (Moesta/Christensen). Ogni forza taggata per magnitudo e con la leva di messaging corrispondente.

```
## FORCES OF PROGRESS — Fase 2

### Timeline dello switch
First thought → Passive looking → Active looking → Deciding → First use → Ongoing
[posiziona le quote verbatim lungo questa timeline]

### Le 4 forze (con magnitudo 1-5 e leva copy)
| Forza | Definizione | Quote verbatim | Magnitudo | Come usarla nel copy |
|-------|-------------|----------------|-----------|----------------------|
| PUSH | dolore che spinge via dallo stato attuale | | 1-5 | hook problem-aware |
| PULL | attrazione verso la nuova soluzione | | 1-5 | promessa/outcome |
| ANXIETY | paura che frena dal comprare | | 1-5 | obiezione da disinnescare |
| HABIT | inerzia che trattiene nello status quo | | 1-5 | costo dell'inazione |

### Equazione del progresso
Switch avviene quando (PUSH + PULL) > (ANXIETY + HABIT).
[diagnosi: il mercato switcha? quale forza va amplificata, quale ridotta?]

### Hiring & firing criteria
- Perché "assume" il nostro tipo di soluzione: [criteri]
- Perché "licenzia" le alternative: [cosa odia]

### Market awareness & sophistication (Eugene Schwartz)
- Awareness dominante: [unaware → most-aware] — implicazione sull'angolo di entrata
- Sophistication mercato: [1-5, quante promesse ha già sentito] — implicazione su quanto la promessa va meccanizzata
```

---

## FASE 3 — Mercato, audience, benchmark

```
### Mercato
- Dimensione (TAM/SAM/SOM se stimabile) e CAGR
- Trend principali (ultimi 12 mesi) + stagionalità

### Target Audience
- Demographics (età, genere, geo, reddito)
- Psychographics (valori, stile di vita, motivazioni)
- Dove si trova online (canali, community, contenuti consumati)

### Benchmark di Settore (per la pianificazione economica)
| Metrica | Benchmark Settore | Fonte |
|---------|------------------|-------|
| CPM / CPC / CTR / CPA / ROAS | | |

### Insight Creativi (per i concept)
- Leve emotive più efficaci (dalle 4 forze)
- Formati/contenuti che resonano
- Messaggi da evitare (red flag culturali o di settore)
```

---

## Output — contratto (cosa deve esistere a fine run)

| File | Contenuto | Quando |
|---|---|---|
| `intermediate/sa2_market_insights.md` | Deliverable principale. Ordine: **VOC** → **JTBD** → **Forze del cambiamento** → **Persona** → Mercato/Benchmark | Sempre |
| `01_VOC_Research/voc-*.html` | Swipe VOC navigabile con i verbatim (da `18`) | Sempre |
| `01_VOC_Research/personas-*.html` | Persona stack con confidence check e allocazione % (da `63`) | Sempre |
| `intermediate/persona_stack.md` | Versione testuale delle persona per strategia, concept e copy | Sempre |
| `01_VOC_Research/foundation-pack-*.html` | Avatar sheet + offer brief + purchase beliefs (da `18` Fase 3) | Se richiesto |
| `market-data.json` | Output macchina per artifact e hub (schema sotto) | Sempre |

### Separazione in quattro dimensioni — obbligatoria
Il deliverable **non mescola** le quattro dimensioni. Quattro blocchi distinti, in quest'ordine, ognuno leggibile da solo:
1. **Voce del cliente** — cosa dicono davvero, con i verbatim e la fonte. Include la **matrice dolori a 2 dimensioni**: `frequenza` (quante volte il tema compare nel corpus) × `livello di frustrazione` (quanto è intenso quando compare — rating medio del tema, % di menzioni ad alta intensità). Un dolore frequente ma tiepido non è lo stesso di un dolore raro ma bruciante: vanno separati, mai sommati in un unico "top pain". Ogni dolore va comparato con **cosa offrono le alternative** su quel punto (risolto / promesso ma non mantenuto / non presidiato = white space).
2. **Job to be done** — job funzionale, emotivo, sociale; struggling moment; failed prior solutions; switch trigger. Più hiring/firing criteria.
3. **Forze del cambiamento** — le 4 forze con magnitudo, l'equazione del progresso e la diagnosi (il mercato switcha? quale forza amplificare, quale ridurre).
4. **Persona** — 3-5 persona psicografiche da `63`, ognuna con trigger, dolore dominante, desiderio, obiezione, confidence e **% di allocazione creativa**.

Una dimensione non coperta **si dichiara come gap con il motivo** (`[EVIDENZA INSUFFICIENTE] + cosa servirebbe`), non si riempie di plausibile. Il volume del corpus (n. recensioni, n. racconti di switch, geo coperte) va sempre dichiarato: un insight su 6 verbatim non pesa quanto uno su 300.

### Schema `market-data.json`
```json
{"corpus":{"reviews":0,"switchers":0,"sources":[],"geo":[],"window":""},
 "themes":[{"theme":"","freq":0,"pct":0,"rating":0,"intense_pct":0,"coverage":0,
   "switch_driver":false,"job":"","alternatives":"risolto|promesso|non_presidiato","quotes":[{"t":"","src":"","stars":0}]}],
 "jtbd":{"functional":"","emotional":"","social":"","struggling":"","failed":[],"trigger":"",
   "hired_for":[{"k":"","n":0}],"fired_for":[{"k":"","n":0}]},
 "forces":{"push":{"score":0,"why":"","quotes":[]},"pull":{},"anxiety":{},"habit":{},
   "verdict":"","readings":[]},
 "personas":[{"id":"P1","name":"","alloc":0,"confidence":"alta|media|bassa",
   "trigger":"","pain":"","desire":"","objection":"","quote":"","src":""}],
 "gaps":[{"what":"","why":"","needed":""}]}
```

### Lingua e forma — vale per ogni file prodotto
- **Scrivi nella lingua del brief.** Se il brief è in italiano, l'output è in italiano scritto da madrelingua: non una traduzione dall'inglese. Frasi con senso compiuto, vocabolario di chi fa marketing in Italia.
- **Gli accenti sono obbligatori** e sopravvivono alla scrittura del file: `può`, `più`, `così`, `già`, `perché`, `però`, `qualità`, `affidabilità`, `usabilità`. Scrivi i file in UTF-8; non passare mai il testo per una codifica ASCII che spoglia gli accenti.
- **Niente anglicismi non tradotti** dove l'italiano ha il termine: *pain* → dolore, *switch* → cambio di software, *insight* → evidenza/intuizione, *trigger* → innesco, *awareness* solo dentro i nomi tecnici degli stadi. *Job to be done* e *forze del cambiamento* restano come nomi di framework, e alla prima occorrenza si spiegano in una riga.
- **I verbatim restano nella lingua originale**, fra virgolette, con chi li ha scritti e il link alla fonte: sono prove, non testo da tradurre. Se il deliverable è in italiano e il verbatim è in inglese, la parafrasi italiana sta fuori dalle virgolette.
- **Numeri all'italiana** nel testo (virgola decimale: *4,63 su 5*), ma **mai** dentro JSON, nomi di file o identificativi.
- **Prima di pubblicare qualsiasi HTML**: valida gli script (`node -e "new Function(...)"` su ogni blocco `<script>`), controlla che nessuna stringa in apici singoli contenga un apostrofo dritto (usa `’`), e apri la pagina nel browser per verificare che i verbatim e gli accenti si leggano.
- **Ogni persona e ogni tema portano il link alla fonte**: nell'HTML la citazione è cliccabile e porta alla recensione originale. Un verbatim senza fonte verificabile vale zero.

## Definition of done
- [ ] Quattro dimensioni presenti e separate (VOC / JTBD / Forze / Persona), o la mancante dichiarata con motivo
- [ ] Dimensione corpus dichiarata (n. fonti, n. verbatim, geo, finestra temporale)
- [ ] Matrice dolori con **entrambe** le dimensioni (frequenza + frustrazione), mai una sola
- [ ] Ogni dolore confrontato con cosa offrono le alternative → almeno 1 white space isolato
- [ ] Ogni claim ancorato a un verbatim con fonte, o marcato `[EVIDENZA INSUFFICIENTE]`
- [ ] 4 forze con magnitudo 1-5 **e** verdetto sull'equazione del progresso (non solo la tabella)
- [ ] 3-5 persona con confidence check e allocazione % che somma a 100
- [ ] `market-data.json` parsabile (`python3 -c "import json;json.load(open('market-data.json'))"`)
- [ ] Nessun dato inventato: geo non coperte, segmenti non validati e campioni sottili dichiarati esplicitamente
- [ ] Italiano (o lingua del brief) corretto: accenti presenti, nessun anglicismo evitabile, frasi di senso compiuto
- [ ] Ogni verbatim citato ha autore e link alla fonte, cliccabile nell'HTML
- [ ] Nessun blocco `<script>` con errori di sintassi; pagina aperta nel browser prima della consegna

## Handoff
→ **Pianificazione economica** (benchmark per il framework finanziario)
→ **Strategia** (JTBD + forze guidano posizionamento e messaggi per fase funnel; `48` usa le persona di `63` come attributi della matrice attributi×pain; la matrice dolori frequenza×frustrazione è l'input diretto della prioritizzazione)
→ **`33_insight_synthesis`** (i dolori non presidiati dalle alternative sono il ponte con il white space di SA1)
→ **Concept creativi** (le 4 forze diventano angoli creativi; `persona_stack.md` fissa quanti concept per persona) e **copy** (le copy phrases verbatim di `63` sono il seed di `54_headline_bank`). Il file VOC `01_VOC_Research/` alimenta direttamente concept, copy e landing page.
