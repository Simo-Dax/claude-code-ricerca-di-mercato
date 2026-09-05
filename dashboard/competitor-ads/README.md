# Competitor Ads Dashboard

Dashboard single-file per **sfogliare e filtrare gli annunci dei concorrenti** raccolti da
`19_ad_spy` + `52_ad_spy_video` (`/pm-competitor-spy`, Meta Ad Library) e da `62_google_ads_spy`
(`/pm-google-spy`, Google Ads Transparency Center). **Un solo posto per i due canali.**

**Posizione:** `dashboard/competitor-ads/` — è un **output** del sistema, non una reference.

---

## Cosa fa

**Colonna sinistra — filtri, tutti combinabili:**

| Gruppo | Valori |
|---|---|
| Canale | Meta Ad Library · Google Ads Transparency |
| Inserzionista | un'opzione per concorrente raccolto |
| Formato | Immagine · Video · Carosello · Dinamico (DCO) · Solo testo |
| Anteprima | Con anteprima · Senza anteprima |
| Stadio di consapevolezza | Inconsapevole → Pronto a comprare (5 stadi di Eugene Schwartz) |
| Funnel | Cima · Metà · Fondo |
| Longevità | Provata (60+ gg) · Calda (21+ gg) · Attiva · Ritirata · Corsa breve |

I **conteggi sono contestuali**: ogni numero dice quanti annunci resterebbero attivando *quella*
opzione, tenendo conto di tutti gli altri filtri già accesi. Le opzioni a zero restano visibili
ma spente, così si vede subito dove il mercato non ha niente. I filtri attivi diventano **chip**
sopra la griglia: si tolgono uno per uno con un clic.

**Destra — griglia di card.** Ogni card mostra l'anteprima reale dell'annuncio, l'inserzionista,
i tag di canale/consapevolezza/funnel, il titolo, e quattro numeri: giorni in aria, copertura UE
(solo Meta) o numero di regioni (Google), varianti, data di prima messa in aria.

**Apertura della card.** Si clicca **ovunque sulla card** (anche da tastiera: Invio o barra
spaziatrice) e si apre la scheda annuncio: creative a dimensione piena nel suo rapporto d'aspetto
originale, corpo del testo, tutti i metadati, il link alla libreria ufficiale e — per i video —
il link al file originale. Con **← e →** si scorre tutta la selezione filtrata senza chiudere.
`Esc` o un clic sullo sfondo chiudono.

**In alto:** ricerca libera su titolo, corpo, CTA, formato e nome dell'inserzionista; ordinamento
per longevità, data, copertura UE o inserzionista. La griglia carica 120 annunci per volta
("Mostra altri…"): con oltre mille creative incorporate è ciò che tiene la pagina reattiva.

---

## Come usarla

1. `19_ad_spy` / `52_ad_spy_video` generano le righe Meta, `62_google_ads_spy` quelle Google
   (`source:"google"`) — schema completo in `data.sample.json`.
2. Unisci gli array `ads` in un unico `data.json` e copialo in questa cartella.
3. Apri `index.html` nel browser (funziona anche offline; senza `data.json` mostra il campione).
4. Per condividerla: pubblicala come artifact, oppure trascina la cartella su Netlify Drop /
   Cloudflare Pages / Vercel. Nessun build step.

---

## ⚠️ Regole non negoziabili sui dati

- **Anteprime incorporate.** `image` deve essere un **`data:` URI**, non un link alla CDN:
  gli URL di Meta e Google scadono in poche ore e la CSP degli artifact blocca le immagini
  esterne. Un annuncio senza anteprima incorporata è un annuncio che nessuno vedrà.
- **Sempre il link alla libreria ufficiale.** Il campo `url` è obbligatorio: è la prova
  verificabile che l'annuncio esiste ed è quello che diciamo.
- **Video:** `image` = primo fotogramma, `media_kind:"video"`, `media_full` = file originale.
- **Testi Google:** le search ad arrivano come immagine renderizzata (`simgad`). Se il testo non
  è stato trascritto con la vista, il titolo resta il segnaposto e la dashboard lo dichiara
  ("il testo dell'annuncio sta dentro l'immagine"): mai inventare la trascrizione.
- **`awareness_stage` e `funnel_stage` sono classificati, non estratti.** Non esistono nelle
  librerie: li assegna il modello leggendo creative e copy. È ciò che rende la dashboard uno
  strumento strategico e non un archivio.
- **`eu_reach` esiste solo su Meta** (obbligo UE). Su Google si mostrano le regioni.

---

## Look & feel

Palette Rausch `#FF385C` su bianco, ink `#222222`, muted `#717171`, bordi `#DDDDDD`;
font Nunito Sans; card in stile listing con anteprima arrotondata e sollevamento all'hover;
badge longevità come pill bianca, tag funnel colorati per stadio.
Le anteprime larghe (le text ad Google) vengono mostrate **intere**, non ritagliate.

---

## Evoluzioni possibili

- Dati su Google Sheet/Airtable con fetch live invece del `data.json` statico
- Aggiornamento schedulato via n8n (re-run settimanale dello spy → nuovo `data.json`)
- Heatmap degli spazi liberi: matrice consapevolezza × concorrente con densità di annunci
