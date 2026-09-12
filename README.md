# NutriAI — Testkit Playtest

Kit di test per i playtester di **NutriAI**: APK installabile + guida completa.
Il server di test è **pubblico via HTTPS** (Tailscale Funnel): i tester **non installano VPN** e non servono inviti.

## Contenuto

| File | Descrizione |
|---|---|
| `nutriai-app.apk` | App Android (release, ~54 MB) — già configurata per il server pubblico |
| `GUIDA-TEST-APK.md` | Guida dettagliata: installazione, fasi di test, diagnostica, segnalazione bug |
| `README.md` | Questo file |

## Server di test (pubblico)

| Parametro | Valore |
|---|---|
| URL pubblico (HTTPS) | `https://nutriai-test-server.tailaf87fc.ts.net` |
| Health check | `https://nutriai-test-server.tailaf87fc.ts.net/health` → `{"status":"ok"}` |
| Backend API | stesso URL, percorsi `/api/v1/...` |
| AI locale | arcangelo-retrain (Qwen3-8B fine-tuned) via llama.cpp CUDA + LiteLLM |

> L'esposizione pubblica è fornita da **Tailscale Funnel** (TLS automatico, reverse proxy gestito).
> L'infrastruttura resta privata dietro il tailnet: è pubblicato **solo** il percorso del backend sulla porta 443.

## Accesso rapido tester

1. Scarica `nutriai-app.apk` da questo repo e installalo (guida §3)
2. Apri l'app: punta già al server pubblico — nessuna configurazione
3. Accedi con l'account ricevuto, o registrati (email con dominio reale, password con maiuscola + numero)
4. Segui le fasi di test nella guida (§5)

## Note sicurezza (per l'amministratore)

- L'endpoint di registrazione è aperto a chiunque conosca l'URL: per la durata del playtest è accettabile; a fine round disattivare con `tailscale funnel --set 443 off` (o `tailscale funnel off`)
- Account di test in circolazione: `test@example.com` — cambiarla password se compromessa
- I dati del playtest sono in un DB Docker locale dedicato (`nutriai-dev`)

---
*Kit aggiornato il 2026-09-12 (URL pubblico). APK firmato con chiave debug: il warning Android "app sconosciuta" è normale.*
