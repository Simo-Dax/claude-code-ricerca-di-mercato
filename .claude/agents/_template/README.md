# Scrivere un tuo agente

Questo kit è il ramo ricerca: **SA1 competitor** e **SA2 mercato**. Finisce dove finisce la
ricerca, con gli insight validati da te.

| Agente | Ruolo |
|---|---|
| **SA1** competitor analysis | spy Meta + Google, messaging matrix, spazi liberi |
| **SA2** market research | voce del cliente, JTBD, forze del cambiamento, profili |

Vuoi un agente per i passi successivi — strategia, creatività, copy? Copia
`agent.template.md`, mettilo in `.claude/agents/` con un nome tuo, compila frontmatter e SOP.
Claude Code lo trova da solo e lo invoca via Task tool.

Due regole che valgono per qualunque agente aggiungi:
- **Un agente = un mestiere.** Se la descrizione ha una "e" al centro, sono due agenti.
- **Handoff via file, non via contesto.** Ogni agente scrive un `.md` in `output/intermediate/`
  e il successivo lo legge da lì.
