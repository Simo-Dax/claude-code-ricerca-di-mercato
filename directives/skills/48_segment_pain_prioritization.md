# Segment & Pain Prioritization — Acquisition (chi targettiamo e perché)

**Agente:** ponte insight→strategia, dentro/dopo `33_insight_synthesis`, prima di `32_brand_strategy`.
**Input:** `01_VOC_Research/` + `intermediate/competitor_review_gap.md` (da `47`) + `intermediate/sa3_financial_framework.md` (se disponibile, per profittabilità) + `context/brand/` + `intermediate/insight.md` **se già esiste** (opzionale — questa skill gira a step 3.5, prima di `33`, e ALIMENTA `33`; non dipende dall'insight finale)
**Output:** `intermediate/segment_pain_matrix.md` → feed a `33` dim 4-5 (Key Segment, Pain) e a `32` (VP per segmento) + targeting delle campagne
**Origine:** metodo Learnn fase 2-3 (segmentazione per contesto/trigger, prioritizzazione TAM). Distinta da `44_rfm` (retention/clienti esistenti) — qui è **acquisition** (prospect).

---

## Perché esiste

Un segmento non è una demografica ("donne 25-40"). È **un contesto + un trigger in cui un pain diventa azione**. Questa skill fa tre cose che il sistema non aveva esplicite:
1. **Scora i pain** su 2 assi (frequency × frustration) e li confronta con ciò che offrono le alternative.
2. **Mappa attributi del segmento × pain** per capire in quale contesto il dolore avviene.
3. **Definisce e prioritizza i segmenti** per contesto+trigger su 3 fattori macro (fatturato/profittabilità, accesso, crescita/TAM).

Il risultato vincola chi targettiamo (l'architettura delle campagne), cosa promettiamo (`32`), e dove l'AI smette e decide l'umano.

---

## Step 1 — Inventario pain (dal VOC + review gap)

Raccogli tutti i pain candidati da: VOC verbatim (`18`), recensioni negative competitor (`47`), forces of progress di SA2. Per ognuno tieni la **frase verbatim** + fonte. Niente pain inventati.

---

## Step 2 — Pain Matrix: frequency × frustration (vs alternative)

Scora ogni pain su 2 dimensioni (1-5) e confronta con ciò che le alternative già risolvono:

| Pain (verbatim) | Frequency (1-5) | Frustration (1-5) | Score (F×F) | Alternative lo risolvono? | Verdetto |
|---|---|---|---|---|---|
| "[pain 1]" | 5 | 4 | 20 | parzialmente | **Sweet spot** |
| "[pain 2]" | 4 | 5 | 20 | no | **White space** |
| "[pain 3]" | 5 | 2 | 10 | sì, bene | Table stakes |

- **Frequency** = quanto spesso emerge (quante fonti/recensioni lo toccano).
- **Frustration** = quanto è intenso/doloroso (linguaggio emotivo, rating, abbandono).
- **Verdetto:**
  - alto F×F **e** alternative NON lo risolvono → **White space** (massima priorità, è qui che si vince).
  - alto F×F **e** alternative lo risolvono solo in parte → **Sweet spot** (lo facciamo meglio).
  - alto F×F **e** alternative lo risolvono bene → **Table stakes** (obbligatorio pareggiare, non differenzia).
  - basso F×F → deprioritizza (non muove l'acquisto).

I pain White space + Sweet spot diventano il cuore di VP e messaggi.

---

## Step 3 — Matrice Attributi × Pain (in quale contesto avviene)

Per capire **dove** il pain morde, mappa gli attributi possibili del segmento (righe = attributi, colonne = i pain prioritari dello Step 2). Attributi tipici:
- Demografiche (età, genere, reddito, area)
- Professione / ruolo
- Tipologia azienda (B2B: dimensione, settore, fase)
- Interessi / lifestyle
- Stato (salute, fase di vita, livello esperienza)
- Comportamento (heavy/light user, canale, occasione d'uso)

| Attributo | Pain 1 (white space) | Pain 2 (sweet spot) | Pain 3 |
|---|---|---|---|
| [es. neo-genitori] | forte | medio | — |
| [es. professionisti 9-18] | medio | forte | forte |
| [es. PMI 10-50 dip.] | — | forte | medio |

Le **celle ad alta intensità** rivelano quali combinazioni attributo×pain sono i candidati-segmento reali (lì il dolore è acuto e contestuale).

---

## Step 4 — Definizione segmento per CONTESTO + TRIGGER

Per ogni segmento candidato (dalle celle calde dello Step 3) definiscilo NON come demografica ma come **situazione**:

```
Segmento: [nome memorabile]
Chi: [attributo/i dominanti]
Contesto: [la situazione concreta in cui vive il pain]
Trigger: [l'evento che fa scattare la ricerca della soluzione — collegato allo struggling moment JTBD di SA2]
Pain dominante: [da Step 2, verbatim]
Desiderio dominante: [come appare "vincere" nelle sue parole]
```

Il trigger è ciò che rende il segmento targettizzabile: si compra quando succede X, non "in generale".

---

## Step 5 — Prioritizzazione segmenti (3 fattori macro)

Scora ogni segmento candidato sui 3 fattori (1-5) e ordina:

| Segmento | Fatturato/Profittabilità | Facilità di accesso | Crescita / TAM | Score | Priorità |
|---|---|---|---|---|---|
| [Seg A] | 5 | 4 | 3 | 12 | 1° |
| [Seg B] | 3 | 5 | 4 | 12 | 2° |
| [Seg C] | 4 | 2 | 5 | 11 | 3° |

- **Fatturato/Profittabilità** — quanto vale (AOV/LTV, margine; usa i dati economici del brand se disponibili).
- **Facilità di accesso** — quanto è raggiungibile/targettizzabile a costo sostenibile (canali, intent, audience size addressable).
- **Crescita / TAM** — quanto è grande e in che direzione va (mercato in crescita > maturo).

Pareggio o score vicini → l'umano decide (decisione di mercato, GATE 1). Raccomanda **1 segmento prioritario** motivando con i 3 fattori; tieni 1-2 secondari per espansione.

---

## Output → `intermediate/segment_pain_matrix.md`

```markdown
# Segment & Pain Prioritization — {Brand} — {Data}

## 1. Pain Matrix (frequency × frustration vs alternative)
## 2. Matrice Attributi × Pain (contesto)
## 3. Segmenti definiti (contesto + trigger)
## 4. Prioritizzazione segmenti (3 fattori macro)
## 5. Raccomandazione: segmento prioritario + motivazione
## ⚠️ DA VALIDARE DALL'UMANO
[la scelta del segmento prioritario è una decisione di mercato — confluisce nel GATE 1]
```

---

## Regole ferree
- **Pain verbatim + fonte** — niente pain inventati o generici.
- **Il segmento è contesto + trigger, non demografica.** Una demografica senza trigger non è targettizzabile.
- **Confronta sempre con le alternative** — un pain già risolto bene dai competitor non differenzia, anche se frequente.
- **Prioritizza, non accumulare** — un segmento prioritario chiaro batte cinque segmenti tiepidi. Meglio dominare una nicchia che diluirsi.
- **La scelta finale del segmento è umana** (GATE 1): l'AI scora e raccomanda, l'umano decide.

---

## Handoff
`segment_pain_matrix.md` →
- **`33_insight_synthesis`**: alimenta il building block 4 (Key Segment, con contesto+trigger+priorità), il 5 (Pain prioritizzati) e l'8 (Trigger point).
- **`32_brand_strategy`**: la VP e i benefici si scrivono PER il segmento prioritario; i pain white space → USP.
- **Architettura delle campagne**: il segmento+trigger guida targeting, audience stack e angoli per fase funnel.
- **Framework finanziario**: la profittabilità del segmento informa AOV/LTV target.
