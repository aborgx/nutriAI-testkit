# Guida Testing — NutriAI APK (server pubblico)

> Versione: 2026-09-12 · APK: `nutriai-app.apk` (~54 MB) · Server: `https://nutriai-test-server.tailaf87fc.ts.net`
> L'app è già compilata per il server pubblico: **nessuna VPN, nessuna configurazione** richiesta ai tester.

---

## Changelog APK

- **2026-09-12 v2** — fix report tester “non compare nulla per andare avanti”: il pulsante **Genera il mio piano** (riepilogo) ora è **sempre visibile in basso**, “Avanti” mostra una spiegazione quando disabilitato, la riga di consenso resta visibile su schermi piccoli. **Azione richiesta: disinstallare e reinstallare l’APK aggiornato.** Nella segnalazione indicare comunque modello telefono + versione Android (§5.4).
- **2026-09-12 v1** — prima build pubblica.

---

## 1. Prerequisiti

| Requisito | Dettaglio |
|---|---|
| Smartphone Android | 7.0+, ~200 MB liberi |
| Connessione internet | Qualsiasi: Wi-Fi o 4G/5G — il server è raggiungibile da internet via HTTPS |
| APK | `nutriai-app.apk` da questo repo |

## 2. Installare l'APK

1. Scarica `nutriai-app.apk` da questo repo (pulsante Download o Releases)
2. Aprilo dal telefono → Android chiede di autorizzare "Installa app sconosciute" per l'app usata (browser/file manager) → consenti
3. Installa → apri "nutriai"
4. Il warning "app non verificata" è normale: APK firmato con chiave di sviluppo

**Verifica rapida connettività (opzionale)**: apri `https://nutriai-test-server.tailaf87fc.ts.net/health` nel browser del telefono → atteso `{"status":"ok"}`.

## 3. Account

- **Tester con account fornito**: usa le credenziali ricevute con l'invito
- **Nuovo account**: Registrati dall'app (email con dominio reale — i domini riservati tipo `.local` sono rifiutati). Password: min 8 caratteri, almeno 1 maiuscola e 1 numero.
- Dopo il login completa l'onboarding (età, sesso, altezza, peso, attività, obiettivo)

## 4. Fasi di test

### Fase 0 — Avvio e diagnostica
| # | Azione | Atteso |
|---|---|---|
| 0.1 | Apri "nutriai" | Splash → Login |
| 0.2 | In caso di errore: voce **Server status** nell'app | `GET /health` e `GET /version` verdi |

### Fase 1 — Autenticazione
| # | Azione | Atteso |
|---|---|---|
| 1.1 | Login | Dashboard caricata |
| 1.2 | Registrazione nuova email | Registrazione → onboarding |
| 1.3 | Password debole tipo `abc` | Errore di validazione, nessun crash |

> **Nuovo (2026-09-12)**: allo step del disclaimer la CTA `”Accetto e proseguo”` è fissata in basso e sempre visibile; i termini completi si aprono con `“Leggi termini e informativa completa”`. Se hai una versione APK precedente, il bottone `Avanti` in basso a destra restava spento dopo la spunta: aggiorna l'APK.

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

> La prima risposta AI può richiedere 30-60s (modello locale sul server); le successive sono più rapide.

### Fase 4 — Ricette AI
| # | Azione | Atteso |
|---|---|---|
| 4.1 | Da un pasto → genera ricetta | Titolo, ingredienti con grammi, passi, sostituzioni, tips (20-60s) |

### Fase 5 — Peso, resilienza, rete
| # | Azione | Atteso |
|---|---|---|
| 5.1 | Registra peso | Salvato e listato |
| 5.2 | Rotazione schermo / app in background 5 min | Nessuna perdita di sessione |
| 5.3 | Modalità aereo 30s → usa l'app → riattiva rete | Errore gestito (niente crash), ripresa al ritorno |
| 5.4 | Passaggio Wi-Fi ↔ 5G durante la chat | Stream interrotto con errore gestito o riprende |

### Criteri di accettazione
- [ ] Zero crash in 15 minuti di uso continuativo
- [ ] Chat: streaming visibile, fonti citate, niente artefatti
- [ ] Ricette AI generate con struttura completa
- [ ] Errori di rete sempre gestiti (messaggi, mai crash)
- [ ] Login/logout/registrazione coerenti

## 5. Segnalazione bug

Apri una issue su `aborgx/App-dieta` (label `playtest`) con:
1. **Fase** e numero passo (es. "Fase 3, 3.1")
2. **Cosa hai fatto** e **cosa ti aspettavi**
3. **Cosa è successo** (testo esatto dell'errore o screenshot)
4. Modello telefono + versione Android
5. Orario approssimativo (per correlare con i log server)

## 6. Note amministratore

```bash
# URL pubblico (Funnel attivo su porta 443 → backend :8000)
curl https://nutriai-test-server.tailaf87fc.ts.net/health

# Stato funnel
"C:\Program Files\Tailscale\tailscale.exe" funnel status

# DISATTIVARE l'accesso pubblico a fine playtest
"C:\Program Files\Tailscale\tailscale.exe" funnel --set 443 off

# Log server
docker logs nutriai-dev-backend-1 --tail 50
docker logs nutriai-dev-worker-1 --tail 30
tail -30 C:\llama-cpp\server.log

# Ricompilare APK (se cambia l'hostname)
cd App-dieta/apps/mobile
flutter build apk --release --dart-define=API_BASE_URL=https://nutriai-test-server.tailaf87fc.ts.net
```

L'hostname `*.ts.net` è stabile; non cambia a meno che il nodo non venga rimosso dal tailnet.

---
*Kit 2026-09-12, server pubblico via Tailscale Funnel (TLS automatico). APK firmato con debug key (normale per playtest).*
