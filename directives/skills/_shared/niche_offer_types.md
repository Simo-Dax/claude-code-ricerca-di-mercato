# Motore condiviso — Nicchia e tipo di offerta

Riferimento condiviso usato da `53_ad_angles`, `54_headline_bank`, `55_video_script`. Risponde a due domande che cambiano focus di messaging, stile di prova, set di CTA e gate di compliance: CHE TIPO di business è il brand (nicchia), e CHE TIPO di offerta sta spingendo questo run.

---

## N.1 — Le tre nicchie

| Nicchia | Chi | Default? |
|---|---|---|
| `dtc` | Brand ecommerce prodotto fisico su Meta/TikTok | SÌ, default |
| `info` | Coach, corsi, infoprodotti, community | rilevata o dichiarata |
| `service` | Servizi locali/online (booking, preventivo, consulenza) | rilevata o dichiarata |

## N.2 — Rilevazione (una volta, poi persisti)

Rileva da `context/brand/business_profile.md` + VOC (mai assumere):
- Descrive prodotto fisico, packaging, spedizione, ingredienti → `dtc`
- Descrive programma, corso, coaching, community, webinar, "studenti"/"clienti", "iscriviti" → `info`
- Descrive appuntamenti, preventivi, area locale, "prenota una call" come l'acquisto stesso → `service`

Conferma UNA volta per brand: "Dai documenti questo sembra un brand `<nicchia>`, corretto?". Se `context/brand/business_profile.md` già dichiara il modello di business, usalo silenziosamente senza richiedere conferma.

## N.3 — Tipi di offerta DTC (mappa di default)

| Tipo offerta | Focus messaging | Mindset avatar da rispondere | CTA default |
|---|---|---|---|
| Prodotto singolo | La trasformazione + il meccanismo credibile | "funzionerà davvero per me?" | Shop Now |
| Bundle | Completezza e value math, la routine risolta in una mossa | "è un affare migliore che comprarli singoli?" | Get the Bundle |
| Abbonamento | Convenienza mai-a-corto, rituale, risparmio per unità | "mi sto legando?" (rispondi cancel-anytime) | Subscribe and Save |
| Trial/campione gratis | Risk reversal, prova-prima-di-impegnarti | "qual è la fregatura?" | Claim Your Sample |
| Nuovo lancio | Novità, sensazione first-mover, storia della waitlist | "perché dovrebbe interessarmi uno nuovo?" | Shop the Launch |
| Sconto/promo | Scadenza e sconto reali, urgenza SOLO se reale | "lo sconto è vero?" | Get X% Off |

## N.4 — Tipi di offerta Info (quando nicchia = info)

| Tipo offerta | Focus messaging | Mindset avatar | CTA default |
|---|---|---|---|
| Webinar (live/evergreen) | L'INSIGHT/breakthrough, mai il webinar in sé | "vale il mio tempo?" | Register Free |
| Training/corso gratis | La competenza/conoscenza specifica ottenuta | "è davvero gratis, qual è la fregatura?" | Get Access |
| Prodotto low-ticket | Valore immediato, quick win, usabilità copy-paste | "posso usarlo subito?" | Get It Now |
| Challenge (5-7 giorni) | Il quick win completato entro la finestra | "riesco davvero a farlo?" | Join the Challenge |
| Community gratis | Identità e appartenenza, "la tua gente" | "mi ci troverò?" | Join Now |
| Programma high-ticket | Timeline della trasformazione + prova sociale reale | "posso fidarmi di questa persona con questi soldi?" | Book a Call |

## N.5 — Tipi di offerta Service (quando nicchia = service)

| Tipo offerta | Focus messaging | CTA default |
|---|---|---|
| Booking/appuntamento | Risultato + facilità di iniziare | Book Now |
| Preventivo | Velocità e no-obbligo | Get a Free Quote |
| Consulenza | Il problema specifico risolto sulla call | Schedule a Call |

## N.6 — Gate di compliance per nicchia (passali a `creative_claims_compliance.md`)

- **`info`:** gate GUADAGNI. Mai dichiarare o implicare un livello/range di reddito, vendite o profitto ("fai 10k€/mese", "6 cifre in 90 giorni") senza sostanziazione scritta fornita dall'utente + contesto di risultato tipico. Default: vendi la trasformazione e il meccanismo, mai una promessa in euro.
- **`dtc`:** gate SALUTE ed ENDORSEMENT. Nessuna claim di cura/trattamento/prevenzione malattia. Testimonianze reali che riflettono risultati tipici o con disclosure. Claim su ingredienti a livello struttura-funzione salvo sostanziazione.
- **`service`:** regole pubblicità locale + stesso standard sulle testimonianze.

## N.7 — Come i consumatori usano questo modulo

1. Risolvi la nicchia (N.2).
2. Chiedi o inferisci il tipo di offerta per QUESTO run (una domanda, opzioni dalla tabella della nicchia).
3. Prendi focus di messaging e CTA default dalla riga corrispondente, passa la CTA a `awareness_tension_funnel.md` per l'aggiustamento su funnel stage, passa la nicchia a `creative_claims_compliance.md` per il gate di compliance corretto.
