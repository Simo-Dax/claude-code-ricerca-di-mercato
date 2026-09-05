# Insight Synthesis — 8 Building Block + Proposta (+ Gate Umano)

**Fase:** ponte tra la ricerca (SA1+SA2) e la strategia. Gira dopo SA1∥SA2 ed è l'ultimo passo del kit.
**Input:** `intermediate/sa1_competitor_landscape.md` + `intermediate/sa2_market_insights.md` + `01_VOC_Research/` + `context/brand/`
**Output:** `output/{brand}_{campaign}_{date}/intermediate/insight.md` (con sezione finale di validazione umana obbligatoria). Vedi Convenzione Output in `claude.md`.
**Origine:** internalizzato da `insight-synthesizer` del Marketing Strategist (metodo Learnn fase 2). Reference: `execution/strategy-method/`.

---

## Filosofia (non negoziabile)

Questo è il punto in cui il sistema rischia di produrre **plausibilità invece di verità**. L'AI accelera dato (fase 1) ed esecuzione (fase 4); il **giudizio su insight (fase 2) e strategia (fase 3) è umano**. Questa skill produce una **BOZZA** che l'umano valida prima che la pipeline prosegua verso strategia e budget.

→ Dopo l'output, **GATE 1**: l'orchestrator si ferma, mostra il riepilogo, chiede conferma. Non si procede senza OK esplicito.

---

## Cosa produce

Due cose, in quest'ordine:
1. Sintesi dei tre livelli di analisi (quantitativa, qualitativa, ricerca macro) negli **8 building block strategici**, ognuno con la **fonte citata**.
2. La **proposta** che ne discende — Value Proposition, USP, reason why, trigger event, offerta, tone of voice. I building block sono la diagnosi; la proposta è ciò che l'umano valida al GATE 1.

I tre livelli nel nostro sistema:
- **Quantitativo** → `first_party_quant.md` (da `38_first_party_data_analysis`, dati reali cliente) + benchmark SA2 + storico dell'account + dati economici del brand
- **Qualitativo** → VOC (`18_voc_research`) + `first_party_qual.md` (da `38`, recensioni/ticket/survey propri) + ad spy (`19_ad_spy`) + UGC (`20_ugc_scraper`) + **gap recensioni competitor (`47_competitor_review_mining`)**
- **Macro** → ricerca di mercato SA2 + competitor landscape SA1
- **Pain & segmento** → **`48_segment_pain_prioritization`** (pain matrix frequency×frustration, matrice attributi×pain, segmenti per contesto+trigger, prioritizzazione) — alimenta dim 4 e 5

---

## Gli 8 building block (UNO ALLA VOLTA, in quest'ordine)

> **L'ordine è il metodo, non una formalità.** Si parte SEMPRE dal Job (dim 1): è il job che definisce chi sono le alternative, quali pain contano, quali desideri muovono l'acquisto. Saltare il job = costruire strategia su sabbia. Catena: **Job → Alternative → Pain non risolto dalle alternative → come lo soddisfiamo parlando ai desideri**.

```
## INSIGHT — 8 Building Block

### 1. Job to be done (SEMPRE PER PRIMO)
Funzionale E emotivo: non solo "cosa fa il prodotto" ma "che lavoro emotivo gli affida il cliente".
Il job viene prima di tutto: definisce il perimetro delle alternative e quali pain sono rilevanti.
[ancorato a JTBD + Forces of Progress di SA2]
Fonte: [quantitativa / qualitativa / macro]

### 2. Alternative
Soluzioni dirette e indirette, inclusi i fai-da-te. + overview competitor con gap sfruttabili (da SA1 white space + `47_competitor_review_mining`).
Per ogni alternativa: cosa risolve bene (table stakes) e cosa NON risolve (dove vive il pain non soddisfatto, dim 5).
Fonte: [...]

### 3. Categoria
Come è strutturata la categoria, sotto-segmenti.
Fonte: [...]

### 4. Key Segment (decisione critica)
Il segmento NON è una demografica: è **contesto + trigger** in cui un pain diventa azione (da `48_segment_pain_prioritization`).
Valuta early adopter (nicchia, alta propensione, alta profittabilità) vs target scalabile (volume), prioritizzando sui 3 fattori macro (fatturato/profittabilità, accesso, crescita/TAM).
Pesa profittabilità contro scalabilità. **Raccomanda un segmento prioritario motivando con contesto, trigger e i 3 fattori.**
Fonte: [`48` + ...]

### 5. Pain point del segmento prioritario (NON risolto dalle alternative)
Dai dolori VOC verbatim, scorati su frequency × frustration (da `48`) e **incrociati con dim 2**: il pain che conta è quello ad alta frequenza+frustrazione che le alternative NON risolvono (white space) o risolvono male (sweet spot). Un pain già ben coperto dai competitor è table stakes, non leva.
Fonte: [`48` + `47` + VOC ...]

### 6. Desideri (come soddisfiamo il pain)
Dai desideri VOC + Pull forces. Qui si costruisce il ponte: **per ogni pain prioritario (dim 5), come la nostra soluzione lo soddisfa parlando al desiderio sottostante** (non alla feature). È il seme della Value Proposition (`32`).
Fonte: [...]

### 7. Obiezioni
Dalle anxiety forces + recensioni negative (proprie e competitor da `47`).
Per ognuna: come si disinnesca (prova, garanzia, riformulazione), non solo l'elenco.
Fonte: [...]

### 8. Trigger point
**L'evento concreto** che trasforma un pain latente in ricerca attiva. Non uno stato ("è frustrato"), un fatto datato ("ha aperto la terza sede", "il gestionale è andato giù nel weekend di punta", "il socio se n'è andato").
Dallo switch trigger di SA2 (Fase 1 JTBD) + dai racconti di switch nel corpus VOC. Per ogni trigger: quanto è frequente nel corpus, quanto anticipo dà (settimane/mesi prima dell'acquisto), e **su quale canale è intercettabile** (Meta = interruzione su trigger latente, Google = intento già formato).
È il building block che dice alla strategia *quando* parlare e al copy *con quale apertura*.
Fonte: [...]
```

Ogni building block chiude con una **"logica strategica"** (non descrittiva): dove ci possiamo spostare a livello di posizionamento.

---

## Regole ferree

- Per OGNI insight indica la **fonte** (quantitativa / qualitativa / macro). Se non supportato dai dati → marcalo **"ipotesi da validare"**.
- **Vietato essere generico**: niente insight che varrebbero per qualsiasi brand. Se vale per chiunque, non è un insight.
- **Niente invenzioni.** Se i dati non bastano per un building block, dillo esplicitamente con `[EVIDENZA INSUFFICIENTE] + cosa servirebbe per colmarla`.
- Distingui sempre dato del brand da dato dei competitor.

---

## La proposta (obbligatoria — è il deliverable, non un extra)

Gli 8 building block sono diagnosi. Senza la proposta, il GATE 1 non ha niente da validare. Sei sezioni, ognuna ancorata a un building block:

```
## PROPOSTA

### Value Proposition — Bain Pyramid of elements of value
Mappa quali elementi di valore Bain il prodotto tocca davvero (funzionali / emotivi / life-changing / social impact), distinguendo **presidiati dai competitor** (table stakes) da **liberi** (leva). Poi la formulazione in una frase, che deve nascere da un elemento libero, non da uno saturo.

### USP — elementi differenzianti
3-5 righe. Ognuna: cosa dice · perché è vera (prova su disco) · perché il competitor non può dirla. Se un concorrente può copiarla domani, non è una USP.

### Emotional Reason Why
La **condizione** che vuole ottenere, non la feature. Dal blocco Desideri. Come deve sentirsi il lunedì mattina.

### Rational Reason Why
Ciò che deve sapere per **giustificare** la decisione (a sé, al socio, al CFO). Numeri, tempi, integrazioni, sicurezza. Dal blocco Obiezioni.

### Trigger Events → azione
Per ogni trigger del blocco 8: il messaggio che lo intercetta e il canale. È qui che la ricerca diventa media plan.

### Offerta core, bonus, garanzie
Alla luce dei dolori non presidiati: cosa possiamo offrire che disinnesca l'obiezione più costosa. Ogni voce dichiara quale obiezione uccide.

### Tone of voice — nemico e POV
- **Nemico rappresentativo**: ciò che il brand non è. Deve essere una *condizione* o un *sistema* (il foglio Excel di mezzanotte, il gestionale che va giù nel servizio), mai il cliente. Regola: il nemico **manleva** il cliente — chi legge deve pensare "non era colpa mia", mai sentirsi in colpa per come lavora oggi.
- **POV / sistema di credenze**: 4-6 affermazioni che il brand sostiene e che un concorrente medio non firmerebbe. Se le firmerebbero tutti, non sono POV.
```

Ogni voce della proposta cita il building block e la fonte da cui discende. Una proposta senza ancoraggio è un'opinione.

---

## Output + GATE 1

Scrivi `intermediate/insight.md`. Termina SEMPRE con:

```
## ⚠️ DA VALIDARE DALL'UMANO

Le 3-5 decisioni più importanti e incerte che richiedono il tuo giudizio di mercato:
1. [es. Key Segment: early adopter X vs target scalabile Y — quale prioritizziamo?]
2. ...
```

Poi l'orchestrator mostra il riepilogo degli 8 building block + la proposta e chiede:
> "Confermi questi insight o vuoi correggere/scartare qualcosa prima di costruire la strategia?"

Applica le correzioni umane riscrivendo il file. **Passa alla fase strategica solo dopo OK esplicito.**

---

## Definition of done
- [ ] 8 building block presenti, nell'ordine (Job per primo — non è negoziabile)
- [ ] Ogni building block con fonte (quantitativa / qualitativa / macro) o marcato `ipotesi da validare`
- [ ] Pain scorati su **entrambe** le dimensioni (frequenza × frustrazione) e incrociati con le alternative
- [ ] Key Segment definito per **contesto + trigger**, prioritizzato sui 3 fattori (fatturato/profittabilità, accesso, crescita/TAM)
- [ ] Trigger point con frequenza, anticipo e canale di intercettazione
- [ ] Proposta completa: VP (Bain) · USP · Emotional RW · Rational RW · Trigger events · Offerta · ToV (nemico + POV)
- [ ] Nemico che manleva il cliente, mai che lo colpevolizza
- [ ] Gap dichiarati esplicitamente con cosa servirebbe per colmarli
- [ ] Sezione `⚠️ DA VALIDARE DALL'UMANO` con 3-5 decisioni reali (non retoriche)

## Handoff
`intermediate/insight.md` (validato) → **pianificazione economica** (i pain/segment informano i target finanziari) e **strategia di marca** (`32_brand_strategy`: la VP e l'offerta discendono da questi insight). Niente strategia senza insight validati.
