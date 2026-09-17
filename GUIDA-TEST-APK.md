# Guida Testing — NutriAI APK (server pubblico)

> Versione: 2026-09-12 · APK: `nutriai-app.apk` (~54 MB) · Server: `https://nutriai-test-server.tailaf87fc.ts.net`
> L'app è già compilata per il server pubblico: **nessuna VPN, nessuna configurazione** richiesta ai tester.

---

## Changelog APK
### v12 (2026-09-17) - Dashboard: macro come chip colorati
- Nelle card dei pasti le sigle P:/C:/F: sono sostituite da puntini colorati con i grammi: Rosso = proteine, Arancio = carboidrati, Blu = grassi (stessi colori dei cerchi in alto)
- Etichette esplicite per screen reader («Proteine: 18g»): accessibilità migliorata
### v11 (2026-09-17) - Tab Peso: virgola/punto + tooltip grafico
- Input peso con virgola (72,5) o punto (72.5): entrambi salvano correttamente; hint « es. 72,5 »
- Errori di input chiari (range 30–300 kg) invece del messaggio generico
- Il grafico al tocco dei punti mostra peso e data (prima: quadrato vuoto)
- Conferma visibile al salvataggio + refresh senza lampi a schermo pieno
### v10 (2026-09-16) - Titolo ricetta a sinistra
- Il titolo della ricetta nel dettaglio parte dal bordo sinistro (mai centrato)
- Se il titolo sembrava ancora centrato, era una versione precedente: questa build lo garantisce con test di posizione
- Reinstallazione richiesta

### v9 (2026-09-16) - Navigazione: tasto indietro
- FIX: il tasto indietro da qualunque scheda NON chiude piu l app
- Dal dettaglio di un pasto: indietro = ritorno animato alla schermata precedente
- Da una scheda (Ricette, Chat, Peso, Profilo): indietro = ritorno alla Home
- Dalla Home: doppio tocco indietro per uscire (avviso al primo tocco)
- Reinstallazione richiesta: disinstalla la vecchia app, installa il nuovo APK


- **2026-09-12 v5** — ROOT CAUSE del bug “nessun pulsante” (screenshot): un’impostazione del tema rendeva il pulsante **fisicamente invisibile** (larghezza infinita) nella riga in basso, su ogni telefono. Corretto alla radice: **Avanti ora è garantito visibile e premibile** su tutti gli step. Corretto anche il pulsante Salva del peso. **Azione richiesta: disinstallare e reinstallare l’APK.**
- **2026-09-12 v4** — terzo report “nessun pulsante per andare avanti” (dati anagrafici): il pulsante **Avanti è ora sempre attivo**. Se manca qualcosa, alla pressione compaiono i messaggi rossi **sotto ogni campo** che lo richiede; corretti i campi, i messaggi spariscono da soli. **Azione richiesta: reinstallare l’APK.**
- **2026-09-13 v8** — sezione Ricette: (1) il titolo nel dettaglio non è più centrato e non copre più la category pill, va a capo su 2 righe; (2) la pill mostra “PIATTO UNICO” senza più il trattino basso; (3) scrollando le ricerche non appare più un errore dopo poche pagine: ora lo scorrimento continua fino in fondo all’elenco e un eventuale errore non cancella i risultati già visti. **Azione richiesta: reinstallare l’APK.**

**2026-09-13 v7** — fix “ cuore preferiti non funziona”: il problema era DOPPIO lato server (rotta ombreggiata + tabella mai creata nel DB), quindi il tocco sul cuore non produceva nulla. Ora: il cuore funziona **sia sulle card sia nel dettaglio ricetta** (in alto a destra, accanto a copia e condividi), con conferma immediata e messaggio d’errore visibile se qualcosa va storto. Bonus: funzionano anche “Ricetta casuale”, filtri per regione/categoria e la scheda Preferiti. **Azione richiesta: reinstallare l’APK.**

**2026-09-13 v6** — fix report titoli in inglese: aprendo un pasto da “Piano di oggi” il titolo in alto a sinistra mostrava l’etichetta interna del server (“breakfast”, “morning snack”) invece dell’italiano. Ora: **Colazione, Spuntino mattina, Pranzo, Spuntino pomeriggio, Cena** — in dettaglio pasto, dashboard e ricette. **Azione richiesta: reinstallare l’APK.**

**2026-09-12 v3** — fix report “Operazione non riuscita. Riprova.” in registrazione: se l’email ha GIÀ un account ora l’app lo dice chiaramente e mostra il bottone **Vai al login** (il server non era guasto: il tester aveva già registrato quell’email). Nuovi messaggi anche per troppi tentativi (attendi 1 minuto) ed errori server. **Azione richiesta: reinstallare l’APK.**
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
