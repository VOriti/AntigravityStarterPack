# 12. Riferimento CLI (`agy`): Automazione da Terminale, Scripting e CI/CD

> **Ambiente Operativo:** [Solo Terminale CLI]  
> **Compatibilità:** Linux (bash, zsh), macOS (zsh, bash), Windows (PowerShell 7+, Windows PowerShell, WSL)

L'Interfaccia a Riga di Comando di Antigravity (`agy`, con alias esteso `antigravity`) è lo strumento primario per interagire con il motore agenziale al di fuori dell'interfaccia grafica. Progettata per sviluppatori, ingegneri DevOps e data scientist, la CLI permette di eseguire task complessi di refactoring, analisi statica, revisione del codice, diagnostica hardware e scaffolding architetturale all'interno di script di shell e pipeline di integrazione continua (CI/CD).

Grazie all'architettura disaccoppiata e headless, `agy` espone l'intero set di capacità dell'ambiente Antigravity attraverso flussi deterministici, contratti di uscita standardizzati (exit codes) e molteplici formati di output strutturato (JSON, NDJSON, SARIF).

---

## 12.1 Introduzione & Architettura della CLI `agy`

### 12.1.1 Ruolo e Filosofia Headless
Nei moderni contesti di ingegneria del software, l'interazione interattiva utente-agente rappresenta solo una frazione del ciclo di vita dello sviluppo. La modalità *headless* (senza interfaccia utente a caratteri o grafica) trasforma Antigravity in un runtime autonomo di esecuzione:
- **Esecuzione in Pipeline CI/CD:** Analisi differenziale delle Pull Request, blocco automatico di regressioni o vulnerabilità e generazione di commenti di revisione.
- **Scripting Batch e Cron:** Esecuzione di migrazioni su vasta scala su centinaia di repository o moduli disaccoppiati senza presidio umano.
- **Integrazione in IDE e Tool Esterni:** Possibilità di richiamare le routine agenziali da editor come Neovim, Emacs, script di build (Make, Just, Gradle) o estensioni personalizzate.

### 12.1.2 Architettura del Runtime Locale (`language_server`)
Il comando `agy` non è un semplice script wrapper monolitico, bensì un client leggero che instaura una connessione IPC (Inter-Process Communication) o socket locale con il motore sottostante `language_server`:
1. **Host del Servizio:** Il binario `language_server` (distribuito internamente come runtime Node 20 / V8 compilato) gestisce la memoria dell'agente, il caricamento delle regole (Rules), la risoluzione dei pattern glob e l'esecuzione protetta dei lifecycle hook.
2. **Client CLI (`agy`):** Traduce i parametri della riga di comando in richieste RPC strutturate, monitora lo streaming dei turni dell'LLM, cattura gli output dei tool eseguiti nel terminale sandbox e restituisce i codici di uscita deterministici al chiamante del sistema operativo.
3. **Isolamento e Sicurezza:** L'esecuzione di comandi shell e modifiche al filesystem da parte dell'agente è regolata dalle policy definite nel workspace (`.agents/`), ereditando le restrizioni impostate nei flag o nelle variabili d'ambiente.

```text
+-----------------------------------------------------------------------+
|                             TERMINALE CLI                             |
|       (Bash / Zsh / PowerShell / GitHub Actions Runner / Cron)        |
+-----------------------------------------------------------------------+
                                  │
                       Invocazione comando agy
                                  ▼
+-----------------------------------------------------------------------+
|                           CLIENT CLI (agy)                            |
|  - Parsing argomenti e flag         - Normalizzazione parametri       |
|  - Rilevazione contesto Git / file   - Selezione formato output        |
+-----------------------------------------------------------------------+
                                  │
                    Protocollo RPC / IPC Locale
                                  ▼
+-----------------------------------------------------------------------+
|                    RUNTIME CORE (language_server)                     |
|  - Scoping Regole (.agents/rules/)  - Engine Inferenza (Cloud/Locale) |
|  - Sandbox Esecuzione Terminale     - Lifecycle Hooks (.agents/hooks) |
|  - Model Context Protocol (MCP)     - Generatore Report (JSON/SARIF)  |
+-----------------------------------------------------------------------+
```

### 12.1.3 Installazione e Configurazione del PATH

#### Linux e macOS
```bash
# Scarica e installa il binario ufficiale in $HOME/.antigravity/bin
curl -fsSL https://antigravity.google/install.sh | bash

# Aggiungi il percorso al profilo di shell (~/.bashrc o ~/.zshrc)
echo 'export PATH="$HOME/.antigravity/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

# Verifica dell'installazione
agy --version
```

#### Windows (PowerShell 7+ / Windows PowerShell)
```powershell
# Esecuzione script di installazione per Windows
irm https://antigravity.google/install.ps1 | iex

# Aggiunta persistente al PATH dell'utente
[System.Environment]::SetEnvironmentVariable(
    "Path",
    $env:Path + ";$env:USERPROFILE\.gemini\antigravity\bin",
    [System.EnvironmentVariableTarget]::User
)

# Ricarica la variabile d'ambiente nella sessione corrente
$env:Path = [System.Environment]::GetEnvironmentVariable("Path", "User")

# Verifica dell'installazione
agy --version
```

> 📌 **Nota sui wrapper per Windows:** Nell'ambiente Windows, il binario `language_server.exe` può essere richiamato anche mediante il comando batch `%USERPROFILE%\.gemini\antigravity\bin\agentapi.bat` o tramite l'alias PowerShell `agy`.

### 12.1.4 Verifica dell'Ambiente e Primo Avvio (`agy doctor`)
Prima di avviare workflow intensivi, verifica lo stato di salute dei componenti locali (GPU, acceleratori, endpoint LLM, server MCP e credenziali):
```bash
agy doctor
```

---

## 12.2 Tassonomia dei Comandi Principali

### 12.2.1 Alberatura Generale dei Comandi
La struttura gerarchica della CLI riflette la modularità dell'architettura agenziale:

```text
agy
├── [PROMPT]                         # Invocazione ad-hoc / single-shot con contesto implicito o esplicito
├── run <workflow> [PARAMETRI]       # Esegue un workflow memorizzato in .agents/workflows/
├── init [TEMPLATE] [OPZIONI]        # Inizializza l'architettura agenziale del workspace
├── doctor                           # Diagnostica completa di runtime, GPU, VRAM, LLM e MCP
├── mcp <SUBCOMANDO>                 # Gestione e ispezione dei Server Model Context Protocol
│   ├── list                         # Elenca i server attivi e i tool registrati
│   ├── test <server-name>           # Esegue handshake diagnostico e verifica schema
│   ├── add <server-name>            # Registra un nuovo server in mcp_config.json
│   └── remove <server-name>         # Disconnette e rimuove un server configurato
├── remote <SUBCOMANDO>              # Pairing cloud e controllo remoto via antigravity.google.com
│   ├── pair                         # Avvia il protocollo di associazione sicura
│   ├── status                       # Mostra lo stato del bridge WebRTC/WebSocket
│   ├── tunnel --port <numero>       # Espone la sessione su canale crittografato
│   └── unpair                       # Revoca i token e disconnette il nodo
├── config <SUBCOMANDO>              # Manipolazione della configurazione di sistema e workspace
│   ├── get <chiave>                 # Legge il valore di un parametro attivo
│   ├── set <chiave> <valore>        # Aggiorna una voce di configurazione
│   └── list                         # Stampa l'albero completo delle opzioni attive
└── auth <SUBCOMANDO>                # Gestione credenziali, token OAuth e quote API
    ├── login                        # Autenticazione interattiva nel browser
    ├── logout                       # Eliminazione sicura delle chiavi di sessione
    └── status                       # Verifica stato licenza, identità e quote residue
```

### 12.2.2 `agy [prompt]` — Invocazione Diretta e Single-Shot
Consente di assegnare all'agente un compito immediato nel contesto del repository corrente.

```bash
# Esecuzione interattiva nel terminale corrente
agy "Ottimizza le query SQL in src/repository/user.py per prevenire problemi N+1"

# Esecuzione con iniezione selettiva del contesto
agy "Genera una suite di unit test per la logica di calcolo sconto" \
  --context src/billing/discounts.py \
  --context tests/billing/fixtures.py

# Invocazione tramite pipe standard input (stdin)
git diff HEAD~1 | agy "Analizza questo changeset e sintetizza potenziali vulnerabilità di sicurezza" --stdin
```

### 12.2.3 `agy run <workflow>` — Esecuzione Workflow Strutturati
I workflow definiti nei file Markdown all'interno della cartella `.agents/workflows/` (o globali in `~/.gemini/workflows/`) possono essere lanciati direttamente per nome:

```bash
# Esecuzione di un workflow parametrizzato
agy run analyze-dataset --param dataset_path="data/raw/samples.parquet" --param threshold=0.05

# Esecuzione headless con flag non-interattivo per automazione CI
agy run security-audit --headless --non-interactive -o reports/audit.json --format json
```

### 12.2.4 `agy init` — Scaffolding del Workspace
Crea l'impalcatura standard delle cartelle e dei file di configurazione agenziali nel progetto corrente.

```bash
# Inizializzazione standard interattiva
agy init

# Inizializzazione automatizzata con template specifico e regole restrittive
agy init enterprise-python --rules strict-typing --hooks dlp-guard --non-interactive
```
La directory risulterà così popolata con `.agents/rules/`, `.agents/workflows/`, `.agents/hooks.json` e `.agents/mcp_config.json`.

### 12.2.5 `agy doctor` — Diagnostica Pre-Flight di Sistema
Esegue una scansione completa di tutte le dipendenze software, le risorse hardware e le integrazioni di rete.

```bash
# Scansione pre-flight standard a terminale
agy doctor

# Diagnostica approfondita con output formattato per script di monitoraggio
agy doctor --verbose --format json
```

### 12.2.6 `agy mcp` — Gestione e Test dei Server MCP
Fornisce strumenti di amministrazione per il Model Context Protocol, consentendo di verificare la connettività e gli schemi JSON-RPC dei tool esposti:

```bash
# Elenco dei server configurati e relativo stato di caricamento (eager / lazy)
agy mcp list

# Test diagnostico e handshake per un server specifico
agy mcp test sqlite-server

# Registrazione di un nuovo server stdio
agy mcp add postgres-db --command "npx" --args "-y,@modelcontextprotocol/server-postgres,postgresql://user:pass@localhost:5432/crm"
```

### 12.2.7 `agy remote` — Pairing Cloud e Supervisione Remota
Gestisce il ciclo di vita del pairing con il portale Web companion su `antigravity.google.com`:

```bash
# Avvia la procedura di accoppiamento generando token a tempo e codice di pairing
agy remote pair

# Ispezione dello stato della sessione remota e latenza del canale
agy remote status

# Revoca immediata dell'autorizzazione remota
agy remote unpair
```

### 12.2.8 `agy config` — Ispezione e Modifica Parametri
Permette di visualizzare e alterare sia le preferenze di workspace (`.agents/config.json`) che le impostazioni globali dell'utente (`~/.gemini/config/`):

```bash
# Lettura del provider attivo
agy config get activeProvider

# Modifica globale del modello di inferenza predefinito
agy config set defaultModel "gemini-2.5-pro" --global

# Forzatura dell'indirizzo dell'endpoint locale Ollama
agy config set providers.ollama.baseUrl "http://127.0.0.1:11434/v1"
```

### 12.2.9 `agy auth` — Gestione Credenziali e Token Cloud
Supervisiona le chiavi crittografiche, i token OAuth e lo stato di quota sui provider cloud:

```bash
# Login interattivo con account sviluppatore Google / Enterprise
agy auth login

# Verifica validità del token corrente e tier di servizio
agy auth status

# Disconnessione e rimozione chiavi memorizzate nel portachiavi di sistema
agy auth logout
```

---

## 12.3 Matrice Esaustiva delle Opzioni e Flag Globali

### 12.3.1 Tabella Sinottica delle Opzioni CLI
La tabella seguente cataloga oltre 15 opzioni globali supportate dal runtime `agy`:

| Flag Breve | Flag Esteso | Tipo / Argomento | Valore Predefinito | Descrizione Funzionale | Esempio Pratico |
|---|---|---|---|---|---|
| `-h` | `--headless` | Booleano | `false` | Disabilita l'interfaccia interattiva da terminale (TUI); redirige lo stato e l'avanzamento su standard stream (stdout/stderr). | `agy "Audit" --headless` |
| `-y` | `--non-interactive` | Booleano | `false` | Concede automaticamente l'approvazione per tool non distruttivi ed evita prompt di blocco. | `agy run lint -y` |
| | `--dry-run` | Booleano | `false` | Esegue il ciclo di ragionamento dell'agente ed elabora il piano d'azione senza alterare file né eseguire comandi terminale reali. | `agy "Elimina log vecchi" --dry-run` |
| `-c` | `--context` | Percorso / Glob | Nessuno (ripetibile) | Inietta percorsi di file, intere cartelle o pattern glob direttamente nel contesto di lavoro iniziale. | `--context src/auth/ --context docs/` |
| `-f` | `--file` | Percorso | Nessuno (ripetibile) | Alias specifico per dichiarare singoli file di input prioritari. | `-f schema.prisma -f main.ts` |
| | `--diff` | Booleano | `false` | Cattura automaticamente il diff del workspace (`git diff HEAD`) e lo inietta nel prompt. | `agy "Revisiona modifiche" --diff` |
| | `--commit-range` | Stringa | Nessuno | Calcola e inietta il diff tra i due rami o commit specificati (es. `main..feature`). | `--commit-range "origin/main..HEAD"` |
| | `--rule` | Percorso | Nessuno (ripetibile) | Inietta forzatamente una specifica regola da file, scavalcando lo scoping per directory. | `--rule .agents/rules/strict_types.md` |
| `-m` | `--model` | Stringa | Modello attivo | Sovrascrive il modello LLM da utilizzare per la sessione corrente. | `-m qwen2.5-coder:32b` |
| | `--provider` | Stringa | `google` | Specifica il backend di inferenza (`google`, `ollama`, `vllm`, `openai-compat`). | `--provider ollama` |
| | `--local` | Booleano | `false` | Impone la modalità 100% offline; inibisce qualsiasi connessione verso API cloud esterne. | `agy "Refactor" --local` |
| | `--endpoint` | URL | Default provider | Imposta l'endpoint HTTP di base per il server di inferenza (compatibile OpenAI API). | `--endpoint http://10.0.0.12:8000/v1` |
| `-o` | `--output` | Percorso | `stdout` | Salva il report finale o la risposta elaborata nel file specificato anziché a video. | `-o reports/security_summary.md` |
| | `--format` | Stringa | `text` | Formato di serializzazione del risultato (`text`, `json`, `stream`, `markdown`, `sarif`). | `--format sarif` |
| `-q` | `--quiet` | Booleano | `false` | Sopprime banner, messaggi intermedi, animazioni di caricamento e log informativi. | `agy "Genera UUID" -q` |
| `-v` | `--verbose` | Booleano | `false` | Emette dettagli avanzati: metriche dei token per turno, trace JSON-RPC delle tool call e stato dei socket. | `agy doctor -v` |
| | `--max-turns` | Intero | `25` | Limite massimo di cicli di ragionamento/invocazione tool prima di arrestare l'agente. | `--max-turns 12` |
| | `--timeout` | Durata | Nessuno | Interrompe forzatamente il processo se non concluso entro il limite stabilito (es. `30s`, `15m`). | `--timeout 600s` |
| | `--network` | Stringa | `standard` | Policy di isolamento di rete per il terminale dell'agente (`offline`, `local_only`, `standard`). | `--network local_only` |
| | `--log-file` | Percorso | Nessuno | Redirige i log operativi e di diagnostica interna su file dedicato. | `--log-file /var/log/agy.log` |

### 12.3.2 Focus su Flag Critici: Targeting, Privacy e Limiti di Risorse
- **Iniezione del Contesto Selettivo (`--context`, `--diff`, `--commit-range`):** Nelle basi di codice estese, caricare l'intero progetto saturerebbe la context window. Combinando `--commit-range "origin/main..HEAD"` con `--context src/core/`, l'agente riceve esattamente le modifiche introdotte nella Pull Request insieme alla documentazione architetturale di supporto.
- **Isolamento Locale e Riservatezza (`--local`, `--network local_only`):** Quando si elaborano repository coperti da segreto industriale, vincoli HIPAA o dati sensibili, l'accoppiata di questi due flag assicura che né i prompt né i comandi del terminale possano dialogare con l'esterno della macchina.
- **Budget e Controllo delle Risorse (`--max-turns`, `--timeout`):** Nelle pipeline di integrazione continua automatizzate è mandatorio impostare un tetto massimo di turni (es. `--max-turns 15`) e un timeout esplicito (es. `--timeout 300s`) per prevenire loop imprevisti causati da suite di test bloccate o risposte ambigue.

---

## 12.4 Variabili d'Ambiente e Gerarchia di Risoluzione

### 12.4.1 Variabili di Sistema Supportate
La CLI `agy` riconosce un insieme completo di variabili di ambiente per automatizzare l'esecuzione in container, VM e agenti runner:

```bash
# Autenticazione Cloud e API Keys
export GEMINI_API_KEY="AIzaSyA..."                 # Chiave API per i modelli cloud Google Gemini
export ANTIGRAVITY_REMOTE_TOKEN="agt_tok_9f..."     # Token di accoppiamento con antigravity.google.com

# Modello e Provider di Inferenza
export ANTIGRAVITY_MODEL="gemini-2.5-pro"          # Modello di default se non specificato con --model
export ANTIGRAVITY_PROVIDER="google"               # Provider predefinito (google, ollama, vllm)
export ANTIGRAVITY_ENDPOINT="http://localhost:11434/v1" # Endpoint base per provider compatibili OpenAI
export ANTIGRAVITY_OFFLINE="1"                     # Imposta la modalità offline (equivalente a --local)

# Directory e Sandbox Operativa
export ANTIGRAVITY_CONFIG_DIR="$HOME/.custom-agy"  # Percorso alternativo alla configurazione globale
export ANTIGRAVITY_LOG_LEVEL="INFO"                # Livello di log: DEBUG, INFO, WARN, ERROR
export ANTIGRAVITY_TELEMETRY="0"                   # Disattiva telemetria, tracciamento e diagnostica remota
export ANTIGRAVITY_SANDBOX_POLICY="strict"         # Livello di isolamento della shell locale
```

### 12.4.2 Gerarchia di Precedenza a 5 Livelli
In caso di definizioni multiple del medesimo parametro, il runtime applica il seguente ordine di risoluzione deterministico:

```text
  ┌─────────────────────────────────────────────────────────────┐
  │  1. FLAG ESPLICITI DA RIGA DI COMANDO                       │ (Massima priorità)
  │     (es. agy --model qwen2.5-coder:32b)                     │
  └──────────────────────────────┬──────────────────────────────┘
                                 │ sovrascrive
  ┌──────────────────────────────▼──────────────────────────────┐
  │  2. VARIABILI D'AMBIENTE DI SISTEMA                         │
  │     (es. export ANTIGRAVITY_MODEL="gemini-2.5-flash")       │
  └──────────────────────────────┬──────────────────────────────┘
                                 │ sovrascrive
  ┌──────────────────────────────▼──────────────────────────────┐
  │  3. CONFIGURAZIONE DEL WORKSPACE LOCALE                     │
  │     (file: .agents/config.json)                             │
  └──────────────────────────────┬──────────────────────────────┘
                                 │ sovrascrive
  ┌──────────────────────────────▼──────────────────────────────┐
  │  4. CONFIGURAZIONE GLOBALE UTENTE                           │
  │     (nella cartella globale ~/.gemini/config/)              │
  └──────────────────────────────┬──────────────────────────────┘
                                 │ sovrascrive
  ┌──────────────────────────────▼──────────────────────────────┐
  │  5. DEFAULT HARDCODED INTEGRATI NEL BINARIO                 │ (Minima priorità)
  │     (es. activeProvider: "google", timeout: 0, turns: 25)   │
  └─────────────────────────────────────────────────────────────┘
```

### 12.4.3 Esempio di Configurazione per Ambienti Headless e Container
In ambienti containerizzati (Docker, Kubernetes runner), è consigliabile configurare l'agente iniettando le variabili tramite il file di ambiente:

```bash
docker run --rm -it \
  -e GEMINI_API_KEY="${CI_SECRET_KEY}" \
  -e ANTIGRAVITY_OFFLINE="0" \
  -e ANTIGRAVITY_LOG_LEVEL="WARN" \
  -e ANTIGRAVITY_TELEMETRY="0" \
  -v "$(pwd)":/workspace \
  -w /workspace \
  antigravity-runner:latest \
  agy "Esegui test di sicurezza e genera report SARIF" --headless -y --format sarif -o /workspace/scan.sarif
```

---

## 12.5 Contratti dei Codici di Uscita Deterministici (Exit Codes)

### 12.5.1 Tabella dei Codici di Ritorno Standardizzati
La CLI adotta una tabella deterministica di codici di ritorno per consentire a script Bash, comandi PowerShell e motori CI/CD di interpretare con precisione la causa di successo o fallimento:

| Codice di Uscita | Costante di Riferimento | Significato e Causa Primaria | Azione Consigliata per la Pipeline / Script |
|---|---|---|---|
| `0` | `EXIT_SUCCESS` | Task completato senza anomalie; asserzioni, verifiche e modifiche applicate con successo. | Procedere allo step successivo del workflow o al merge. |
| `1` | `EXIT_QUALITY_GATE_FAILED` | Fallimento del quality gate: violazioni di stile o lint non sanabili, suite di test rossa o vincoli del prompt non soddisfatti. | Bloccare la pipeline di CI/CD e notificare il team sulla PR. |
| `2` | `EXIT_CLI_SYNTAX_ERROR` | Errore nella riga di comando: flag inesistente, percorso file non valido o sintassi argomenti malformata. | Correggere i parametri di invocazione dello script. |
| `3` | `EXIT_INFERENCE_FAILURE` | Errore del backend di intelligenza artificiale: chiave API non valida, quota esaurita (HTTP 429), endpoint locale non raggiungibile o timeout del modello. | Riprovare (retry) con backoff esponenziale o attivare il provider di fallback. |
| `4` | `EXIT_SECURITY_VETO` | Blocco di sicurezza attivo: un hook `PreToolUse` o una regola di DLP (Data Loss Prevention) ha intercettato un'operazione vietata. | Ispezionare i log di sicurezza (`reports/security_block.log`). |
| `124` | `EXIT_TIMEOUT` | Timeout di esecuzione superato (limite imposto tramite `--timeout`). | Aumentare il valore di `--timeout` o segmentare il prompt. |
| `126` | `EXIT_COMMAND_DENIED` | Permessi del sistema operativo insufficienti per eseguire un comando terminale o manipolare un file protetto. | Verificare i privilegi di esecuzione (`chmod +x` o gruppo utente). |
| `130` | `EXIT_SIGINT` | Interruzione manuale da parte dell'utente o del processo genitore (`Ctrl+C` / segnale SIGINT). | Gestione del rollback o rilascio dei lock di workspace. |

### 12.5.2 Gestione degli Errori e Resilienza nelle Pipeline di Shell
Negli script di produzione, l'intercettazione dell'exit code consente di intraprendere azioni differenziate:

```bash
# Esempio di gestione robusta del codice di ritorno in Bash
agy "Esegui validazione e test" --headless -y --timeout 180s
EXIT_STATUS=$?

case $EXIT_STATUS in
  0)
    echo "Task completato con successo. Procedo al deploy."
    ;;
  1)
    echo "Attenzione: Quality gate non superato. Notifica aperta su Slack."
    exit 1
    ;;
  3)
    echo "Errore inferenza LLM. Tento il fallback su modello locale..."
    agy "Esegui validazione e test" --headless -y --local --model qwen2.5-coder:32b
    ;;
  4)
    echo "ALLARME SICUREZZA: Operazione bloccata dalle policy di sicurezza!"
    exit 1
    ;;
  *)
    echo "Errore inaspettato con codice: $EXIT_STATUS"
    exit $EXIT_STATUS
    ;;
esac
```

---

## 12.6 Formati di Output Strutturati per l'Automazione

### 12.6.1 Plain Text e Rendering a Terminale
Il formato predefinito (`--format text`) produce un output testuale leggibile dall'operatore. Quando richiamato in modalità interattiva, adotta formattazione a colori e indicatori visivi di progresso. In modalità `--quiet`, l'output viene epurato da qualsiasi elemento grafico, restituendo unicamente la risposta sintetica.

### 12.6.2 Output JSON Envelope (`--format json`) con Schema Dettagliato
Ideale per parser automatizzati (Python, `jq`, PowerShell `ConvertFrom-Json`). Fornisce metadati completi sull'esecuzione:

```json
{
  "schema_version": "1.2.0",
  "session_id": "agy-sess-8a71c9b2",
  "status": "success",
  "exit_code": 0,
  "execution_metrics": {
    "duration_ms": 14250,
    "turns_count": 5,
    "model_used": "gemini-2.5-pro",
    "token_usage": {
      "prompt_tokens": 4210,
      "completion_tokens": 780,
      "total_tokens": 4990
    }
  },
  "tool_execution_summary": [
    { "tool": "view_file", "invocations": 3, "status": "ok" },
    { "tool": "replace_file_content", "invocations": 2, "status": "ok" },
    { "tool": "run_command", "invocations": 1, "status": "ok" }
  ],
  "modified_files": [
    "src/middleware/auth_jwt.py",
    "tests/middleware/test_auth_jwt.py"
  ],
  "artifacts": [
    {
      "name": "migration_report",
      "path": "reports/jwt_migration.md",
      "size_bytes": 2840
    }
  ],
  "result_summary": "Migrazione al middleware JWT completata: rimosse dipendenze da sessioni cookie, aggiunta validazione token asimmetrici RS256 e superata la suite con 34 test unitari positivi."
}
```

### 12.6.3 Streaming Eventi NDJSON (`--format stream`) per Monitor Real-Time
Invia riga per riga oggetti JSON conformi allo standard NDJSON (Newline Delimited JSON). Ideale per dashboard o connettori server in tempo reale:

```json
{"event":"turn_start","turn":1,"timestamp":"2026-09-29T14:10:00.102Z"}
{"event":"thought","content":"Analizzo le intestazioni del file di configurazione per identificare la versione del framework."}
{"event":"tool_call","tool":"view_file","args":{"AbsolutePath":"src/config/app.py"}}
{"event":"tool_result","tool":"view_file","bytes":1420,"status":"success"}
{"event":"turn_complete","turn":1,"duration_ms":1650}
{"event":"turn_start","turn":2,"timestamp":"2026-09-29T14:10:01.760Z"}
{"event":"thought","content":"Eseguo la suite di test pytest per convalidare lo stato iniziale."}
{"event":"tool_call","tool":"run_command","args":{"CommandLine":"pytest -q"}}
{"event":"tool_result","tool":"run_command","exit_code":0,"stdout":"12 passed in 1.4s"}
{"event":"completed","exit_code":0,"summary":"Refactoring e test completati con successo."}
```

### 12.6.4 Esportazione SARIF (`--format sarif`) per GitHub Code Scanning
Il formato SARIF (Static Analysis Results Interchange Format, standard OASIS 2.1.0) permette di visualizzare i rilievi di Antigravity direttamente all'interno della sezione *Security -> Code Scanning Alerts* di GitHub o GitLab:

```json
{
  "$schema": "https://raw.githubusercontent.com/oasis-tcs/sarif-spec/master/Schemata/sarif-schema-2.1.0.json",
  "version": "2.1.0",
  "runs": [
    {
      "tool": {
        "driver": {
          "name": "Antigravity Code Guard",
          "version": "2.5.0",
          "rules": [
            {
              "id": "AGY-SEC-001",
              "shortDescription": { "text": "Credenziale sensibile cablata nel sorgente (Hardcoded Secret)" },
              "defaultConfiguration": { "level": "error" }
            }
          ]
        }
      },
      "results": [
        {
          "ruleId": "AGY-SEC-001",
          "message": { "text": "Rilevata chiave privata JWT statica. Trasferire il valore nelle variabili d'ambiente protette." },
          "locations": [
            {
              "physicalLocation": {
                "artifactLocation": { "uri": "src/middleware/auth_jwt.py" },
                "region": { "startLine": 28, "startColumn": 12, "endColumn": 48 }
              }
            }
          ]
        }
      ]
    }
  ]
}
```

---

## 12.7 Workflow Terminali Completi Dual-Language (Bash & PowerShell)

### 12.7.1 Workflow 1: CI/CD Quality Gate & Automated PR Review
Questo scenario convalida il changeset di una Pull Request rispetto al ramo principale (`origin/main`). L'agente verifica la conformità dello stile, esegue i test e genera nativamente il file SARIF per GitHub Security. Se emergono difetti bloccanti, la pipeline fallisce con codice 1.

#### Implementazione Bash (GitHub Actions `.github/workflows/antigravity-gate.yml`) [Usa in Bash]
```yaml
name: Antigravity Automated Quality Gate

on:
  pull_request:
    branches: [main, master]

jobs:
  ai-quality-gate:
    name: AI Architectural Review & Security Scan
    runs-on: ubuntu-latest
    permissions:
      contents: read
      security-events: write
      pull-requests: write

    steps:
      - name: Scarica Codice Sorgente
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Installa Antigravity CLI
        run: |
          curl -fsSL https://antigravity.google/install.sh | bash
          echo "$HOME/.antigravity/bin" >> $GITHUB_PATH

      - name: Esecuzione Revisione Headless e Generazione SARIF
        env:
          GEMINI_API_KEY: ${{ secrets.GEMINI_API_KEY }}
          ANTIGRAVITY_LOG_LEVEL: "INFO"
        run: |
          mkdir -p reports
          echo "Avvio analisi differenziale dei commit con agy..."
          
          agy "Analizza con rigore il diff tra origin/main e HEAD.
               1. Verifica la conformità con le linee guida architetturali.
               2. Esegui la suite di test con pytest.
               3. Identifica potenziali falle di sicurezza, race condition o memory leak.
               Genera il report SARIF in reports/review.sarif.
               Termina con codice 1 se riscontri violazioni o test falliti, altrimenti termina con 0." \
            --headless \
            --non-interactive \
            --diff \
            --commit-range "origin/main..HEAD" \
            --format sarif \
            --output reports/review.sarif \
            --timeout 600s
          
          GATE_EXIT_CODE=$?
          echo "agy terminato con codice: $GATE_EXIT_CODE"
          
          if [ $GATE_EXIT_CODE -ne 0 ]; then
            echo "Quality gate non superato. La PR contiene difetti bloccanti."
            exit $GATE_EXIT_CODE
          fi

      - name: Pubblica Risultati SARIF nella Tab Security
        if: always()
        uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: reports/review.sarif
          category: antigravity-ai-review
```

#### Implementazione PowerShell (Azure DevOps / Windows Runner `Invoke-QualityGate.ps1`) [Usa in PowerShell]
```powershell
<#
.SYNOPSIS
    Script di validazione Quality Gate per pipeline Azure DevOps o runner Windows.
#>
[CmdletBinding()]
param(
    [string]$TargetBranch = "origin/main",
    [string]$ReportPath = "reports\quality_gate.json"
)

$ErrorActionPreference = "Stop"

Write-Host "=== Antigravity Quality Gate (PowerShell Engine) ===" -ForegroundColor Cyan

# 1. Verifica disponibilità CLI o caricamento dal percorso standard
if (-not (Get-Command agy -ErrorAction SilentlyContinue)) {
    Write-Warning "CLI agy non presente nel PATH. Caricamento dal percorso utente..."
    $StandardPath = "$env:USERPROFILE\.gemini\antigravity\bin"
    if (Test-Path "$StandardPath\agentapi.bat") {
        $env:Path += ";$StandardPath"
        Set-Alias -Name agy -Value "$StandardPath\agentapi.bat"
    } else {
        throw "Impossibile individuare il binario di Antigravity CLI nel sistema."
    }
}

# 2. Creazione della directory reports
if (-not (Test-Path "reports")) {
    New-Item -ItemType Directory -Path "reports" | Out-Null
}

# 3. Composizione del prompt di validazione
$PromptText = @"
Analizza il changeset rispetto a $TargetBranch.
Esegui la suite di test 'dotnet test' o 'pytest'.
Verifica che tutte le nuove funzioni abbiano typing rigoroso e documentazione esaustiva.
Compila il report strutturato in formato JSON su $ReportPath.
Se la suite fallisce o riscontri regressioni gravi, restituisci exit code 1.
"@

# 4. Esecuzione headless con monitoraggio dell'exit code
Write-Host "Invocazione agy in modalità headless..." -ForegroundColor Yellow
$process = Start-Process -FilePath "agy" `
    -ArgumentList "`"$PromptText`" --headless -y --diff --commit-range `"$TargetBranch..HEAD`" --format json --output `"$ReportPath`" --timeout 300s" `
    -NoNewWindow -PassThru -Wait

$AgyExitCode = $process.ExitCode
Write-Host "Processo agy terminato. Codice di uscita: $AgyExitCode" -ForegroundColor Gray

# 5. Ispezione dei metadati generati
if (Test-Path $ReportPath) {
    $ReportJson = Get-Content -Raw -Path $ReportPath | ConvertFrom-Json
    Write-Host "Esito: $($ReportJson.result_summary)" -ForegroundColor Green
    Write-Host "Token consumati: $($ReportJson.execution_metrics.token_usage.total_tokens)" -ForegroundColor DarkGray
}

if ($AgyExitCode -ne 0) {
    Write-Error "Quality Gate FALLITO! Codice di errore: $AgyExitCode."
    exit $AgyExitCode
} else {
    Write-Host "Quality Gate SUPERATO con successo!" -ForegroundColor Green
    exit 0
}
```

---

### 12.7.2 Workflow 2: Headless Script Batching per Refactoring Multi-Progetto
Consente di iterare su una moltitudine di microservizi in un monorepo, applicando una migrazione controllata (es. aggiornamento librerie o typing), committando le modifiche riuscite e ripristinando lo stato con `git checkout` in caso di errore.

#### Implementazione Bash (`batch_refactor.sh`) [Usa in Bash]
```bash
#!/usr/bin/env bash
set -eo pipefail

SERVICES_DIR="./services"
LOG_FILE="./migration_summary.csv"
echo "service_name,status,exit_code,duration_seconds" > "$LOG_FILE"

echo "Avvio batch refactoring sui microservizi in $SERVICES_DIR..."

for svc_path in "$SERVICES_DIR"/*; do
  if [ -d "$svc_path" ]; then
    svc_name=$(basename "$svc_path")
    echo "=================================================="
    echo "Elaborazione servizio: $svc_name"
    echo "=================================================="
    
    START_TIME=$(date +%s)
    
    # Invocazione agy con contesto mirato al singolo microservizio
    if agy "Effettua il refactoring di questo servizio per conformarlo a Pydantic v2:
           1. Sostituisci @validator con @field_validator.
           2. Rinomina .dict() in .model_dump().
           3. Esegui la suite di test locale con pytest.
           Se tutti i test passano, conclude con codice 0." \
         --headless \
         --non-interactive \
         --context "$svc_path" \
         --rule ".agents/rules/pydantic_v2_migration.md" \
         --timeout 240s; then
         
      END_TIME=$(date +%s)
      DURATION=$((END_TIME - START_TIME))
      echo "$svc_name,SUCCESS,0,$DURATION" >> "$LOG_FILE"
      
      # Commit atomico del singolo servizio aggiornato
      git add "$svc_path"
      git commit -m "chore($svc_name): automated pydantic v2 migration via agy" || true
      echo "Migrazione completata e committata per $svc_name in ${DURATION}s."
    else
      EXIT_CODE=$?
      END_TIME=$(date +%s)
      DURATION=$((END_TIME - START_TIME))
      echo "$svc_name,FAILED,$EXIT_CODE,$DURATION" >> "$LOG_FILE"
      echo "ERRORE: Migrazione fallita per $svc_name (Codice: $EXIT_CODE). Ripristino modifiche locali..."
      git checkout -- "$svc_path"
    fi
  fi
done

echo "Batch processing concluso. Consulta $LOG_FILE per il dettaglio completo."
```

#### Implementazione PowerShell (`Invoke-BatchRefactor.ps1`) [Usa in PowerShell]
```powershell
<#
.SYNOPSIS
    Esegue un batch refactoring su directory multiple con salvataggio metriche e rollback automatico Git.
#>
[CmdletBinding()]
param(
    [string]$ServicesPath = ".\services",
    [string]$CsvLog = ".\batch_migration_results.csv"
)

$Results = [System.Collections.Generic.List[PSCustomObject]]::new()
$Services = Get-ChildItem -Path $ServicesPath -Directory

Write-Host "Trovati $($Services.Count) servizi da elaborare in $ServicesPath." -ForegroundColor Cyan

foreach ($Service in $Services) {
    Write-Host "`n---> Elaborazione modulo: $($Service.Name)" -ForegroundColor Yellow
    $Stopwatch = [System.Diagnostics.Stopwatch]::StartNew()
    
    $Prompt = @"
Aggiorna tutte le query sincrone in $($Service.FullName) alla sintassi asincrona async/await di SQLAlchemy 2.0.
Verifica ed esegui i test con pytest. Termina con codice 0 solo se la suite è completamente verde.
"@
    
    $Process = Start-Process -FilePath "agy" `
        -ArgumentList "`"$Prompt`" --headless -y --context `"$($Service.FullName)`" --timeout 180s" `
        -NoNewWindow -PassThru -Wait
        
    $Stopwatch.Stop()
    $DurationSec = [Math]::Round($Stopwatch.Elapsed.TotalSeconds, 2)
    
    if ($Process.ExitCode -eq 0) {
        Write-Host "Successo per $($Service.Name) in ${DurationSec}s!" -ForegroundColor Green
        git add $($Service.FullName)
        git commit -m "refactor($($Service.Name)): modernize sqlalchemy to 2.0 via agy" | Out-Null
        
        $Results.Add([PSCustomObject]@{
            ServiceName    = $Service.Name
            Status         = "SUCCESS"
            ExitCode       = 0
            ElapsedSeconds = $DurationSec
        })
    } else {
        Write-Host "Fallimento per $($Service.Name) (ExitCode: $($Process.ExitCode)). Rollback in corso..." -ForegroundColor Red
        git checkout -- $($Service.FullName)
        
        $Results.Add([PSCustomObject]@{
            ServiceName    = $Service.Name
            Status         = "FAILED"
            ExitCode       = $Process.ExitCode
            ElapsedSeconds = $DurationSec
        })
    }
}

$Results | Export-Csv -Path $CsvLog -NoTypeInformation -Encoding utf8
Write-Host "`nProcesso batch terminato. Report generato in $CsvLog" -ForegroundColor Green
```

---

### 12.7.3 Workflow 3: Scaffolding e Inizializzazione Workspace Deterministico
Inizializza una nuova cartella di progetto predisponendo automaticamente la struttura agenziale (`.agents/rules/`, `.agents/workflows/`, `.agents/hooks.json`), configurando le policy di isolamento e validando l'ambiente.

#### Script di Scaffolding Bash (`setup_workspace.sh`) [Usa in Bash]
```bash
#!/usr/bin/env bash
set -e

PROJECT_NAME=$1
if [ -z "$PROJECT_NAME" ]; then
  echo "Uso: $0 <nome-progetto>"
  exit 1
fi

echo "Inizializzazione workspace Antigravity per: $PROJECT_NAME..."
mkdir -p "$PROJECT_NAME"
cd "$PROJECT_NAME"
git init

# Inizializzazione agenziale tramite template
agy init full-stack-web --non-interactive

# Configurazione di una regola restrittiva per il codice sorgente
cat << 'EOF' > .agents/rules/security_guard.md
---
trigger: "always_on"
description: "Blocca credenziali cablate e comandi distruttivi"
---
# Security Guard Rule
1. Non inserire mai API key, token o password direttamente nei sorgenti.
2. Utilizza sempre variabili d'ambiente (.env).
3. Non eseguire comandi di cancellazione ricorsiva sul filesystem.
EOF

echo "Verifica struttura generata con successo:"
ls -la .agents/
ls -la .agents/rules/
echo "Workspace pronto per lo sviluppo aumentato con Antigravity!"
```

#### Script di Scaffolding PowerShell (`Initialize-AntigravityWorkspace.ps1`) [Usa in PowerShell]
```powershell
<#
.SYNOPSIS
    Inizializza un nuovo repository con l'architettura standard delle regole di Antigravity.
#>
param(
    [Parameter(Mandatory=$true)]
    [string]$ProjectName
)

$ErrorActionPreference = "Stop"

Write-Host "Creazione del workspace $ProjectName..." -ForegroundColor Cyan
New-Item -ItemType Directory -Path $ProjectName | Out-Null
Set-Location -Path $ProjectName

git init | Out-Null

# Inizializzazione tramite CLI
agy init python-enterprise --non-interactive

# Scrittura di una regola iniziale di qualità del codice
$RuleContent = @"
---
trigger: "always_on"
description: "Standard di qualità e tipizzazione rigorosa"
---
# Python Quality Standards
1. Tutte le funzioni devono dichiarare type hints completi (mypy compatibili).
2. Ogni modulo pubblico deve includere docstring Google-style.
3. Vietato l'uso di 'Any' non motivato.
"@

Set-Content -Path ".agents\rules\strict_types.md" -Value $RuleContent -Encoding utf8

Write-Host "Workspace inizializzato con successo. Struttura .agents creata." -ForegroundColor Green
Get-ChildItem -Path ".agents" -Recurse | Select-Object FullName
```

---

### 12.7.4 Workflow 4: Pre-Flight System Diagnostics e Monitoraggio Salute
Esegue un controllo sistemistico delle risorse prima di avviare un job notturno o una sessione batch ad alto consumo computazionale, controllando lo stato della GPU, la memoria VRAM, gli endpoint locali Ollama/vLLM e le connessioni MCP.

#### Script Bash di Verifica Salute (`check_system_health.sh`) [Usa in Bash]
```bash
#!/usr/bin/env bash
set -e

echo "Esecuzione pre-flight check del sistema Antigravity..."

# Esegue agy doctor con serializzazione JSON
REPORT=$(agy doctor --format json)

# Estrazione dello stato generale tramite Python
STATUS=$(python3 -c "import sys, json; data=json.loads(sys.argv[1]); print(data.get('status', 'unknown'))" "$REPORT")

if [ "$STATUS" = "healthy" ]; then
  echo "Tutti i sistemi sono operativi: GPU, LLM locale e server MCP pronti."
  exit 0
else
  echo "ATTENZIONE: Rilevate anomalie nell'ambiente di esecuzione!"
  python3 -c "import sys, json; data=json.loads(sys.argv[1]); print('\n'.join(data.get('warnings', [])))" "$REPORT"
  python3 -c "import sys, json; data=json.loads(sys.argv[1]); print('\n'.join(data.get('errors', [])))" "$REPORT"
  exit 1
fi
```

#### Script PowerShell di Verifica Salute (`Test-AntigravityEnvironment.ps1`) [Usa in PowerShell]
```powershell
<#
.SYNOPSIS
    Pre-flight check automatizzato prima del lancio di sessioni batch o carichi intensivi.
#>
$DoctorOutput = agy doctor --format json | ConvertFrom-Json

Write-Host "Verifica stato ambiente Antigravity..." -ForegroundColor Cyan

if ($DoctorOutput.status -ne "healthy") {
    Write-Warning "Rilevate anomalie nell'ambiente:"
    if ($DoctorOutput.warnings) {
        $DoctorOutput.warnings | ForEach-Object { Write-Host " - [AVVISO] $_" -ForegroundColor Yellow }
    }
    if ($DoctorOutput.errors) {
        $DoctorOutput.errors | ForEach-Object { Write-Host " - [ERRORE] $_" -ForegroundColor Red }
        throw "L'ambiente non soddisfa i requisiti minimi per l'esecuzione del task."
    }
} else {
    Write-Host "Tutti i sistemi sono operativi: GPU, VRAM, provider LLM e server MCP online." -ForegroundColor Green
}
```

---

## 12.8 Best Practice Operative per la CLI

1. **Evitare Sovrascritture di Concorrenza:** Quando si eseguono istanze multiple di `agy` in parallelo sulla medesima cartella di lavoro, assicurarsi che ciascun processo operi su file separati o all'interno di worktree Git distinti (`git worktree add`).
2. **Limitare la Dimensione del Contesto:** Evitare l'inclusione generica di directory voluminose con `--context .`. Utilizzare pattern mirati (es. `--context src/**/*.py`) ed escludere cartelle non rilevanti (`node_modules/`, `.venv/`, `target/`).
3. **Utilizzare Sempre il Flag `--timeout` nelle Pipeline CI/CD:** Impedisce che deadlock nei test o risposte prolungate dei modelli blocchino i runner dell'infrastruttura aziendale.
4. **Adottare `--dry-run` durante la Fase di Test dei Prompt:** Permette di convalidare la logica e gli strumenti scelti dall'agente senza rischiare alterazioni involontarie ai sorgenti o al database.
5. **Configurare Politiche di Sandbox Restrittive in Ambienti Condivisi:** Impostare `--network local_only` o la variabile `ANTIGRAVITY_OFFLINE="1"` su runner condivisi per garantire che nessun frammento di codice o dato riservato lasci l'infrastruttura locale.

---

[⬅️ Torna all'Indice](../index.md) | [Capitolo Precedente: 11. Controllo Remoto](./11_remote_control.md)
