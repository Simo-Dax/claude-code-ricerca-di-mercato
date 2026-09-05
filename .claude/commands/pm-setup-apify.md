---
description: Configura la Apify API key usata da /pm-competitor-spy, /pm-competitor-spy-video e /pm-ugc-analysis. Salva il token in modo sicuro.
---

# /pm-setup-apify — Configura Apify API Key

Configura la Apify Personal API token (formato `apify_api_xxx`) usata dalle skill `19_ad_spy` (`/pm-competitor-spy`), `52_ad_spy_video` (`/pm-competitor-spy-video`) e `20_ugc_scraper` (`/pm-ugc-analysis`).

## Hard rules
1. Esegui ogni comando shell tu stesso via Bash. Non far aprire terminali all'utente.
2. Non echeggiare mai il token in chiaro nei log dopo averlo salvato.
3. Idempotente: se la key esiste già e funziona, dillo in una riga e fermati.

## Step
1. Chiedi all'utente la Apify token. Spiega: gratis su https://console.apify.com/account/integrations — free tier $5/mese (~90 run di `/pm-ugc-analysis`).
2. Salva la token come variabile d'ambiente persistente per Apify. Scrivi in `~/.config/pm-agent/apify.env` (crea la dir con `mkdir -p`):
   ```
   APIFY_TOKEN=<token>
   ```
   `chmod 600` sul file.
3. Aggiorna anche `.mcp.json` → server `apify` → `env.APIFY_TOKEN` con lo stesso valore (così il MCP Apify proprio usa la key). Vedi `.mcp.json.example`.
4. Verifica: chiamata di test all'API Apify con header `Authorization: Bearer $APIFY_TOKEN` (es. GET `https://api.apify.com/v2/users/me`). Token sempre come header, MAI in URL.
5. Conferma in una riga: "Apify key configurata e verificata."

## Errore tipo
Se la verifica fallisce → "Token non valido o scaduto. Rigenerala su console.apify.com/account/integrations e rilancia /pm-setup-apify."
