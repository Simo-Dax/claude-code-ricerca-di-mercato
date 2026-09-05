# SA2 — Persona Stack (buyer persona psicografiche + allocazione creativa)

**Agente:** SA2 (Market Research)
**Input:** `01_VOC_Research/voc-*.html` (obbligatorio, da `18`) + `intermediate/competitor_review_gap.md` (da `47`, se c'è) + `03_Ad_Spy/` (da `19`/`52`/`62`) + `intermediate/sa3_financial_framework.md` (se c'è, per LTV/CAC reali)
**Output:** `01_VOC_Research/personas-<product>-<YYYYMMDD>.html` + `intermediate/persona_stack.md`
**Origine:** metodo 5-step di Alex Gough Cooper (Adcrate), adattato al sistema agentico.

---

## Perché esiste (e cosa NON è)

Il sistema aveva il **job** (SA2), il **segmento** (`48`) e l'**avatar singolo** (Foundation Pack di `18`). Mancava lo strato intermedio: **più persona psicografiche coesistenti**, ognuna con il suo peso sul budget creativo.

Distinzioni obbligatorie, altrimenti si duplica lavoro:

| Skill | Unità | Serve a |
|---|---|---|
| `48_segment_pain_prioritization` | **Segmento** = contesto + trigger | Decidere chi targettizzare (strategia) |
| `63_persona_stack` (questa) | **Persona** = psicografia | Decidere a chi parla ogni creative e con quanto budget (concept e produzione) |
| `18` Fase 3 Foundation Pack | **Avatar singolo** | Starter d'offerta |
| `22_character_creator` | **Personaggio visivo** | Volti/immagini, nessuna psicografia |

Una persona **non** è una demografica. "Mamma 45-55 con tre figli a Denver" è una demografica. Una persona è **come decide, cosa teme, quali sono i suoi valori, cosa le fa tirare fuori la carta**.

**I due errori da non fare:**
1. Etichettare demografiche come persona (età + geo + genere ≠ persona).
2. Identity overlay come "il regalatore" — quello è un **contesto d'acquisto**, non una persona: quasi ognuna delle tue persona reali può anche regalare.

**Le 4 domande che rendono una persona reale.** Se non sai rispondere a tutte e 4 con materiale VOC, non hai ancora una persona:
1. Cosa **crede già** del problema?
2. Cosa ha **già provato** e ha fallito?
3. Di cosa ha **paura**, e cosa **desidera segretamente**?
4. Quali **parole esatte** usa quando ne parla con sé stesso?

---

## STEP 1 — Lista persona: due passate indipendenti, poi il confronto

Il metodo originale fa dialogare due tool: uno forte nel tirare fuori evidenza (recensioni, commenti sotto le ads, survey post-acquisto), uno forte nel ragionare. **Noi non usiamo tool a pagamento di terzi**: la stessa dialettica si ottiene con due **passate separate su fonti diverse**, entrambe dentro Claude Code, senza che la prima contamini la seconda.

**Lo stack dell'evidenza (nessun tool esterno a pagamento):**
- recensioni competitor e di categoria via **`47_competitor_review_mining`** (Apify: Amazon, Trustpilot, G2, App Store, Google reviews)
- verbatim di prodotto e problem space via **`18_voc_research`** (ricerca web: Reddit, forum, community, Q&A, commenti social)
- transcript video ad alto engagement via **`20_ugc_scraper`** (TikTok)
- ads realmente in run dei competitor via **`19_ad_spy`** / **`52_ad_spy_video`** / **`62_google_ads_spy`**
- dati propri del cliente (survey, ticket, export ordini) via **`38_first_party_data_analysis`** — quando ci sono, battono tutto il resto



- **Passata A — evidenza.** Solo dallo stack qui sopra: verbatim VOC (`18`), recensioni competitor (`47`), first-party (`38`). Raggruppa per *pattern di decisione*, non per demografica. **Nessuna inferenza**: se non è scritto da un cliente reale, non entra.
- **Passata B — ragionamento.** Da JTBD + Forces of Progress (SA2 Fase 1-2) + ad spy cross-canale (chi stanno realmente targettizzando i competitor su Meta **e** su Google: `19`/`52`/`62`). Ipotizza le persona che il mercato implica. Falla **in una sessione separata**, senza rileggere l'output della passata A.

Poi il **riff**: metti A e B a confronto esplicito in tabella.

| Persona candidata | Emersa da | Sopravvive all'altra passata? | Verdetto |
|---|---|---|---|
| | A / B / entrambe | sì / no / parzialmente | tieni / scarta / unisci |

Tieni **3-5 persona** che passano il gate delle 4 domande. Chi emerge solo da B (ragionamento) senza uno straccio di verbatim a supporto va marcata **"ipotesi da validare"**, non promossa a persona.

---

## STEP 2 — Deep dive: 10 punti per persona

Per **ogni** persona che sopravvive, questi 10 blocchi. Ogni affermazione ancorata a VOC verbatim con fonte.

```
## PERSONA {N} — {nome memorabile}

### 1. Overview alla Gary Halbert
[un paragrafo, prima persona plurale, che descrive questa persona come la descriveresti
a un copywriter al bar: chi è, com'è la sua giornata, cosa la tiene sveglia. Denso, concreto,
zero aggettivi da brochure.]

### 2. Voice of customer — citazioni
[6-10 verbatim ESATTE di questa persona, con fonte. Slang, CAPS, errori inclusi.]

### 3. Le Cinque F
- **Fears** (paure): …
- **Frustrations** (frustrazioni quotidiane): …
- **Failures** (dove si sente fallita): …
- **Fantasies** (come immagina la vittoria): …
- **Failed solutions** (cosa ha già provato e perché non ha funzionato): …
[ognuna con verbatim]

### 4. Emozione + trigger moment
[Cosa stava PROVANDO nell'istante esatto in cui ha tirato fuori la carta. Non "voleva risparmiare
tempo": l'emozione e la scena. Collegato allo struggling moment / switch trigger del JTBD di SA2.]

### 5. Awareness + sophistication (Schwartz)
- Livello di consapevolezza: [unaware → most-aware] → implicazione sull'angolo di entrata
- Sofisticazione di mercato: [1-5, quante promesse ha già sentito] → quanto va meccanizzata la promessa

### 6. Buying journey
[First thought → passive looking → active looking → deciding → first use. Dove cerca, chi consulta,
quanto ci mette, chi altro decide con lei.]

### 7. Mappa dei bias cognitivi
[3-5 bias che governano davvero questa decisione, con la leva corrispondente.
Carica `09_marketing_psychology` per la tassonomia. Ogni bias con l'evidenza VOC che lo suggerisce.]

### 8. Objection stack
[Le obiezioni in ORDINE di comparsa nel processo decisionale, non alla rinfusa.
Per ognuna: verbatim + a che punto del journey compare + cosa la neutralizza.]

### 9. Soluzioni concorrenti già provate
[Cosa usa oggi, incluso il fai-da-te e il "non fare niente". Perché non basta — verbatim.
Incrocia con la gap map di `47`.]

### 10. Copy phrases pronte
[10-15 frasi VOC verbatim utilizzabili così come sono in un'ad. Non headline scritte da noi:
frasi già dette dal mercato. Sono il materiale grezzo di `54_headline_bank` e `28_meta_copy`.]

### ✓ Confidence check
| Sezione | Confidenza | Perché |
|---|---|---|
| 1-10 | alta / media / **da validare dall'umano** | [n. di fonti, qualità dei verbatim] |
```

Il **confidence check è obbligatorio**: il modello dichiara dove è solido e dove sta inferendo. È ciò che rende la revisione umana rapida invece che integrale — e alimenta il GATE 1 di `33`.

> Il principio: **il copy è già scritto nella testa del prospect.** Non stiamo convincendo di qualcosa di nuovo, stiamo identificando ciò che già teme e desidera e lo incanaliamo verso il prodotto.

---

## STEP 3 — Content diet: cosa consuma già questa persona

Sapere chi è non basta: serve sapere **su cosa si ferma già** mentre scrolla.

1. **Reverse-engineering delle query.** Per ogni persona, genera 8-12 termini di ricerca esatti che digiterebbe *partendo dalle sue paure e desideri* (Cinque F), non dal nome del prodotto.
2. **Cerca davvero quei termini** su TikTok e YouTube (`20_ugc_scraper` per i transcript virali; WebSearch per il resto).
3. Per ogni reference annota: tipo di creator · produzione alta o girato a telefono · **dove** è girato (bagno, cucina, auto, ufficio) · talking head o montato a stacchi · luce · durata dell'hook.
4. Salva i 3-5 migliori esempi **dentro il doc della persona**, con link.

Output: quando la produzione creativa lavora su questa persona non indovina l'estetica — la ricalca da ciò che la persona già guarda di sua iniziativa. Alimenta direttamente `57_ugc_studio` e `58_ugc_blueprint`.

---

## STEP 4 — Ranking e allocazione creativa

Una tabella, punteggio 1-10 per riga. Usa i numeri veri del brand (margine, LTV, retention) se disponibili; altrimenti stima e **marcalo come stima**.

| Persona | Contributo a fatturato | LTV | CAC (10 = più basso) | Priorità strategica | Media | % sprint creativo |
|---|---|---|---|---|---|---|
| Persona 1 | 9 | 8 | 7 | 9 | 8.3 | 45% |
| Persona 2 | 7 | 7 | 8 | 7 | 7.3 | 40% |
| Persona 3 | 5 | 6 | 5 | 4 | 5.0 | 15% |

La media dà il peso: quanta parte del prossimo sprint creativo va a ciascuna persona.

**Regola di test con intenzione:** ogni ad prodotta viene taggata con la persona che serve **e con l'ipotesi** ("vincerà perché…"). A fine sprint si chiude il loop con un report — cosa ha funzionato, cosa no — e si aggiornano i pesi. Senza il tag e l'ipotesi, il test non insegna niente: è spaghetti al muro.

Questa tabella è un vincolo esplicito per i **concept** (quanti per persona) e per la **produzione** (quante creative).

---

## STEP 5 — Persona sottoservite nell'ad account *(opzionale, serve account attivo)*

Solo se il brand ha già uno storico su Meta.

1. Da `50_meta_analyze` (Meta Ads MCP) prendi lo spend degli ultimi 90 giorni.
2. Tagga ogni ad esistente con la persona che sta servendo (leggendo copy + creative).
3. Confronta due percentuali:

| Persona | % delle recensioni/VOC (share of voice) | % dello spend | Delta |
|---|---|---|---|
| | 50% | 10% | **-40 → opportunità** |

Un delta negativo grande = persona che genera clienti ma non riceve creative. È l'insight a più alto ROI di tutta la ricerca, e nel caso Adcrate ha prodotto 3 delle top 5 ad dell'account.
Delta positivo grande = stai spendendo su una persona che non compra: candidato al taglio.

---

## Output

**A) `intermediate/persona_stack.md`** — testo strutturato per le fasi a valle (`33`, `48`, strategia, concept, copy).
**B) `01_VOC_Research/personas-<product>-<data>.html`** — documento self-contained, CSS inline, una scheda per persona con i 10 blocchi, il content diet e la tabella di allocazione. Zero dipendenze esterne.
**C) blocco `personas` in `market-data.json`** — una riga per persona con `id`, `name`, `alloc`, `confidence`, `trigger`, `pain`, `desire`, `objection`, `quote`, `src` (schema completo in `.claude/agents/sa2_market_research.md`). È ciò che alimenta hub e artifact senza rimappature.

## Regola del file vivo

Il persona doc **non è un one-shot**. Ogni nuova recensione, commento sotto un'ad, thread Reddit è materiale da aggiungere. Rilancia la skill in append (non riscrivere da zero) quando arriva VOC nuova, e ricontrolla mensilmente lo Step 5. Metti in testa al file la data dell'ultimo aggiornamento e il numero di fonti su cui poggia.

## Regole ferree
- Nessuna persona senza verbatim. Una persona senza citazioni è un'ipotesi: etichettala così.
- Niente demografiche spacciate per persona, niente contesti d'acquisto spacciati per persona.
- Confidence check obbligatorio su ogni sezione.
- Le "copy phrases" sono verbatim del mercato, non copy nostro. Il copy si scrive dopo, nella fase di copywriting.

## Definition of done
- [ ] 3-5 persona, ognuna con i 10 blocchi popolati
- [ ] Ogni persona ancorata ad almeno un verbatim con fonte; quelle senza sono etichettate `ipotesi`
- [ ] Confidence check per sezione, con il livello dichiarato (alta / media / bassa)
- [ ] Allocazione % del budget creativo che **somma a 100**, con il razionale dell'allocazione
- [ ] Dichiarato esplicitamente su cosa l'allocazione **non** è ottimizzata (senza dati economici del brand: non è CAC/LTV-driven — va detto)
- [ ] Persona che non vanno mai fuse dichiarate come tali, con il motivo
- [ ] Geo o segmenti non validati dichiarati come gap
- [ ] `market-data.json` parsabile con il blocco `personas` popolato

## Handoff
→ **`48_segment_pain_prioritization`** (le persona informano gli attributi della matrice attributi×pain)
→ **`33_insight_synthesis`** (dim 4 Key Segment, dim 5 Pain, dim 7 Obiezioni + il confidence check confluisce nel GATE 1)
→ **concept creativi** (`53_ad_angles`: un angolo per persona) · **copy** (`54_headline_bank`: le copy phrases sono il seed) · **produzione asset** (content diet = reference estetica)
