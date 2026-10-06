# 11. Controllo Remoto: Cloud Web Control (antigravity.google.com), SSH & Headless

Nello sviluppo software moderno, i flussi di lavoro degli agenti intelligenti — come il refactoring architetturale di interi moduli, l'esecuzione di suite di test end-to-end su larga scala o l'analisi di complessi dataset genomici — possono richiedere da diversi minuti fino a svariate ore di elaborazione continua. Essere costretti a rimanere fisicamente seduti davanti alla propria postazione di lavoro durante questi cicli riduce drasticamente l'agilità e il valore del paradigma di programmazione autonoma (*vibecoding*).

Antigravity risolve questa limitazione introducendo un'architettura completa di **Controllo Remoto**. Questa funzionalità ti consente di monitorare, dirigere e supervisionare il tuo agente locale da qualsiasi luogo: da uno smartphone in mobilità, da un tablet durante una riunione o da un portatile leggero mentre la tua potente workstation (o cluster di laboratorio) esegue il lavoro pesante.

In questo capitolo esploreremo in dettaglio il **Metodo Primario Ufficiale**: il portale web **`antigravity.google.com`**, basato su un'architettura a relay outbound crittografata senza apertura di porte sul router. Successivamente, analizzeremo i **Metodi Secondari**: sessioni persistenti SSH con multiplexer (`tmux`), esecuzione come demone headless di sistema (`systemd` e servizi Windows) e tunneling privato su reti mesh (Tailscale e Cloudflare Zero Trust).

---

## 11.1 Il Paradigma del Controllo Remoto: Architettura Ibrida Host-Cloud

Il controllo remoto di Antigravity non trasforma l'ambiente in un servizio SaaS centralizzato in cui il tuo codice sorgente viene caricato permanentemente sui server di un fornitore terzo. Al contrario, adotta un **paradigma ibrido federato** basato su due entità strettamente separate:

```
┌────────────────────────────────────────────────────────────────────────┐
│               SOVEREIGN LOCAL HOST (Macchina di Sviluppo)              │
│                                                                        │
│  - Codice sorgente su disco locale (nessun upload permanente)          │
│  - Runtime Antigravity & Language Server                               │
│  - Container Docker, database locali, compilatori, toolchain           │
│  - Sandbox di esecuzione e file di configurazione (.agents/)          │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │
                       Crittografia End-to-End (E2EE)
                       WebSocket Outbound su Porta 443
                                    │
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│              SOVEREIGN REMOTE SURFACE (Superficie di Controllo)        │
│                                                                        │
│  - Browser Web Desktop / Mobile su https://antigravity.google.com      │
│  - Streaming dei log e albero gerarchico dei task in tempo reale       │
│  - Ispezione visuale dei diff e invio di nuovi prompt                  │
│  - Notifiche push per approvazione interattiva Human-in-the-Loop       │
└────────────────────────────────────────────────────────────────────────┘
```

### Principi Architetturali Fondamentali:
1. **Sovereign Local Host (Motore Locale Sovrano):** Tutto il codice sorgente, i database, i container Docker, le credenziali e i log di sistema risiedono esclusivamente sulla macchina locale dell'utente. Il motore esecutivo non delega l'elaborazione dei file al cloud.
2. **Sovereign Remote Surface (Superficie di Controllo Remota):** Il dispositivo remoto (smartphone, tablet o laptop secondario) non esegue il codice. Funziona unicamente come interfaccia remota per visualizzare lo stato, inviare istruzioni e approvare le azioni critiche.
3. **Zero Data Retention sul Relay:** Il server di relay cloud agisce da commutatore effimero di pacchetti crittografati end-to-end (E2EE). Nessun frammento di codice o cronologia di prompt viene memorizzato o indicizzato sui server cloud.

---

## 11.2 METODO PRINCIPALE — Cloud Control via antigravity.google.com

Il metodo primario e raccomandato per interagire a distanza con Antigravity è il portale web ufficiale **`https://antigravity.google.com`**. Questa piattaforma web-native offre un'esperienza visiva ricca, reattiva e ottimizzata per schermi sia desktop che mobile.

### 11.2.1 Architettura di Relay Outbound: Zero Porte Aperte e NAT Traversal

La maggior parte degli ambienti di sviluppo opera all'interno di reti locali protette: router Wi-Fi domestici con NAT (Network Address Translation) o reti aziendali e universitarie schermate da firewall rigidi con blocco del traffico in ingresso.

L'architettura di Antigravity elimina la necessità di configurare porte virtuali (port-forwarding), richiedere IP pubblici statici o utilizzare configurazioni DDNS complesse:

```
┌────────────────────────┐                               ┌────────────────────────┐
│   DISPOSITIVO REMOTO   │                               │    WORKSTATION LOCAL   │
│   (Smartphone/Tablet)  │                               │    (Host di Sviluppo)  │
└───────────┬────────────┘                               └───────────┬────────────┘
            │                                                        │
            │ Connessione HTTPS/WSS                                  │ Connessione WebSocket
            │ verso porta 443                                        │ OUTBOUND verso porta 443
            ▼                                                        ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│               RELAY GATEWAY GOOGLE (relay.antigravity.google.com)               │
│                                                                                 │
│  - Autenticazione Identità Google (OAuth 2.0 / 2FA)                             │
│  - NAT Traversal & Multiplexing delle sessioni attive                           │
│  - Smistamento flussi cifrati (Nessuna ispezione o persistenza payload)         │
└─────────────────────────────────────────────────────────────────────────────────┘
```

#### Meccanica di Connessione:
- **Connessione Unidirezionale in Uscita (Outbound Only):** All'abilitazione del controllo remoto, l'istanza locale di Antigravity stabilisce una connessione WebSocket protetta (`wss://relay.antigravity.google.com:443/agent-session`) verso i server Google. Poiché la connessione è originata dall'interno della rete locale verso l'esterno sulla porta 443 standard HTTPS, viene autorizzata in modo trasparente da qualsiasi router NAT e firewall aziendale.
- **NAT Traversal Nativo:** Il Relay Gateway mantiene aperto il canale bidirezionale tramite pacchetti di keep-alive leggeri. Quando l'utente invia un'istruzione dal browser web, il gateway la instrada istantaneamente attraverso il socket già aperto con la macchina locale.
- **Zero Open Ingress Ports:** Nessuna porta TCP/UDP viene aperta in ascolto verso internet sulla macchina locale, eliminando qualsiasi superficie di attacco esposta alla scansione di porte esterne.

---

## 11.3 Procedura di Accoppiamento (Pairing) e Handshake Crittografico

Per garantire che solo l'utente autorizzato possa accedere e controllare il proprio agente, Antigravity implementa una procedura di associazione crittografica a più fattori.

### Step 1: Abilitazione del Servizio sulla Macchina Locale

Puoi abilitare il bridge remoto direttamente dall'interfaccia grafica dell'IDE oppure tramite la configurazione globale.

> **Ambiente Operativo:** [Usa in Antigravity IDE]

Nell'IDE, apri **Settings -> Remote Control** e attiva l'opzione **"Enable Cloud Remote Bridge"**. In alternativa, modifica il file di configurazione globale `~/.gemini/config/config.json`:

```json
{
  "userSettings": {
    "remoteControlEnabled": true,
    "remoteControlHostname": "workstation-lab-pavia",
    "remoteControlNotifyOnToolUse": true,
    "remoteControlSessionTimeoutMinutes": 480
  }
}
```

In ambienti senza interfaccia grafica, avvia il processo di accoppiamento direttamente dal terminale:

> **Ambiente Operativo:** [Solo Terminale CLI]

```bash
# Avvia la procedura di pairing da terminale
agy remote pair
```

```text
[Antigravity Remote Bridge] Inizializzazione connessione sicura...
[Antigravity Remote Bridge] Connesso a relay.antigravity.google.com:443 (TLS 1.3)
[Antigravity Remote Bridge] Identificativo installazione: a8b4c2-7f91-4e20-b61a
---------------------------------------------------------------------
CODICE DI ACCOPPIAMENTO A 6 CARATTERI:   X 7 K 9 M 2
Valido per i prossimi 05:00 minuti.
Apri https://antigravity.google.com sul tuo dispositivo ed effettua il login.
---------------------------------------------------------------------
In attesa di autorizzazione da parte del client remoto...
```

### Step 2: Login e Accoppiamento su antigravity.google.com

1. Apri il browser web sul tuo dispositivo portatile (smartphone, tablet o altro PC) e visita **`https://antigravity.google.com`**.
2. Effettua l'accesso con il medesimo account Google associato alla tua licenza di Antigravity.
3. Seleziona **"Add Workstation"** (o "Collega Nuova Postazione") e inserisci il codice di accoppiamento a 6 caratteri generato localmente (es. `X7K9M2`).
4. **Handshake Crittografico:** I due endpoint scambiano chiavi pubbliche effimere mediante l'algoritmo ECDH (Elliptic-Curve Diffie-Hellman su Curve25519). Tutte le successive comunicazioni vengono cifrate con algoritmo simmetrico **AES-256-GCM**, garantendo che il server di relay non possa decifrare il payload scambiato.

---

## 11.4 La Dashboard Web: Monitoraggio Live e Interazione Remota

Una volta completato l'accoppiamento, la dashboard web su `antigravity.google.com` presenta una console operativa completa:

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│ antigravity.google.com          [Postazione: workstation-lab-pavia ● ONLINE]    │
├──────────────────────────┬──────────────────────────────────────────────────────┤
│ TASK TREE ATTIVO         │ LOG & STREAMING TERMINALE                            │
│ ├─ Refactoring Auth      │ [14:32:01] Inizio analisi modulo src/auth/jwt.py    │
│ │  ├─ Test unitari (OK)  │ [14:32:05] AST parse completato: 4 violazioni trovate│
│ │  └─ Migrazione bcrypt  │ [14:32:12] Esecuzione: pytest tests/test_auth.py     │
│ └─ Fix Regressione DB    │ [14:32:18] 14 passati, 0 falliti in 1.42s            │
├──────────────────────────┴──────────────────────────────────────────────────────┤
│ GIT DIFF IN TEMPO REALE                                                         │
│ --- a/src/auth/jwt.py                                                           │
│ +++ b/src/auth/jwt.py                                                           │
│ @@ -45,2 +45,3 @@                                                               │
│ -    token = jwt.encode(payload, SECRET, algorithm="HS256")                     │
│ +    # Utilizzo di algoritmo asimmetrico conforme a policy aziendale            │
│ +    token = jwt.encode(payload, PRIVATE_KEY, algorithm="RS256")                │
├─────────────────────────────────────────────────────────────────────────────────┤
│ 💬 CHAT CON L'AGENTE: [Aggiungi anche il controllo sulla scadenza del token   ] │
│ [INVIA] [⏸ SOSPENDI TASK] [⏹ TERMINA EMERGENZA (KILL)]                          │
└─────────────────────────────────────────────────────────────────────────────────┘
```

### Funzionalità Chiave della Dashboard:
- **Gestore Multi-Workstation:** Puoi connettere e visualizzare più macchine contemporaneamente (es. PC ufficio, server GPU in laboratorio, laptop personale) e passare da una postazione all'altra con un solo tocco.
- **Task Tree Dinamico:** Visualizza in tempo reale l'albero di decomposizione dei task, con indicazione immediata delle sotto-fasi completate, in corso o in stato di errore.
- **Streaming Terminale ad Alta Efficienza:** I messaggi di output della console vengono compressi e trasmessi con latenza minima, consentendo di verificare l'avanzamento dei comandi lunghi (compilazioni, download, suite di test).
- **Ispezione Live dei File e dei Diff:** Puoi analizzare i file appena modificati o creati dall'agente con evidenziazione sintattica dei `git diff`, permettendo una rapida verifica visuale prima di qualsiasi commit.
- **Console di Interazione Bidirezionale:** Puoi digitare ulteriori indicazioni correttive, chiarire ambiguità o inviare nuovi prompt in qualunque momento.

---

## 11.5 Human-in-the-Loop Remoto & Approvazione Push su Dispositivi Mobili

Il rischio principale della gestione a distanza di un agente autonomo risiede nell'esecuzione incontrollata di comandi potenzialmente distruttivi (es. cancellazione di volumi Docker, migrazioni distruttive su database, comandi shell non reversibili come `rm -rf`).

Antigravity colma questa criticità implementando un rigoroso protocollo di **Human-in-the-Loop Remoto** assistito da notifiche push native:

```
┌────────────────────────────────────────────────────────┐
│ 🔔 Notifica Push su Dispositivo Mobile (PWA / Browser) │
│                                                        │
│ ANTIGRAVITY RICHIESTA AUTORIZZAZIONE TOOL CRITICO      │
│ Postazione: workstation-lab-pavia                      │
│ Tool: run_command                                      │
│ Comando: alembic upgrade head                          │
│                                                        │
│        [ ✅ APPROVA ]         [ ❌ RIFIUTA ]           │
└────────────────────────────────────────────────────────┘
```

### Meccanismo di Funzionamento:
1. **Rilevamento del Tool Critico:** Quando l'agente locale tenta di invocare un'azione catalogata come sensibile (modifica al filesystem di sistema, chiamate di rete esterne, esecuzione di comandi bash/powershell), il runtime locale blocca l'esecuzione ed entra in stato di attesa attiva (*paused awaiting approval*).
2. **Invio della Notifica Push:** Il Relay Gateway invia una notifica push Web Push (W3C standard) al browser o allo smartphone registrato dell'utente.
3. **Pannello di Revisione Interattivo:** Toccando la notifica, l'interfaccia apre un modale che evidenzia:
   - Il comando esatto o il file coinvolto.
   - La motivazione dichiarata dall'agente per l'esecuzione.
   - I potenziali impatti sul sistema.
4. **Azione dell'Utente e Audit Log:**
   - **Approva:** L'agente riceve il token di sblocco e procede con l'operazione.
   - **Rifiuta:** L'agente riceve una risposta negativa con l'eventuale nota correttiva fornita dall'utente (es. *"Non eseguire la migrazione in produzione adesso"*), riorientando la pianificazione.
   - Tutte le interazioni remote vengono registrate con firma crittografica nel file di audit locale `.agents/audit/remote_audit.log`, tracciando timestamp, identificativo del dispositivo e decisione adottata.

---

## 11.6 Sicurezza delle Sessioni, Token e Revoca Istantanea (Kill-Switch)

L'accesso remoto offre un elevato livello di comodità operativa ma richiede solidi meccanismi di salvaguardia per prevenire abusi o intrusioni:

### Politiche di Sicurezza:
- **Scadenza delle Sessioni (TTL):** Le sessioni web hanno una validità massima predefinita di 8 ore di inattività, al termine delle quali è richiesto un nuovo pairing crittografico.
- **Rotazione dei Token di Sessione:** Durante la sessione attiva, le chiavi crittografiche di trasporto vengono rigenerate e ruotate automaticamente ogni 60 minuti tramite Perfect Forward Secrecy (PFS).
- **Limitazione delle Azioni Fuori dal Workspace:** Anche in modalità approvata, il runtime vieta l'esecuzione di comandi che tentino di accedere a directory esterne al repository se non espressamente autorizzate nella configurazione `nonWorkspaceFileAccessPolicy`.

### Revoca Immediata e Kill-Switch

Qualora il dispositivo portatile venga smarrito o vi sia il sospetto di una compromissione della sessione, puoi revocare l'accesso all'istante:

> **Ambiente Operativo:** [Usa in Antigravity IDE]

Dall'interfaccia dell'IDE locale:
1. Apri **Settings -> Remote Control**.
2. Clicca sul pulsante rosso **"Revoke All Remote Sessions & Keys"**.
3. Il socket di rete con il relay viene interrotto immediatamente e tutti i token crittografici associati alla macchina vengono invalidati istantaneamente sul server centrale.

> **Ambiente Operativo:** [Solo Terminale CLI]

Da riga di comando sulla macchina locale:

```bash
# Revoca istantanea di tutte le sessioni remote attive
agy remote revoke --all
```

Dalla dashboard web su `antigravity.google.com`:
Accedi alla sezione **Account Security -> Active Workstations**, individua la macchina interessata e seleziona **"Disconnect & Terminate Session"**.

---

## 11.7 METODI SECONDARI — Connessioni Headless SSH e Multiplexer (`tmux`)

Nei casi in cui si operi su macchine server prive di interfaccia grafica (es. cluster HPC universitari, server rack dedicati, istanze cloud AWS EC2 o Google Cloud Engine) oppure in ambienti dove le policy aziendali vietano l'uso del bridge web Google, il controllo remoto si effettua via **SSH** in combinazione con un multiplexer di terminale.

### Il Rischio delle Disconnessioni di Rete e il Ruolo di `tmux`

Quando avvii un task lungo tramite la CLI di Antigravity (`agy`) all'interno di una semplice connessione SSH, la perdita temporanea del segnale Wi-Fi o la chiusura del computer portatile invia un segnale `SIGHUP` alla shell remota, provocando l'immediata terminazione forzata del processo dell'agente.

L'uso di un multiplexer di terminale come **`tmux`** (o `screen`) garantisce che il processo continui a essere eseguito in background sul server, indipendentemente dallo stato della tua connessione remota.

> **Ambiente Operativo:** [Solo Terminale CLI]

### Workflow Operativo Passo-Passo con SSH e `tmux`:

#### 1. Connessione al Server e Creazione della Sessione
```bash
# Connessione al server remoto di calcolo
ssh sviluppatore@server-lab.ateneo.it

# Creazione di una sessione tmux dedicata ad Antigravity
tmux new -s antigravity-workspace
```

#### 2. Avvio dell'Agente in Modalità Headless o Interattiva
All'interno della sessione `tmux`, avvia l'agente sul progetto di lavoro:
```bash
cd /opt/projects/bio-pipeline

# Avvio del task con streaming su terminale
agy "Esegui il refactoring del parser FASTA e implementa i test unitari di conformità" --stream
```

#### 3. Sconnessione Volontaria dalla Sessione (Detach)
Premi la sequenza di tasti:
`Ctrl + B`, seguito da `D` (Detach).

Il terminale visualizzerà:
`[detached (from session antigravity-workspace)]`

A questo punto puoi chiudere la connessione SSH, spegnere il tuo laptop o cambiare rete: l'agente continuerà a lavorare sul server a piena velocità.

#### 4. Riaggancio alla Sessione da Qualsiasi Dispositivo (Attach)
In qualunque momento (anche da un client SSH su smartphone tramite Termius o JuiceSSH), riconnettiti e riaggancia la sessione attiva:
```bash
# Riconnessione immediata alla sessione persistente
ssh sviluppatore@server-lab.ateneo.it -t "tmux attach -t antigravity-workspace"
```

Troverai l'intero output del terminale, lo stato attuale dei file modificati e l'eventuale richiesta di conferma dell'agente esattamente come li avevi lasciati.

---

## 11.8 Esecuzione Headless come Demone di Sistema (`systemd` e Servizi Windows)

Per flussi di lavoro continuativi — come worker che attendono compiti via webhook, agenti dedicati alla continuous integration locale o servizi di refactoring automatico notturno — Antigravity può essere eseguito come **demone persistente di sistema**.

### 11.8.1 Configurazione come Servizio `systemd` su Linux

Crea il file di servizio `/etc/systemd/system/antigravity-worker.service`:

```ini
[Unit]
Description=Antigravity Autonomous Agent Background Worker
After=network.target local-fs.target
Wants=network-online.target

[Service]
Type=simple
User=sviluppatore
Group=sviluppatore
WorkingDirectory=/home/sviluppatore/progetti/core-service
Environment="PATH=/home/sviluppatore/.local/bin:/usr/local/bin:/usr/bin"
Environment="GEMINI_API_KEY=AIzaSyD-TuaChiaveApiSegreta12345"
ExecStart=/home/sviluppatore/.local/bin/agy daemon --workspace /home/sviluppatore/progetti/core-service --port 8085
Restart=on-failure
RestartSec=10
LimitNOFILE=65535

# Protezioni di sicurezza del demone
ProtectSystem=full
ProtectHome=read-only
ReadWritePaths=/home/sviluppatore/progetti/core-service /home/sviluppatore/.gemini

[Install]
WantedBy=multi-user.target
```

Attiva e avvia il demone:
```bash
# Ricarica la configurazione dei servizi di sistema
sudo systemctl daemon-reload

# Abilita l'avvio automatico al boot e avvia il servizio
sudo systemctl enable --now antigravity-worker.service

# Verifica lo stato e i log in tempo reale
sudo systemctl status antigravity-worker.service
journalctl -u antigravity-worker.service -f
```

### 11.8.2 Configurazione come Servizio di Background su Windows (PowerShell)

Su workstation Windows, puoi registrare l'agente come operazione pianificata persistente o servizio di background utilizzando PowerShell con privilegi di Amministratore:

```powershell
# Creazione dell'azione per l'esecuzione in background di Antigravity
$taskAction = New-ScheduledTaskAction -Execute "agy.exe" `
    -Argument "daemon --workspace C:\Sviluppo\EnterpriseApp --port 8085" `
    -WorkingDirectory "C:\Sviluppo\EnterpriseApp"

# Trigger: avvio automatico all'accesso utente o al boot di sistema
$taskTrigger = New-ScheduledTaskTrigger -AtLogon

# Configurazione delle credenziali e priorità di esecuzione
$taskPrincipal = New-ScheduledTaskPrincipal -UserId "$env:USERDOMAIN\$env:USERNAME" -LogonType S4U -RunLevel Highest

# Registrazione del task persistente
Register-ScheduledTask -TaskName "AntigravityBackgroundWorker" -Action $taskAction -Trigger $taskTrigger -Principal $taskPrincipal

# Avvio immediato del servizio
Start-ScheduledTask -TaskName "AntigravityBackgroundWorker"
```

---

## 11.9 Mesh VPN Privata e Tunneling (Tailscale & Cloudflare Zero Trust)

Quando l'ambiente richiede un accesso remoto diretto senza intermediari cloud terzi e senza esporre porte aperte su internet, le soluzioni ideali sono le **Mesh VPN** (come Tailscale basata su WireGuard) o i **Tunnel Zero Trust** (come Cloudflare Tunnel).

### 11.9.1 Connessione Diretta Tramite Tailscale MagicDNS

**Tailscale** crea una rete virtuale privata cifrata (chiamata *tailnet*) tra tutti i tuoi dispositivi, assegnando a ciascuno un indirizzo IP privato univoco (nell'intervallo `100.x.y.z`) e un nome DNS risolvibile internamente (*MagicDNS*):

```
┌────────────────────────┐                               ┌────────────────────────┐
│   DISPOSITIVO MOBILE   │                               │    WORKSTATION FISSA   │
│   (Laptop in Viaggio)  │                               │    (Ufficio / Casa)    │
│   IP: 100.64.0.15      │ ◄═══════════════════════════► │    IP: 100.64.0.2      │
└────────────────────────┘     Tunnel WireGuard Diretto  └────────────────────────┘
                               (Peer-to-Peer Crittografato)
```

#### Configurazione Operativa:
1. Installa Tailscale sulla macchina di sviluppo e sul tuo dispositivo portatile:
   ```bash
   # Installazione e accesso sulla macchina host
   tailscale up
   ```
2. Individua l'indirizzo privato assegnato alla macchina di sviluppo:
   ```bash
   tailscale ip -4
   # Esempio restituito: 100.64.0.2 (nome: dev-workstation.tailnet-xyz.ts.net)
   ```
3. Avvia la webview o il server API locale di Antigravity vincolandolo all'interfaccia Tailscale:
   ```bash
   agy serve --host 100.64.0.2 --port 9090
   ```
4. Sul tuo dispositivo mobile o portatile (connesso alla medesima tailnet), apri il browser e naviga su `http://100.64.0.2:9090`. La comunicazione viaggia direttamente peer-to-peer con crittografia ChaCha20-Poly1305, senza alcun transito attraverso server terzi.

### 11.9.2 Accesso Protetto con Cloudflare Zero Trust Tunnel

Se la tua organizzazione utilizza Cloudflare, puoi esporre la dashboard interna di Antigravity proteggendola con Identity Provider aziendale (Okta, Google Workspace, Azure AD) tramite il client `cloudflared`:

```bash
# Creazione del tunnel sicuro verso la rete Cloudflare
cloudflared tunnel create antigravity-dev

# Instradamento del traffico verso l'istanza locale di Antigravity
cloudflared tunnel route dns antigravity-dev agent-pavia.azienda.com

# Avvio del connettore locale
cloudflared tunnel run --url http://localhost:9090 antigravity-dev
```

In questo modo, l'accesso tramite URL pubblico `https://agent-pavia.azienda.com` richiederà l'autenticazione a due fattori dell'organizzazione prima di raggiungere la tua macchina locale.

---

## 11.10 Tutorial Passo-Passo: Supervisione Mobile di un Refactoring Notturno

In questo tutorial pratico simuleremo una situazione comune: prima di lasciare l'ufficio, avvii una complessa operazione di refactoring del livello dati e della suite di test. Durante la serata, verificherai l'avanzamento dallo smartphone e approverai a distanza l'applicazione di una migrazione di database critica.

### Fase 1: Avvio del Task in Ufficio

> **Ambiente Operativo:** [Usa in Antigravity IDE]

Dalla postazione fissa, assicurati che il bridge remoto sia attivo in **Settings -> Remote Control**. Invia all'agente l'istruzione di refactoring:

**🗣️ Tu (Prompt):**
> "Riorganizza il modulo `src/database/` separando i repository di accesso dati dai modelli di dominio SQLAlchemy. Aggiorna la suite di test in `tests/test_repositories/` ed esegui i test. Se tutti i test hanno esito positivo, prepara la migrazione Alembic per i nuovi indici su `user_transactions` ma richiedi esplicitamente l'approvazione prima di applicarla al database locale."

**🤖 Agente (Risposta):**
> "Ricevuto. Pianificazione avviata:
> 1. Ispezione dei file attuali in `src/database/models.py`.
> 2. Estrazione dell'interfaccia Repository in `src/database/repositories/`.
> 3. Refactoring dei test unitari ed esecuzione con `pytest`.
> 4. Generazione script di migrazione `alembic revision --autogenerate`.
> 
> *Stato del bridge remoto: Attivo e sincronizzato con `antigravity.google.com`.*
> *Inizio elaborazione autonoma...*"

Puoi bloccare lo schermo del computer e lasciare la postazione.

---

### Fase 2: Monitoraggio Serale da Smartphone

> **Ambiente Operativo:** [Usa nell'App 2.0]

Dal tuo smartphone, apri il browser e naviga su `https://antigravity.google.com`:

1. Esegui il login con il tuo account Google.
2. La dashboard mostra la postazione `workstation-lab-pavia` con stato **"In Elaborazione (Fase 3/4)"**.
3. Cliccando su **"Log Streaming"**, verifichi che l'agente ha completato con successo la scrittura dei repository e ha eseguito la suite di test:
   ```text
   pytest tests/test_repositories/
   ==================== 28 passed in 3.12s ====================
   ```
4. Cliccando su **"Live Diff"**, ispezioni le modifiche apportate ai file Python, visualizzando i blocchi di codice aggiunti e rimossi direttamente dall'interfaccia touch del telefono.

---

### Fase 3: Ricezione della Notifica Push e Approvazione Human-in-the-Loop

Dopo alcuni minuti, sullo smartphone appare una notifica push del browser:

> **Antigravity Push Alert:**  
> *Richiesta di autorizzazione tool: `run_command`*  
> *Comando: `alembic upgrade head`*  
> *Target: SQLite Database locale `dev_store.db`*

1. Tocchi la notifica per aprire il pannello di dettaglio.
2. L'interfaccia mostra il file di migrazione appena generato:
   ```python
   def upgrade() -> None:
       op.create_index('ix_transactions_user_id_created', 'user_transactions', ['user_id', 'created_at'])
   ```
3. Poiché la modifica all'indice è corretta e sicura, premi il pulsante verde **"✅ Approva Esecuzione"**.
4. L'agente locale riceve il segnale crittografato, esegue il comando e notifica la riuscita:

**🤖 Agente (Risposta su Web Dashboard):**
> "Comando `alembic upgrade head` eseguito con successo. La migrazione degli indici è stata completata senza errori. Tutti i 28 test rimangono verdi. Task completato con successo; le modifiche sono pronte per il commit."

---

## 11.11 Matrice Comparativa dei Metodi di Controllo Remoto

La tabella seguente riassume le caratteristiche distintive di ciascun approccio, facilitando la scelta del metodo più adatto al tuo contesto operativo:

| Criterio di Valutazione | Web Cloud (`antigravity.google.com`) | SSH Diretto + `tmux` | Demone di Sistema (`systemd`) | Mesh VPN Privata (Tailscale) |
|---|---|---|---|---|
| **Ambiente Primario** | Laptop, Tablet, Smartphone (Browser) | Terminale CLI Linux/macOS | Server Headless, Worker CI | Rete privata multi-device |
| **Apertura Porte Router/Firewall** | **Nessuna** (Outbound 443 WSS) | Porta 22 in ingresso (o bastion) | Nessuna (o porta locale) | **Nessuna** (WireGuard UDP) |
| **Esperienza Utente (UX)** | Grafica ricca, Task Tree, Diffs live | Solo testo / Terminale | Nessuna GUI (Log su file) | Dipende dal client collegato |
| **Notifiche Push Mobile** | **Sì nativo** (Web Push standard) | No (richiede bot ausiliari) | No | No (a meno di webhook) |
| **Human-in-the-Loop** | Bottoni visuali Approva / Rifiuta | Interazione da prompt shell | Policy automatica (`--yes`) | Manuale via webview |
| **Resistenza a Disconnessioni** | **Totale** (Relay bufferizza eventi) | **Totale** (Sessione `tmux` isolata) | **Totale** (Gestito da OS) | Media (dipende dal client) |
| **Dipendenza da Servizi Cloud** | Richiede account Google | **Zero** (100% autonomo) | **Zero** (100% autonomo) | Minima (control plane VPN) |
| **Idoneità Ambienti Air-Gapped** | No (richiede accesso a internet) | **Sì** (su rete LAN isolata) | **Sì** (su rete locale) | **Sì** (con Headscale locale) |
| **Complessità di Setup** | Minima (Accoppiamento a 6 cifre) | Bassa (Chiavi SSH standard) | Media (Scrittura file unit) | Bassa (Installazione agent) |

---

## 11.12 Troubleshooting e Risoluzione dei Problemi

In caso di difficoltà di connessione o funzionamento anomalo del controllo remoto, consulta le soluzioni seguenti:

### 1. Codice di Accoppiamento Scaduto o Non Riconosciuto
- **Sintomo:** Il browser su `antigravity.google.com` restituisce l'errore *"Invalid or expired pairing code"*.
- **Risoluzione:** I codici di accoppiamento a 6 caratteri hanno una durata limitata di 5 minuti per prevenire attacchi brute-force. Rigenera un nuovo codice digitando `agy remote pair` nel terminale o cliccando su "Genera Nuovo Codice" nelle impostazioni dell'IDE.

### 2. Caduta della Connessione WebSocket dietro Proxy Aziendale con SSL Inspection
- **Sintomo:** L'IDE locale segnala continuo stato di riconnessione (`Reconnecting to relay...`).
- **Causa:** Molti proxy aziendali (DPI - Deep Packet Inspection) interrompono i canali WebSocket persistenti se rilevano connessioni aperte per oltre 60 secondi senza traffico HTTP standard.
- **Risoluzione:** Assicurati che l'opzione `remoteControlKeepAliveSeconds` sia impostata a un valore inferiore a 30 nel file `config.json` per forzare l'invio frequente di frame `PING/PONG`:
  ```json
  {
    "userSettings": {
      "remoteControlKeepAliveSeconds": 25
    }
  }
  ```

### 3. Mancata Ricezione delle Notifiche Push sullo Smartphone
- **Sintomo:** L'agente rimane in attesa di approvazione ma il telefono non visualizza alcuna notifica.
- **Risoluzione:**
  1. Verifica che nel browser dello smartphone i permessi di invio notifiche siano autorizzati per il dominio `https://antigravity.google.com`.
  2. Su dispositivi Android o iOS, assicurati che la modalità di risparmio energetico non blocchi l'esecuzione in background del browser o della Web App (PWA).

### 4. Processi Zombie o Sessioni `tmux` Bloccate
- **Sintomo:** Riconnettendosi a una sessione SSH, l'agente risulta bloccato o non risponde più ai prompt.
- **Risoluzione:** Verifica i processi attivi con `ps aux | grep agy`. Se necessario, termina delicatamente il processo tramite segnale di terminazione (`kill -15 <PID>`). Puoi visualizzare l'elenco delle sessioni attive con `tmux ls` e distruggere una sessione orfana con `tmux kill-session -t <nome-sessione>`.

---

[⬅️ Torna all'Indice](../index.md) | [Capitolo Precedente: 10. Uso in Locale](./10_local_usage.md) | [Prossimo Capitolo: 12. Riferimento CLI](./12_cli_reference.md)
