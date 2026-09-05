# Motore condiviso — Format Teardown (reverse-engineering a livello di ricreazione)

Riferimento condiviso usato da `19_ad_spy` (fase EXTRACT, a tempo di scrape) e `24_static_ads` (fase EXTRACT sui winner del brand + fase TRANSFORM per ogni rebrand). Trasforma un'immagine ad statica (competitor o del brand stesso) in una **CATTURA DI FORMATO A LIVELLO DI RICREAZIONE**: un prompt scritto così completo che incollarlo nel modello immagine **SENZA IMMAGINE DI RIFERIMENTO** rigenera essenzialmente lo stesso ad, più una mappa di swap che dice esattamente cosa ribrandizzare. Implementazione unica di teardown del progetto — nessuna skill tiene una copia privata di questa logica.

Perché esiste: un ad che ha sopravvissuto a spesa reale ha vinto con l'INTERO design — layout, device, tipografia, disciplina colore, composizione esatta, il modo in cui sta organico nel feed. Il design si mantiene a piena fedeltà, di proposito. Quello che non può mai passare è l'IDENTITÀ della fonte: le sue parole, i suoi marchi, il suo prodotto, i suoi numeri, i suoi colori brand. Questo modulo cattura l'intero ad fedelmente e mappa i pezzi identitari meccanicamente, così chi consuma può tenere il design e scambiare l'identità.

Due fasi, mai confonderle:

- **F-EXTRACT** ricrea l'ad sorgente come prompt paste-ready, INCLUSO il testo on-image verbatim, gli hex osservati esatti, ogni dettaglio visivo alla fedeltà di un reverse-engineer manuale frame-by-frame. L'artefatto è competitive intelligence e base per il rebrand; non va MAI spedito com'è come ad di qualcuno.
- **F-TRANSFORM** ribrandizza la ricreazione per UN brand: il design resta, l'identità si scambia (tutte le parole, marchi, prodotto, numeri, colori brand), poi girano i gate di rebrand (serie R sotto). Solo un rebrand che ha passato i gate, più l'adjacency pass (`adjacency_kill_pass.md`), può arrivare a un renderer.

---

## F.1 — Quando gira, chi chiama, variabili

F-EXTRACT gira quando chi chiama ha un'immagine ad SU DISCO LOCALE e vuole bancarne il formato per riuso. Chiamanti: `19_ad_spy` (a tempo di scrape, mentre i byte immagine sono freschi) e `24_static_ads` (sui winner live del brand stesso, Step 3b). Modulo caller-neutral; ogni chiamante futuro passa le stesse variabili.

Variabili in ingresso, tutte obbligatorie salvo indicato:

- `{{AD_IMAGE_PATH}}`: path locale del file creativo. Solo file locale, mai un URL — gli URL immagine di Meta Ad Library scadono in pochi giorni.
- `{{AD_ARCHIVE_ID}}`: l'archive id Meta Ad Library dell'ad sorgente.
- `{{PAGE_NAME}}`, `{{SLUG}}`: nome pagina e slug del competitor (o del brand stesso).
- `{{HEADLINE}}`, `{{BODY}}`, `{{CTA_TEXT}}`: i campi testo snapshot dell'ad (stringa vuota se assenti).
- `{{TIER}}`: il tier di scoring a tempo di scrape (es. PROVEN WINNER, HOT RUNNER).
- `{{WHY_THIS_WINS}}`: l'analisi in un paragrafo di chi chiama sul perché questa creative vince (opzionale, portata verbatim nell'artefatto se presente).
- `{{OUTPUT_DIR}}`: la directory che riceve l'artefatto, normalmente `$WORKDIR/03_Ad_Spy/_scratch`.

F-EXTRACT richiede di GUARDARE davvero: leggi `{{AD_IMAGE_PATH}}` col tool Read prima di scrivere un solo campo. Mai un teardown dal solo testo snapshot.

## F.2 — Cosa passa in un ad del brand, cosa deve scambiarsi

Il prompt di ricreazione cattura TUTTO a piena fedeltà. Questa tabella governa il TRANSFORM, non l'extract: a tempo di rebrand la colonna "resta" passa nell'ad del brand invariata, la colonna "scambia" viene sostituita con l'identità del brand.

| Categoria | RESTA (il design, a piena fedeltà) | SCAMBIA (l'identità, sostituita al rebrand) |
|---|---|---|
| Anatomia layout | L'intera mappa zone, posizioni percentuali, rapporti dimensionali, spazio negativo, composizione | |
| Grammatica del device | I device COSÌ COME SONO: l'annotazione, il calendario che brucia, lo scontrino diviso, l'esatta meccanica visiva che ha vinto | Un device che È il prodotto/mascotte del competitor (scambia il soggetto con l'equivalente del brand, tieni la meccanica) |
| Tipografia | L'intero sistema osservato: classe display, pesi, case, gerarchia, dimensioni | Niente (un typeface custom distintivo si descrive per classe di stile, basta per tenere la sensazione) |
| Colore | La DISCIPLINA di palette e i rapporti di contrasto (quali ruoli sono scuri, quali risaltano, quanto forte colpisce l'accento) | Gli hex brand della fonte: ricolora ai colori brand del brand; quando il brand non ha un equivalente, scegli hex di sostituzione con LO STESSO rapporto di contrasto così il design si legge identico |
| Copy | Conteggio parole, casing, ritmo, pattern di enfasi, a capo, dove sta il colpo | Ogni stringa. L'ad del brand non contiene zero frasi della fonte; ogni zona testo si riempie col messaggio del brand, length-matched all'originale |
| Prova | La forma visiva, il peso e la posizione dell'elemento di prova | I loro numeri, testimonianze, nomi stampa, credenziali: solo i fatti sourced del brand riempiono questi slot |
| Marchi | Niente | Loghi, wordmark, mascotte, persone identificabili, product art unica: sempre del brand o rimossi |

**Legge feed-native:** l'ad ribrandizzato deve sentirsi ORGANICO nel feed quanto lo era la fonte. Se la fonte si leggeva come una card di testo casual, uno screenshot, uno scontrino, una foto con energia phone-camera, il rebrand tiene quell'energia. Rendere lucido un winner che sembrava organico è un fallimento di rebrand.

## F.3 — Procedura F-EXTRACT

Leggi l'immagine, scrivi il prompt di ricreazione contro la regola unica F.5 (incollarlo senza immagine di riferimento rigenera questo ad), poi costruisci ogni blocco dello schema F.6 come registro di cosa hai visto e mappa di swap per il rebrand:

1. **Canvas**: aspect ratio, dimensione canvas apparente, trattamento sfondo come ruolo colore con hex osservato.
2. **Zone**: ogni regione distinta ha una voce zona: id (Z1, Z2...), ruolo in parole semplici, posizione (percentuale), tipo contenuto (headline, body, lista puntata, prop, prova, firma, cta), classe font, ruolo colore, e un verdetto `reuse`: `swap` (slot resta, contenuto sostituito), `optional` (slot può essere droppato), `drop` (mai riprodurre, es. zona mascotte competitor). Ogni zona testo registra `word_count_budget` e `char_count_budget` dal testo sorgente reale.
3. **Sistema tipografico**: la gerarchia di classi come stili, mai come identificazione brand.
4. **Ruoli colore**: ogni colore osservato mappato a un ruolo, con `is_competitor_brand_color` true/false.
5. **Grammatica device**: le categorie di device presenti, in linguaggio semplice.
6. **Device firma**: la lista BREVE dei device compositivi che rendono quest'ad riconoscibilmente di QUESTO inserzionista (uno stile di annotazione, una regola colore-accento isolata, un marchio ricorrente). Fanno parte del DESIGN vincente che il rebrand preserva; il rebrand tiene la meccanica del device e scambia solo il soggetto quando il device è il prodotto/mascotte del competitor. Almeno una voce quando l'ad ha un device distintivo; lista vuota va giustificata in `why_this_wins`.
7. **Registro testo verbatim**: ogni stringa di testo nell'ad, verbatim, ognuna con zone id, conteggio parole, conteggio caratteri, un `swap_role` che descrive che tipo di sostituzione serve, e `render_in_member_ad: false`. Il registro È la mappa di swap e il binario anti-copia a tempo di rebrand: queste stringhe APPAIONO nel prompt di ricreazione (è il punto della recreation grade), e non possono MAI apparire nel prompt/render ribrandizzato del brand.
8. **Marchi presenti**: ogni marchio competitor in frame, ognuno con `must_swap: true`.
9. **Classificazione**: meccanica hook, stadio awareness, hint famiglia formato (usa un nome canonico da `24_static_ads/references/format_families.md` quando calza; testo libero solo se nessuno calza — su collisione wrapper+contenuto, vince la famiglia WRAPPER), e la nota scheletro-retorico (F.4).

## F.4 — La nota scheletro-retorico

Riduci l'argomento centrale dell'ad sorgente a UNA frase astratta con placeholder X/Y, es: "Categoria X nominata come frammento, poi una credenziale di N anni, poi un'antitesi X-non-scala-Y-sì." Registrala in `classification.rhetorical_skeleton_note`. Sotto il modello di ricreazione questa nota è intelligence, non un gate: dice allo scrittore del rebrand quale LAVORO argomentativo fa ogni zona testo, così il copy sostitutivo del brand fa lo stesso lavoro con la sua claim, il suo nemico, i suoi numeri, nella sua voce. Le parole cambiano sempre completamente (gate R3); il lavoro argomentativo fa parte del design che ha vinto.

## F.5 — Il prompt di ricreazione: una regola, il risultato

L'artefatto porta un `condensed_prompt` (il PROMPT DI RICREAZIONE). Una sola regola su come si scrive, ed è il risultato:

**Uno sconosciuto che incolla questo prompt nel modello immagine, senza immagine di riferimento, ottiene indietro essenzialmente lo stesso ad che stai guardando. Stesso layout, stesse parole negli stessi posti, stessi colori, stesso mood, stessa energia da feed.**

Nessun template governa la scrittura. Nessuna struttura richiesta, nessuna sezione richiesta, nessun form da riempire. Guarda l'ad e scrivi qualunque prompt ottenga il risultato, come lo faresti a mano per te stesso. Prima di bancarlo, giudicalo solo contro il risultato: "se lo incollassi ora, otterrei INDIETRO questo ad?" Se una parte della risposta è no, il prompt non è finito.

Vincoli operativi (fisica e legge, non regole di scrittura):

- Al massimo 3800 caratteri: il tetto di render dei modelli classe GPT Image 2 è ~4000, e le stringhe scambiate al rebrand possono correre leggermente più lunghe delle originali.
- Ogni stringa del registro appare nel prompt (self-check 3).
- Ogni stringa scritta senza trattini lunghi (vedi F.6).
- Il prompt di ricreazione è intelligence e base di rebrand. MAI dispatchato com'è come ad di qualcuno; solo un rebrand F-TRANSFORM di esso spedisce.

## F.6 — Contratto di output

Scrivi due file in `{{OUTPUT_DIR}}`:

- `format-{{SLUG}}-{{AD_ARCHIVE_ID}}-<YYYYMMDD>.json`, l'artefatto, esattamente questa forma:

```json
{
  "format_id": "F-<ad_archive_id>", "schema_version": 2, "fidelity": "recreation",
  "status": "extracted_not_transformed", "created_by_skill": "<skill chiamante>", "created_at": "<ISO>",
  "source": {
    "slug": "", "page_name": "", "page_id": "", "ad_archive_id": "",
    "ad_library_url": "", "tier_at_scrape": "", "days_running_at_scrape": 0,
    "is_active_at_scrape": true, "scraped_at": "", "swipe_file": "",
    "local_image_path": "", "duplicate_creative_count": 1,
    "duplicate_ad_archive_ids": [], "why_this_wins": ""
  },
  "classification": { "hook_mechanic": "", "awareness_stage": "", "format_family_hint": "", "rhetorical_skeleton_note": "", "signature_devices": [] },
  "structure": {
    "canvas": { "aspect_ratio": "", "size_px": "", "background": "" },
    "zones": [ { "zone_id": "Z1", "role": "", "position": "", "content_type": "", "font": "", "color": "", "reuse": "swap", "word_count_budget": 0, "char_count_budget": 0 } ],
    "typography_system": [], "color_roles": [ { "role": "", "hex_observed": "", "is_competitor_brand_color": false } ],
    "device_grammar": [], "photographic_style": "", "proof_devices": []
  },
  "verbatim_text_ledger": [ { "zone_id": "", "verbatim_text": "", "render_in_member_ad": false, "swap_role": "", "word_count": 0, "char_count": 0 } ],
  "brand_marks_present": [ { "zone_id": "", "type": "", "must_swap": true } ],
  "condensed_prompt": "", "condensed_prompt_char_count": 0
}
```

- Stesso basename `.txt`: solo il prompt di ricreazione, leggibile dall'utente, prima riga esattamente `PROMPT DI RICREAZIONE, rigenera l'ad sorgente com'è, esegui 24_static_ads per ribrandizzarlo per il tuo brand prima di spedirlo`.

Self-check a tempo di produzione, tutti devono passare:

1. Il JSON riparsa pulito.
2. `condensed_prompt_char_count` ≤3800 e combacia con un conteggio reale.
3. COMPLETEZZA: una scansione case-insensitive normalizzata sugli spazi trova OGNI stringa del registro dentro `condensed_prompt`. Stringa mancante = il prompt non può ricreare l'ad, aggiungila.
4. Zero caratteri trattino-lungo/medio ovunque in entrambi i file.

Gli artefatti sono immutabili una volta scritti. Mai sovrascrivere; un redo richiesto scrive un nuovo file con suffisso `-2`.

## F.7 — F-TRANSFORM, il rebrand e i suoi gate

F-TRANSFORM ribrandizza una ricreazione per UN brand e UN concept. La legge in una riga: IL DESIGN RESTA, L'IDENTITÀ SCAMBIA. Parti dal prompt di ricreazione verbatim, poi fai esattamente questi cambi e nessun altro:

1. **Parole**: sostituisci ogni stringa del registro col messaggio del brand che fa lo stesso lavoro argomentativo (la nota scheletro di F.4 dice quale), nella voce Brand DNA del brand, length-matched ai conteggi parole/caratteri originali così il ritmo visivo provato sopravvive. Fonti: campi del concept approvato, Brand DNA, VOC verbatim, input esplicito dell'utente.
2. **Marchi e prodotto**: ogni voce `brand_marks_present` diventa il marchio del brand o si droppa; il prodotto della fonte diventa il prodotto del brand nella stessa posizione, scala, trattamento.
3. **Colori**: ricolora gli hex brand della fonte ai colori brand del brand dove calzano lo stesso ruolo. Quando la palette del brand non ha un colore adatto per un ruolo, scegli un hex di sostituzione con LO STESSO rapporto di contrasto ai vicini.
4. **Numeri e prova**: solo i fatti sourced del brand (vedi R4).

Tutto il resto nel prompt — layout, composizione, device, tipografia, texture, mood, energia feed — resta parola per parola dalla ricreazione. Dopo gli swap, riconta il prompt assemblato: al massimo 4000 caratteri.

Poi gira ogni gate. Tutti i gate girano silenziosamente; solo i fallimenti emergono, nella voce di chi chiama:

- **R1, Fedeltà del design.** Diffa il prompt ribrandizzato contro quello di ricreazione: ogni differenza deve tracciare a una delle 4 classi di swap sanzionate sopra. Un drift non sanzionato (zona spostata, device droppato, nuova idea di layout, restyle "più pulito") fallisce.
- **R2, Legge del recolor.** Ogni colore nel rebrand è o il colore non-brand della fonte tenuto com'è, o un colore brand del brand in un ruolo adatto, o una sostituzione a contrasto uguale. Nessuna invenzione di palette, nessun accento extra che la fonte non aveva.
- **R3, Swap completo delle parole, meccanico.** Zero stringhe da `verbatim_text_ledger` appaiono nel prompt/render ribrandizzato, case-insensitive, whitespace-normalizzato. Ogni zona testo ha avuto una sostituzione del brand. Esenzione: CTA standard di settore sotto le 4 parole ("Shop Now", "Scopri di più").
- **R4, Solo prova sourced, con scala di sostituzione.** Ogni zona numerica/prova sociale si riempie solo con fatti sourced di QUESTO brand. Quando non esiste un fatto equivalente, tieni il peso visivo della zona e sostituisci giù per questa scala: (1) una citazione VOC verbatim sized allo stesso peso, (2) una claim non-numerica specifica da Brand DNA, (3) solo come ultima risorsa lascia recedere la zona. Mai inventare un numero.
- **R5, Igiene marchi.** Nessun elemento renderizzato riproduce il wordmark/mascotte/illustrazione proprietaria della fonte o di terzi, o una persona identificabile. Marchi incidentali (un logo OS su un mockup telefono) si genericizzano.
- **R6, Integrità assemblaggio, meccanico.** Il prompt finale è il prompt di ricreazione più solo gli swap sanzionati, verificato da una scansione finale del registro (R3 ri-eseguito) dopo ogni revisione.

Dopo R1-R6, chi chiama esegue l'adjacency pass (`adjacency_kill_pass.md`) prima che qualsiasi cosa renderizzi.

## F.8 — Come i chiamanti consumano questo modulo

- **`19_ad_spy`**: banca una ricreazione per ogni creative unica trovata dallo scrape (qualsiasi tier, ordine tier, tetto per-brand-per-run) — solo F-EXTRACT, mai F-TRANSFORM, mai l'adjacency pass (nessun brand target, mai renderizza).
- **`24_static_ads`**: la reference bank è la SUA UNICA fonte strutturale (nessun template stock). Squarcia anche i winner LIVE del brand stesso in ricreazioni (source origin = il brand stesso). Poi REBRANDIZZA la ricreazione assegnata a ogni ad nel batch plan approvato via F-TRANSFORM + gate R (le ricreazioni del brand stesso saltano il requisito R3 di full-word-swap quando il brand evolve il proprio copy, tengono R4 e R6).
- **Shell legacy**: artefatti con `schema_version: 1` (pre-ricreazione) non sono basi di rebrand. Quando ne viene scelto uno, ri-esegui F-EXTRACT sulla sua `source.local_image_path` per produrre una ricreazione `schema_version: 2` con suffisso `-2`, poi ribrandizza quella.

## F.9 — Regole hard

| Regola | Dettaglio |
|---|---|
| Solo immagini locali | F-EXTRACT legge un file locale. Gli URL Ad Library scadono in giorni e non sono mai input di teardown. |
| Guarda prima di scrivere | L'immagine si legge col tool Read prima di autorare un campo. Mai teardown dal solo testo snapshot. |
| Il registro non renderizza MAI in un ad del brand | `verbatim_text_ledger[].verbatim_text` appare nel prompt di ricreazione, mai in un prompt/render ribrandizzato. `render_in_member_ad` è false per sempre. |
| Una ricreazione non è un ad del brand | Il prompt di ricreazione rigenera l'ad SORGENTE. È intelligence e base di rebrand; dispatcharla non ribrandizzata come creative del brand è vietato. `status` resta `extracted_not_transformed` per sempre. |
| Artefatti immutabili | Mai sovrascrivere un teardown. Redo e upgrade di shell legacy prendono suffisso `-2`. |
| Una bank canonica | Tutti i teardown vivono in `$WORKDIR/03_Ad_Spy/_scratch/`, chiunque li abbia creati. |
| I gate si confermano, non si assumono | Ogni rebrand ri-esegue R1-R6 + l'adjacency pass per il proprio brand e il proprio run. |
| Niente trattini lunghi ovunque | In chat o nei file. |
