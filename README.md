# NutriAI — Testkit Playtest

Kit di test per i playtester di **NutriAI**: APK installabile + guida completa.
Il server di test è **privato** (raggiungibile solo tramite Tailscale): nessun dato del progetto è pubblico.

## Contenuto

| File | Descrizione |
|---|---|
| `nutriai-app.apk` | App Android (release, ~54 MB) — punta al server di test tramite Tailscale |
| `GUIDA-TEST-APK.md` | Guida dettagliata: Tailscale, installazione, fasi di test, diagnostica |
| `README.md` | Questo file |

## Server di test

| Parametro | Valore |
|---|---|
| Nome host tailnet | `nutriai-test-server` |
| Indirizzo Tailscale | `100.80.91.38` |
| Backend API | `http://100.80.91.38:8000` |
| AI locale | arcangelo-retrain (Qwen3-8B) via llama.cpp + LiteLLM |

> L'indirizzo `100.80.91.38` esiste **solo dentro la rete Tailscale** (tailnet `vazzana.michele92@`).
> Non è raggiungibile da internet: serve l'invito Tailscale (vedi guida, §2).

## Accesso rapido tester

1. Installa Tailscale sul telefono e accedi con l'invito ricevuto (guida §2)
2. Verifica: apri `http://100.80.91.38:8000/health` nel browser del telefono → deve rispondere `{"status":"ok"}`
3. Installa `nutriai-app.apk` (guida §3)
4. Segui le fasi di test nella guida (§5)

## Account di test

Usa quello fornito con l'invito, oppure registramenti nuovi dal form dell'app (email con dominio reale, es. `nome@gmail.com`; i domini riservati tipo `.local` vengono rifiutati).

---
*Kit generato il 2026-09-12. L'APK è firmato con chiave debug: il warning Android "app sconosciuta" è normale.*
