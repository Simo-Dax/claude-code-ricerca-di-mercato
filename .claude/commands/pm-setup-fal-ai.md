---
description: Configura la Fal AI API key usata dalle skill di generazione immagini/video (statiche, ugc-video, product-photo, multiplier, buyer-persona) e dalla trascrizione di 52_ad_spy_video. Salva il token in modo sicuro.
---

# /pm-setup-fal-ai — Configura Fal AI API Key

Configura la Fal AI API key usata dalle skill di generazione (Path C): `24_static_ads`, `25_ugc_prompt`, `26_product_shot`, `27_multiplier`, `22_character_creator`, `23_competitor_rebuild`. Usata anche da `52_ad_spy_video` per la trascrizione (`fal-ai/whisper`).

## Hard rules
1. Esegui ogni comando shell tu stesso via Bash. Non far aprire terminali all'utente.
2. Non echeggiare mai la key in chiaro dopo il salvataggio.
3. Idempotente: se la key esiste già e funziona, dillo in una riga e fermati.

## Step
1. Chiedi la Fal AI key. Spiega: https://fal.ai/dashboard/keys.
2. Salva in `~/.config/pm-agent/fal.env` (`mkdir -p` la dir):
   ```
   FAL_KEY=<key>
   ```
   `chmod 600` sul file.
3. Verifica con una chiamata leggera (es. lista modelli o un check di auth fal.ai).
4. Conferma: "Fal AI key configurata e verificata."

## Nota modelli
- GPT Image 2 (`openai/gpt-image-2`, `openai/gpt-image-2/edit`): NON accetta `safety_tolerance`.
- Nano Banana 2 (`fal-ai/nano-banana-2`, `.../edit`): accetta `safety_tolerance: "4"`.

## Errore tipo
Verifica fallita → "Key non valida. Controlla su fal.ai/dashboard/keys e rilancia /pm-setup-fal-ai."
