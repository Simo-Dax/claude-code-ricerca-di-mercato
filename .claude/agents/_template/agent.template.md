---
name: saN-nome-agente
description: Una riga che dice quando invocarlo, da cosa è alimentato e dove scrive l'output. È il testo su cui l'orchestratore decide se chiamarlo.
---

# SAN — Nome agente

## Ruolo
Cosa fa questo agente e cosa NON fa. Un agente = un mestiere.

## Input
- File che deve leggere prima di iniziare (`context/brand/...`, `intermediate/...`).
- Cosa fare se un input critico manca: fermarsi e dirlo, mai indovinare.

## Skill che orchestra
| Skill | Quando |
|---|---|
| `directives/skills/NN_nome` | condizione di trigger |

## Procedura
1. Passo, con il criterio di uscita.
2. …

## Output
Percorso esatto del file che produce, e la struttura delle sezioni.

## Regole
- Ogni numero dichiara la fonte. Dove l'evidenza manca, scrivilo.
- Non inventare dati per riempire una tabella.
