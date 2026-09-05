# Market Research Kit — Orchestrator

Sistema multi-agent per la **fase di ricerca** del performance marketing: dalla voce dei clienti e dalle ads dei competitor ai **building block della comunicazione**, pronti per diventare strategia, copy e creative.

Agnostico rispetto al brand: il contesto specifico vive in `context/brand/` e `context/campaign/`.

> Questo kit è il ramo *research* di un sistema più ampio (9 sub-agent). Qui trovi i due agenti che fanno la ricerca (SA1 competitor, SA2 mercato) e le skill che orchestrano. Gli agenti a valle — strategia, copy, produzione asset — non sono inclusi.

---

## Regole base (valgono per ogni task)

1. **Non assumere. Non nascondere la confusione. Esplicita i tradeoff.**
2. **Ogni claim ha una fonte.** Se un'affermazione non è supportata dai dati, va marcata `ipotesi da validare`.
3. **Verbatim o niente.** Le citazioni dei clienti si riportano esatte: slang, maiuscole, errori inclusi. Zero parafrasi.
4. **Definisci i criteri di successo. Verifica prima di dichiarare fatto.**

---

## Architettura

```
ORCHESTRATOR (questo file)
├── sa1_competitor_analysis   ← ad spy Meta (static + video), ad spy Google Transparency,
│                               review mining, UGC scraper → white space map
└── sa2_market_research       ← VOC research, Jobs-to-be-Done + Forces of Progress,
                                persona stack psicografico
```

**Flusso:** SA1 ∥ SA2 (in parallelo, sono indipendenti) → `47` review gap + `48` segment/pain → `33` insight synthesis → **🚦 GATE UMANO** → building block validati.

**Due regole non negoziabili:**
- **Competitor: mai un solo canale.** Ogni analisi copre **Meta Ad Library** (`19` static + `52` video) **e** **Google Ads Transparency Center** (`62`). Meta mostra cosa spingono in interruzione, Google cosa presidiano in intento. La messaging matrix si tagga per canale (`M`/`G`/`MG`) e si chiude con la sezione META vs GOOGLE.
- **Persona: il VOC non basta.** Dopo `18_voc_research` gira sempre `63_persona_stack` — 3-5 persona psicografiche, confidence check per sezione, e la **% di allocazione del budget creativo**. Senza quella percentuale, la produzione creativa a valle non ha un vincolo e si fa a sentimento.

**Filosofia:** l'AI accelera la raccolta dati e l'esecuzione. Il **giudizio su insight e strategia resta umano**. Il gate non è un limite del sistema: è il punto in cui il sistema smette di produrre plausibilità e comincia a produrre verità.

---

## Come si invocano gli agenti

I due sub-agent vivono in `.claude/agents/` come subagent nativi. Si invocano via Task tool con `subagent_type` = `sa1-competitor-analysis` / `sa2-market-research`. Girano in **contesto isolato** e si passano il lavoro **via file** (`intermediate/*.md`), non via chat condivisa.

**Agente vs skill vs command:** un *subagent* è un ruolo che orchestra skill. Una *skill* (`directives/skills/NN_*`) è una procedura. Un *command* `/pm-*` lancia una skill.

---

## Runbook

0. **🚦 Context gate.** Compila `context/brand/business_profile.md` e `context/campaign/brief.md`. Campi critici mancanti → fermati e chiedili. Garbage in, garbage out.
1. **Ricerca in parallelo** — SA1 e SA2 nello stesso messaggio:
   - SA2: `/pm-dati-qualitativi` (VOC → JTBD → Forces of Progress) → `/pm-personas`
   - SA1: `/pm-competitor-spy` + `/pm-google-spy` (+ `/pm-competitor-spy-video`, `/pm-ugc-analysis`)
2. `/pm-review-gap` — il delta fra recensioni positive e negative dei competitor.
3. `/pm-segments` — pain matrix (frequency × frustration) e segmenti per contesto + trigger.
4. `/pm-insight` — sintesi nelle 7 dimensioni → **🚦 GATE**: mostra le decisioni incerte e aspetta l'OK umano.
5. Consolida i building block e passali a valle (strategia, copy, creative).

---

## Comandi

| Comando | Cosa fa | Skill / Agente |
|---|---|---|
| `/pm-dati-qualitativi` | VOC research → HTML con JTBD strutturato (+ Foundation Pack opzionale) | 18 / SA2 |
| `/pm-personas` | Persona stack psicografico: 3-5 persona, deep dive 10 punti, % allocazione creativa | 63 / SA2 |
| `/pm-competitor-spy` | Ad spy Meta static: swipe file ranked per longevità | 19 / SA1 |
| `/pm-competitor-spy-video` | Teardown video Meta: script, hook, beat sheet | 52 / SA1 |
| `/pm-google-spy` | Ad spy Google Transparency: Search/YouTube/Shopping + confronto META vs GOOGLE | 62 / SA1 |
| `/pm-ugc-analysis` | 25 transcript TikTok virali con vetting | 20 / SA1 |
| `/pm-review-gap` | Gap di mercato dal delta recensioni positive/negative | 47 / SA1 |
| `/pm-segments` | Pain matrix + segmenti per contesto e trigger + prioritizzazione | 48 |
| `/pm-insight` | Sintesi nelle 7 dimensioni + 🚦 gate umano | 33 |
| `/pm-data-analysis` | Analisi dati first-party del cliente: track quantitativo + qualitativo | 38 / SA2 |
| `/pm-setup-apify` · `/pm-setup-fal-ai` | Configurazione chiavi API | — |

### Riferimenti a skill non incluse

Gli agenti e le skill di questo kit citano, negli handoff, skill a valle che **non fanno parte del kit** (`32_brand_strategy`, `53_ad_angles`, `54_headline_bank`, `28_meta_copy`, `24_static_ads`, `50_meta_analyze`…). Sono indicazioni su dove finisce il lavoro nel sistema completo, non file da aprire. Se un comando prova a leggerle, ignora e prosegui: la ricerca è completa senza di esse.

---

## Struttura output

Le skill scrivono nella cartella dove Claude Code è aperto (`pwd`).

```
01_VOC_Research/     ← VOC (18) + persona stack (63)
03_Ad_Spy/           ← swipe file Meta (19, 52) + google/ (62) + _scratch/
intermediate/        ← output testuali degli agenti: sa1_*, sa2_*, persona_stack,
                       competitor_review_gap, segment_pain_matrix, insight
dashboard/competitor-ads/  ← dashboard Meta + Google (apri index.html)
```

---

## Prerequisiti

- **Claude Code** installato.
- **Apify API key** (`/pm-setup-apify`) — serve per ad spy Meta e Google, review mining, UGC scraper. Costo reale: pochi centesimi per run.
- **fal.ai API key** (`/pm-setup-fal-ai`) — solo per i teardown video (trascrizione).
- `ffmpeg` — solo per i teardown video.

Senza chiavi API girano comunque VOC research, persona stack, segment/pain e insight synthesis (usano solo la ricerca web).
