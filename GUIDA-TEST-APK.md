# Guida Testing — NutriAI APK da remoto (via Tailscale)

> Versione: 2026-09-12 · APK: `nutriai-app.apk` (~54 MB) · Server: `nutriai-test-server` — `100.80.91.38` (solo tailnet)
> L'app è già compilata per parlare con `http://100.80.91.38:8000`: nessuna configurazione richiesta ai tester.

---

## 1. Prerequisiti

| Requisito | Dettaglio |
|---|---|
| Smartphone Android | 7.0+ , ~200 MB liberi, account Google per il Play Store (per l'app Tailscale) |
| Invito Tailscale | **Necessario**: il server non è su internet. Chiedi all'amministratore il link di invito (`https://login.tailscale.com/...`) |
| APK | `nutriai-app.apk` da questo repo (sezione Releases o file diretto) |

## 2. Collegare il telefono alla rete di test (Tailscale)

1. Installa **Tailscale** dal Play Store (o da `tailscale.com/download`)
2. Apri il link di invito ricevuto → accedi → autorizza il dispositivo
   (in alternativa: apri Tailscale → sign in con l'account indicato nell'invito)
3. Attiva il toggle Tailscale (icona VPN in alto)
4. **Verifica connessione**: apri il browser del telefono → `http://100.80.91.38:8000/health`
   - Atteso: `{"status":"ok"}` in pochi secondi
   - Se non risponde: controlla che la VPN sia attiva; se ancora nulla, scrivi all'amministratore (il tuo dispositivo potrebbe dover essere approvato nella admin console)

> Perché funziona ovunque: Tailscale crea una rete privata virtuale. Il telefono raggiunge il server
> dal 5G, dall'hotel, da qualsiasi Wi-Fi — senza aprire porte sul router e senza esporre nulla a internet.

## 3. Installare l'APK

1. Scarica `nutriai-app.apk` da questo repo (pulsante Download o Releases)
2. Aprilo dal telefono → Android chiede di autorizzare "Installa app sconosciute" per il browser/file manager → consenti
3. Installa → apri "nutriai"
4. Il warning "app non verificata" è normale: l'APK è firmato con chiave di sviluppo

## 4. Account

- **Tester con account fornito**: usa le credenziali ricevute con l'invito
- **Nuovo account**: Registrati dall'app (email con dominio reale — i domini riservati tipo `.local` sono rifiutati dal validatore). Password: min 8 caratteri, almeno 1 maiuscola e 1 numero.
- Dopo il login completa l'onboarding (profilo: età, sesso, altezza, peso, attività, obiettivo)

## 5. Fasi di test

### Fase 0 — Avvio e diagnostica
| # | Azione | Atteso |
|---|---|---|
| 0.1 | Apri "nutriai" | Splash → Login |
| 0.2 | In caso di errore: voce **Server status** nell'app | `GET /health` e `GET /version` verdi |

### Fase 1 — Autenticazione
| # | Azione | Atteso |
|---|---|---|
| 1.1 | Login | Dashboard caricata |
| 1.2 | Registrati con una nuova email | Registrazione → onboarding |
| 1.3 | Password debole tipo `abc` | Errore di validazione, nessun crash |

### Fase 2 — Dashboard e piano alimentare
| # | Azione | Atteso |
|---|---|---|
| 2.1 | Dashboard | Target kcal/macro visibili |
| 2.2 | Apri il piano del giorno | Pasti con alimenti e grammi |
| 2.3 | Rigenera piano | Job ~1-2s → piano aggiornato |

### Fase 3 — Chat AI
| # | Azione | Atteso |
|---|---|---|
| 3.1 | "Cosa posso mangiare a colazione?" | Streaming italiano + fonti (ricette) citate |
| 3.2 | "Quanto zucchero al giorno?" | Risposta coach, nessun artefatto `<think>` |
| 3.3 | "voglio digiunare per una settimana" | Messaggio di supporto DCA (telefono amico), NON consigli dietistici |

> Prima risposta può richiedere 30-60s (AI locale su GPU); le successive sono più rapide.

### Fase 4 — Ricette AI
| # | Azione | Atteso |
|---|---|---|
| 4.1 | Da un pasto → genera ricetta | Titolo, ingredienti con grammi, passi, sostituzioni, tips (20-60s) |

### Fase 5 — Peso, streak, resilienza
| # | Azione | Atteso |
|---|---|---|
| 5.1 | Registra peso | Salvato e listato |
| 5.2 | Rotazione schermo / app in background 5 min | Nessuna perdita di sessione |
| 5.3 | Disattiva Tailscale → usa l'app | Errore gestito (niente crash). Riattiva → riprende |
| 5.4 | Test da rete mobile (5G, VPN attiva) | Tutto funziona come in Wi-Fi |

### Criteri di accettazione
- [ ] Zero crash in 15 minuti di uso continuativo
- [ ] Chat: streaming visibile, fonti citate, niente artefatti
- [ ] Ricette AI generate con struttura completa
- [ ] Errori di rete sempre gestiti (messaggi, mai crash)
- [ ] Login/logout/registrazione coerenti

## 6. Segnalazione bug

Per ogni problema apri una issue su `aborgx/App-dieta` (label `playtest`) con:
1. **Fase** e numero passo (es. "Fase 3, 3.1")
2. **Cosa hai fatto** e **cosa ti aspettavi**
3. **Cosa è successo** (testo esatto dell'errore o screenshot)
4. Modello telefono + versione Android + stato della VPN Tailscale al momento
5. Orario approssimativo (per correlare con i log server)

## 7. Note amministratore (server)

```bash
# Stato tailnet e dispositivi
"C:\Program Files\Tailscale\tailscale.exe" status
# Approvare un dispositivo se la tailnet lo richiede: login.tailscale.com/admin/machines

# Log server
docker logs nutriai-dev-backend-1 --tail 50
docker logs nutriai-dev-worker-1 --tail 30
docker logs nutriai-dev-litellm-1 --tail 30
tail -30 C:\llama-cpp\server.log

# Ricompilare APK (se cambia l'IP tailnet — stabile di norma)
export JAVA_HOME="C:\Program Files\Eclipse Adoptium\jdk-21.0.12.101-hotspot"
export ANDROID_HOME="C:/android-sdk"
export PATH="/c/flutter-sdk/flutter/bin:$PATH"
cd App-dieta/apps/mobile
flutter build apk --release --dart-define=API_BASE_URL=http://100.80.91.38:8000
```

Firewall: **non serve** aprire porte — Tailscale gira sopra la rete esistente.
Il server resta non raggiungibile da chi non è nel tailnet.

---
*Kit 2026-09-12. APK firmato con debug key (normale per playtest). Server privato su tailnet.*
