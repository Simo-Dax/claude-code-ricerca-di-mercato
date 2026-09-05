# SA2 — VOC Research (Voice of Customer)

**Agente:** SA2 (Market Research)
**Output:** `01_VOC_Research/voc-[product-name].html` + (opzionale) `01_VOC_Research/foundation-pack-[product-name].html`
**Prerequisiti:** Web search attivo (solo Fasi 1-2; la Fase 3 Foundation Pack non richiede ricerca)
**Reference (solo Fase 3):** `../_shared/awareness_tension_funnel.md` (belief chain), `../_shared/niche_offer_types.md` (tipo offerta), `../_shared/creative_claims_compliance.md` (gate prove)

---

## Input richiesti

Prima di iniziare, chiedi all'utente:
1. **URL prodotto** — pagina prodotto specifica (non homepage). Es: `brand.com/products/nome-prodotto`
2. **Nome prodotto** — come lo chiama il brand

Non procedere senza entrambi.

---

## Protezione cartella (esegui PRIMA di creare file)

```bash
PWD_ABS="$(pwd)"
TARGET="${PWD_ABS}"
PROTECTED=0
case "$PWD_ABS" in
  "$HOME"|"$HOME/"|"/"|"/tmp"|"/tmp/"|"$HOME/Downloads"|"$HOME/Desktop")
    PROTECTED=1 ;;
esac
if [ "$PROTECTED" = "1" ] && [ ! -d "$TARGET" ]; then
  echo "PROTECTED:$PWD_ABS"
elif [ ! -f "$TARGET/_meta/folder-confirmed.flag" ] && [ ! -d "$TARGET" ]; then
  echo "FIRSTRUN:$TARGET"
else
  mkdir -p "$TARGET/01_VOC_Research" "$TARGET/_meta"
  echo "READY:$TARGET"
fi
```

- `PROTECTED`: rifiuta, chiedi di aprire Claude Code in una cartella brand-specifica
- `FIRSTRUN`: chiedi conferma al membro, poi crea le cartelle e scrivi il flag
- `READY`: continua silenziosamente

---

## Procedura — Due Fasi

### FASE 1 — Ricerca VOC

Esegui ricerca web approfondita raccogliendo citazioni verbatim dei clienti da:
- Recensioni prodotto (Amazon, sito brand, Trustpilot, G2, Yelp)
- Community Reddit, forum di settore, gruppi Facebook
- Commenti social (TikTok, YouTube, Instagram)
- Q&A di prodotto
- Competitor simili (fallback se il prodotto ha poche recensioni)
- Problem space (dolori che il prodotto risolve)

**Soglie minime:**
- ≥ 30 citazioni verbatim da clienti reali
- Copertura di: dolori principali, desideri, linguaggio identitario, obiezioni, trasformazione

**Regola critica:** ogni citazione deve essere verbatim — slang, errori grammaticali, CAPS, punteggiatura emotiva inclusi. Zero parafrasi.

Quando Fase 1 è completa, di' all'utente: "Ricerca completata. Costruisco il documento..."

### FASE 2 — Documento HTML

Costruisci un documento HTML self-contained con:
- **CSS inline** — design pulito e professionale
- **Sezioni obbligatorie:**
  - Citazioni verbatim clienti (con fonte)
  - Dolori e linguaggio emotivo
  - Linguaggio identitario e segnali ICP
  - Obiezioni ricorrenti
  - Desideri e trasformazione attesa
  - Contesto competitor/problem space (se VOC diretta è scarsa)
  - **Matrice dolori frequenza × frustrazione** (vedi sotto — obbligatoria)
- **Nessuna dipendenza esterna** — file completamente portabile

Salva in: `<pwd>/01_VOC_Research/voc-[nome-prodotto].html`

Quando Fase 2 è completa, **offri la Fase 3**:
> "VOC completato. Vuoi anche il **Foundation Pack**? È la strategia d'offerta derivata da questo VOC (nessuna nuova ricerca): Customer Avatar Sheet + Offer Brief + le 6 Purchase Beliefs. (sì/no)"

Se sì → Fase 3. Se no → fine.

---

### FASE 3 — Foundation Pack (opzionale, derivato dal VOC, NESSUNA nuova ricerca)

Layer di offer-strategy costruito **interamente dal VOC** (Fasi 1-2 o un doc esistente) + Brand DNA se presente. Non scrive copy finito e non fa ricerca nuova: struttura ciò che il VOC già dice in una base d'offerta pronta per SA4/SA5/SA7.

**Trigger:**
- Dopo Fase 2, su "sì".
- **Standalone** su VOC esistente — l'utente dice "costruisci il foundation pack" / "build my foundation pack": salta Fasi 1-2, fai auto-discovery del VOC più recente:
  ```bash
  ls -t "$TARGET/01_VOC_Research/"voc-*.html 2>/dev/null | head -n 1
  ```
  Se nessun VOC esiste → di' che serve prima un VOC (`/pm-dati-qualitativi`) o l'utente incolla il doc.

**Input:** il VOC doc + (se presente) Brand DNA da `02_Brand_DNA/` o `context/brand/business_profile.md`+`tone_of_voice.md`. Carica `../_shared/niche_offer_types.md` (risolvi nicchia+tipo offerta), `../_shared/awareness_tension_funnel.md` (per la belief chain), `../_shared/creative_claims_compliance.md` (gate prove: ogni numero/claim tracciabile al VOC/Brand DNA o omesso).

**Produce 3 sezioni** (un unico HTML companion, stessa palette pulita del VOC doc):

1. **📋 Customer Avatar Sheet** — il compratore nelle SUE parole. Chi è (contesto + trigger event), giornata/momento tipo, dolore dominante (citazione VOC verbatim), desiderio/trasformazione attesa (verbatim), obiezioni principali (verbatim), linguaggio identitario ("come si descrive"). Ogni riga ancorata a una citazione VOC reale — zero avatar inventato.

2. **🎯 Offer Brief** — **Big idea** (una frase, la promessa centrale) · **Meccanismo** (il "perché funziona" unico e credibile, derivato da Brand DNA + VOC; se non derivabile, fai UNA domanda all'utente) · **3-5 headline candidate** (nominate col framework, dal linguaggio VOC — non copy finale, sono direzioni) · **Obiezioni + risposta** (top 3-5 dal VOC, ognuna con l'angolo che la neutralizza) · **Belief chain** (la sequenza di convinzioni dall'awareness stage dominante del VOC fino all'acquisto, calibrata via `awareness_tension_funnel.md`).

3. **🧠 6 Purchase Beliefs** — le 6 cose che un prospect DEVE credere prima di comprare, ognuna con: la belief, l'**evidenza VOC** che la supporta o la contesta (verbatim), e l'**implicazione per il copy** (cosa deve dimostrare l'ad). Le 6 di default (adatta i nomi al contesto): (1) "ho davvero questo problema/costa più di quanto pensi", (2) "è risolvibile", (3) "questo tipo di soluzione/meccanismo lo risolve", (4) "QUESTO prodotto ha quel meccanismo meglio delle alternative", (5) "vale il prezzo/rischio", (6) "è il momento di agire ora".

**Output:** `01_VOC_Research/foundation-pack-[nome-prodotto].html`.

**Relazione con SA4/SA5:** il Foundation Pack è uno **starter d'offerta veloce derivato dal VOC**, non sostituisce il full Brand Strategy di `32_brand_strategy` (🚦GATE 2) né l'`33_insight_synthesis`. Li **prefigura e alimenta**: SA4 può partire da qui invece che da zero. Se la pipeline completa girerà, segnalalo all'utente ("questo diventa input per SA4, non lo rimpiazza").

---

## Matrice dolori — frequenza × frustrazione (obbligatoria)

Un elenco di dolori ordinato per "quanto se ne parla" è mezzo insight. I dolori vanno scorati su **due dimensioni indipendenti**, mai fuse in un unico punteggio:

| Dimensione | Come si misura | Cosa significa |
|---|---|---|
| **Frequenza** | n. di verbatim in cui il tema compare / totale corpus → `freq` + `%` | quanto è diffuso |
| **Frustrazione** | rating medio delle recensioni che citano il tema (vs rating medio del corpus) + % di menzioni ad alta intensità (linguaggio estremo, caps, minacce di churn) | quanto brucia quando c'è |

Le due dimensioni raccontano cose diverse:
- **alta freq + alta frustrazione** → il dolore da attaccare in campagna
- **bassa freq + alta frustrazione** → nicchia rovente: alta conversione, basso volume. Ottimo per un segmento, pessimo come messaggio principale
- **alta freq + bassa frustrazione** → table stakes, non vende
- bassa/bassa → rumore

**Terza colonna obbligatoria: cosa fanno le alternative su quel dolore.** Per ogni riga marca `risolto` (i competitor lo coprono bene → non è leva) · `promesso ma non mantenuto` (lo dicono, le recensioni negative smentiscono → sweet spot) · `non presidiato` (nessuno lo nomina → white space). Senza questa colonna la matrice non produce posizionamento, solo descrizione.

```
| Tema | Freq | % corpus | Rating medio | % intense | Alternative | Driver di switch |
|------|------|----------|--------------|-----------|-------------|------------------|
```

Un tema con rating medio **sotto** la media del corpus è un dolore acuto anche se poco frequente: va segnalato esplicitamente, non sepolto in fondo alla tabella.

---

## Regole operative

- **Solo dati reali.** Nessun linguaggio inventato o sintetizzato.
- **Il prodotto prima del brand.** Ricerca prima le recensioni del prodotto specifico, poi espandi al brand.
- **Nessun hook o headline finale.** Fasi 1-2 raccolgono VOC grezza per copywriter. La Fase 3 produce direzioni strategiche (avatar/offer/beliefs), non copy finale — le headline candidate sono direzioni, non ad pronti.
- **Cascade fallback:** se le recensioni dirette sono scarse → VOC competitor → ricerca problem space.
- **Foundation Pack = derivato, mai inventato.** Ogni riga di avatar/offer/beliefs traccia a una citazione VOC verbatim o al Brand DNA. Nessuna nuova ricerca web in Fase 3. Numeri/claim: gate `creative_claims_compliance.md` (sourced o omessi).

---

## Validazione output

Prima di dichiarare completato (Fasi 1-2):
1. File esiste in `01_VOC_Research/voc-[nome-prodotto].html`
2. Dimensione > 20.000 byte
3. Almeno 30 citazioni verbatim presenti
4. Nessun placeholder (`{product_url}`, `{product_name}`, `[BRAND_NAME]`)
5. Tutte le sezioni obbligatorie popolate
6. Matrice dolori presente con **entrambe** le dimensioni (frequenza + frustrazione) e la colonna alternative
7. Dimensione del corpus dichiarata in testa al documento (n. fonti, n. verbatim, geo, finestra temporale) — un insight su 6 verbatim non pesa quanto uno su 300
8. Geo o segmenti **non** coperti dichiarati esplicitamente come gap, non taciuti
9. `market-data.json` scritto o aggiornato con i blocchi `corpus` e `themes` (schema in `.claude/agents/sa2_market_research.md`)

Se validazione fallisce → tenta auto-fix (ricerche aggiuntive) → se impossibile, report onesto all'utente con prossimi passi concreti.

**Fase 3 (Foundation Pack), se generata:**
1. File esiste in `01_VOC_Research/foundation-pack-[nome-prodotto].html`
2. Tutte e 3 le sezioni presenti (Avatar Sheet, Offer Brief, 6 Purchase Beliefs)
3. Ogni riga di avatar/beliefs ancorata a una citazione VOC verbatim (spot-check: nessuna affermazione senza fonte VOC/Brand DNA)
4. 6 Purchase Beliefs presenti, ognuna con evidenza VOC + implicazione copy
5. Nessun numero/claim non sourced (gate `creative_claims_compliance.md`)
