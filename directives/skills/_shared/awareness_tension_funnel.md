# Motore condiviso — Awareness + Tensione + Funnel + CTA

Riferimento condiviso usato da `53_ad_angles`, `54_headline_bank`, `55_video_script` (e riusabile da SA4/SA7 per copy/campagne). Definisce **come cambia il messaggio** in base allo stato del prospect, su due assi distinti:

- **Asse 1 — AWARENESS (Schwartz):** quanto il prospect SA.
- **Asse 2 — TENSIONE (layer):** quale pressione emotiva/identitaria sta vivendo ORA, indipendente da quanto sa.

Un ad preciso sceglie una cella su ENTRAMBI gli assi: "un Solution-Aware in frizione identitaria" è un ad diverso da "un Solution-Aware con una scadenza". L'awareness decide quanto spiegare; la tensione decide quale nervo colpisce l'hook.

---

## A.1 — I 5 stadi di awareness (Schwartz)

| Stadio | Il prospect | Stile hook | Profondità messaggio | Prova richiesta | Peso CTA |
|---|---|---|---|---|---|
| Unaware | Non sa di avere il problema | Pattern interrupt, storia, identity callout. MAI il prodotto | Massima: nomina il problema nascosto prima di tutto | Credibilità di chi racconta | Più soft (scopri, impara) |
| Problem-Aware | Sente il dolore, non sa che esistono soluzioni | Agita e rispecchia il dolore nelle sue parole | Alta: dolore prima, poi "la categoria di soluzione esiste" | Empatia + meccanismo credibile | Soft-medio |
| Solution-Aware | Sa che esistono soluzioni, non la tua | Differenzia: perché questo meccanismo batte la categoria | Media: guida col meccanismo unico | Evidenza del meccanismo, confronti | Medio |
| Product-Aware | Conosce il tuo prodotto, non ha comprato | Gestisci l'obiezione, mostra la prova | Bassa: obiezione + prova + offerta | Recensioni, garanzie, demo | Forte |
| Most-Aware | Lo vuole, aspetta una ragione | L'offerta, il drop, il promemoria | Minima: solo offerta e scadenza | L'offerta stessa | Massimo (compra ora) |

Leggi lo stadio dominante di DEFAULT dalla distribuzione awareness del VOC (`01_VOC_Research/` o `intermediate/insight.md`). Proponilo, lascia che l'utente lo sovrascriva in una parola.

## A.2 — I 5 layer di tensione (asse 2)

Applicabili a ogni nicchia; leggi la colonna che corrisponde al modello di business del brand.

| Layer | Lettura DTC/consumer | Lettura info/business | Energia hook |
|---|---|---|---|
| L1 Provato ma incostante | "Ha funzionato prima ma non regge, ricado sempre" (peso ripreso, pelle di nuovo irritata) | Ha risultati ma non li ripete, sbatte contro un soffitto | Deja-vu frustrato |
| L2 Sintomi strutturali | "Qualcosa non va e non so nominarlo" (sempre stanco, sempre gonfio) | Sente che il business non gira ma non sa diagnosticarlo | Nominare l'innominato |
| L3 Gap di meccanismo | "Troppe opzioni, non so cosa funziona davvero o perché il tuo è diverso" | Processo poco chiaro, differenziazione debole, si confonde nel mucchio | Rivelare il meccanismo |
| L4 Frizione identitaria | "Mi sento indietro, imbarazzato, non più me stesso" | Dubbio, confronto, disallineamento rispetto a dove dovrebbe essere | Specchio identitario |
| L5 Tensione avanzata | "Un trigger mi sta forzando la mano ORA" (matrimonio, post-parto, diagnosi, compleanno) | Spinta di crescita, pressione di pivot, scadenza strategica | Scadenza e decisione |

Il layer non è derivabile in automatico dai documenti: proponi il layer più plausibile dal VOC (territorio emotivo), poi conferma con l'utente. Se il layer scelto contraddice l'offerta o l'avatar, dillo chiaramente e proponi il layer corretto con una riga di motivazione.

## A.3 — Funnel stage, derivato dall'awareness

TOF/MOF/BOF descrivono la relazione col TRAFFICO, e mappano di default dalla distribuzione awareness:

- Unaware + Problem-Aware dominanti → default **TOF** (freddo)
- Solution-Aware dominante → default **MOF** (tiepido)
- Product-Aware + Most-Aware dominanti → default **BOF** (retargeting)

Proponi il default derivato ("il tuo pubblico skewa Problem-Aware quindi default TOF, ok?"). L'utente può sovrascrivere.

Regole strutturali per funnel stage:
- **TOF (freddo):** l'avatar non ha mai sentito il brand e non gliene importa. Niente brand-first, niente credenziali. Guida col SUO problema o trasformazione. Copy più lungo guadagna fiducia. Curiosità/agitazione calibrate sul layer di tensione.
- **MOF (tiepido):** conosce il brand, non ha comprato. Riferimento leggero alla familiarità, focus su OFFERTA e PROVA, gestisci la obiezione principale dal VOC, CTA più diretta.
- **BOF (retargeting):** ha visitato e non comprato. Richiama il comportamento ("avevi messo l'occhio su..."), rispondi direttamente all'obiezione, urgenza solo se reale, copy più corto, CTA più dura.

## A.4 — Intelligenza CTA

Scegli la CTA in 3 step: default di nicchia (vedi `niche_offer_types.md`), aggiusta per funnel stage, poi calibra sull'energia dell'ad.

| Funnel | DTC | Info | Service |
|---|---|---|---|
| TOF | Learn More, Shop Now (soft), Claim Sample | Register Free, Get the Free Training, Follow/Save | Get a Free Quote |
| MOF | Shop Now, Get the Bundle, Subscribe and Save | Join the Challenge, Download, Register | Book Now |
| BOF | Complete Your Order, Get X% Off, Subscribe and Save | Book a Call, Apply, Enroll Now | Schedule Today |

Una sola CTA per ad. Mai due richieste impilate. Mai forzare urgenza che i fatti non supportano (gate in `creative_claims_compliance.md`).

## A.5 — Contratto di output

Ogni volta che una skill calibra un concept o un pezzo di copy con questo motore, etichetta il lavoro con tutti e quattro i valori così le skill a valle e i reviewer possono verificare la congruenza:

```
awareness: <unaware | problem-aware | solution-aware | product-aware | most-aware>
tension_layer: <L1 | L2 | L3 | L4 | L5>
funnel_stage: <TOF | MOF | BOF>
cta: <testo esatto della CTA>
```

Un disallineamento tra queste etichette e il copy reale (es. una lezione sul meccanismo puntata su Most-Aware, o uno sconto nudo puntato su Unaware) è un fallimento di calibrazione e va riscritto, mai spedito.
