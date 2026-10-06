# 9. Server MCP (Model Context Protocol): Integrazione e Strumenti Esterni

Il **Model Context Protocol (MCP)** è lo standard aperto universale per connettere modelli di intelligenza artificiale a sorgenti dati strutturate, strumenti di calcolo esterni e sistemi aziendali. In Antigravity, MCP supera i limiti dei singoli plugin proprietari o delle estensioni ad-hoc: consente all'agente di accedere dinamicamente a database relazionali, file system esterni, repository Git remoti, motori di ricerca scientifici e API proprietarie con un'interfaccia tipizzata, standardizzata e sicura.

Questo capitolo analizza l'architettura tecnica del protocollo, illustra le differenze tra i meccanismi di trasporto e le politiche di caricamento in memoria, fornisce tutorial esecutivi completi per database e servizi cloud, ed elenca le strategie indispensabili di diagnostica e sicurezza operativa.

---

## 9.1 Architettura dello Standard MCP e Paradigma JSON-RPC 2.0

L'integrazione di MCP all'interno del runtime di Antigravity opera secondo una topologia distribuita **Host-Client-Server**, basata sullo standard di messaggistica **JSON-RPC 2.0**.

```
┌────────────────────────────────────────────────────────────────────────┐
│                        ANTIGRAVITY RUNTIME (HOST)                      │
│                                                                        │
│   ┌────────────────────┐                   ┌───────────────────────┐   │
│   │   Large Language   │ ◄── Richieste ────┤  MCP Client Manager   │   │
│   │   Model (Gemini)   │ ──  Risposte ────►│  (Dispatcher Nativo)  │   │
│   └────────────────────┘                   └───────────┬───────────┘   │
└────────────────────────────────────────────────────────┼───────────────┘
                                                         │
                ┌────────────────────────────────────────┴────────────────────────────────────────┐
                ▼ (stdio: stdin/stdout JSON-RPC 2.0)                                              ▼ (SSE: HTTP Stream + POST)
┌───────────────────────────────────────────────┐                                 ┌───────────────────────────────────────────────┐
│              SERVER MCP LOCALE                │                                 │              SERVER MCP REMOTO                │
│ (Processo Child avviato dall'Host)            │                                 │ (Container Docker / Kubernetes / Microservice)│
│                                               │                                 │                                               │
│  - Engine SQLite / Filesystem Scoped          │                                 │  - Cluster PostgreSQL Aziendale               │
│  - Controller Git Locale                      │                                 │  - API GitHub Enterprise / Jira               │
│  - Parser di Documentazione Interna           │                                 │  - Data Lakehouse / BigQuery Gateway          │
└───────────────────────────────────────────────┘                                 └───────────────────────────────────────────────┘
```

### Ruoli nell'Ecosistema
1. **MCP Host**: È l'applicazione che coordina l'esperienza utente e orchestra il modello (in questo caso, Antigravity IDE o Antigravity App 2.0). L'Host avvia i client, negozia i permessi e mantiene lo stato della sessione.
2. **MCP Client**: Il sottosistema interno all'Host che mantiene connessioni persistenti (1-a-1) verso ciascun server registrato, mappando i metodi remoti in capacità richiamabili dall'agente.
3. **MCP Server**: Un programma autonomo (eseguito localmente come processo figlio o remoto su rete HTTP) che espone dati e funzionalità attraverso primitive formali standardizzate.

### I Tre Pilastri Fondamentali del Protocollo
I server MCP possono esporre tre categorie distinte di funzionalità:

* **Tools (Strumenti)**: Funzioni eseguibili con effetti collaterali o computazionali (es. eseguire una query SQL, inviare una Pull Request, compilare un modulo). Ciascun tool è descritto da uno schema `JSON Schema` che ne definisce rigorosamente argomenti, vincoli e tipi di ritorno. L'agente decide autonomamente quando e con quali argomenti invocarli.
* **Resources (Risorse)**: Dati passivi di sola lettura accessibili tramite URI tipizzati (es. `file:///logs/nginx.log`, `postgres://schema/users`, `docs://api/v2`). Possono essere testuali o binari e consentono all'agente di allegare al contesto blocchi di dati mirati senza richiedere l'esecuzione di script.
* **Prompts (Modelli di Prompt)**: Workflow predefiniti e parametrizzati gestiti dal server (es. prompt di code review o analisi incidenti), selezionabili dall'utente per guidare il modello verso un compito specializzato.

### Il Flusso JSON-RPC 2.0
Tutti i messaggi scambiati tra Antigravity e i server MCP viaggiano in formato JSON-RPC 2.0. Un'invocazione di strumento tipica si articola in due passaggi deterministici:

1. **Richiesta dall'Host verso il Server:**
   ```json
   {
     "jsonrpc": "2.0",
     "id": 42,
     "method": "tools/call",
     "params": {
       "name": "query_db",
       "arguments": {
         "sql": "SELECT order_id, total_amount FROM orders WHERE status = 'pending' LIMIT 5;"
       }
     }
   }
   ```

2. **Risposta strutturata dal Server verso l'Host:**
   ```json
   {
     "jsonrpc": "2.0",
     "id": 42,
     "result": {
       "content": [
         {
           "type": "text",
           "text": "[{\"order_id\": 1084, \"total_amount\": 149.50}, {\"order_id\": 1085, \"total_amount\": 82.00}]"
         }
       ],
       "isError": false
     }
   }
   ```

---

## 9.2 Meccanismi di Trasporto & Connettività: stdio vs SSE

MCP standardizza due canali di trasporto indipendenti dalla piattaforma per il transito dei payload JSON-RPC:

| Caratteristica | Trasporto `stdio` | Trasporto `sse` (Server-Sent Events) |
| :--- | :--- | :--- |
| **Meccanismo di Esecuzione** | Sottoprocesso locale generato direttamente da Antigravity (`child_process.spawn`) | Connessione di rete HTTP persistente verso un endpoint indipendente |
| **Flusso Client → Server** | Canale `stdin` del processo figlio (righe JSON terminate da `\n`) | Richiesta HTTP `POST` con corpo contenente il payload JSON-RPC |
| **Flusso Server → Client** | Canale `stdout` del processo figlio (righe JSON terminate da `\n`) | Flusso unidirezionale persistente `GET` su `text/event-stream` |
| **Canale Diagnostico** | Canale `stderr` (intercettato da Antigravity per i log di debug) | Log lato server o endpoint diagnostico separato |
| **Configurazione Rete** | Nessun socket aperto, esecuzione 100% offline | Richiede porta aperta (es. 8000), firewall o reverse proxy |
| **Autenticazione** | Variabili d'ambiente locali (`env`) | Header HTTP (`Authorization: Bearer <token>`, API Keys) |
| **Casi d'Uso Elettivi** | Utility da riga di comando (`uvx`, `npx`), SQLite, script locali | Cluster Kubernetes, database condivisi di team, microservizi |

### Il Vincolo Critico di `stdio`: Nessuna "Stdout Pollution"
Quando si sviluppa o si configura un server MCP basato su `stdio`, **è severamente vietato scrivere su `stdout` qualsiasi output che non sia un messaggio JSON-RPC 2.0 conforme**.

> ⚠️ **Avvertenza Architetturale:**
> Se uno script Python include `print("Inizializzazione completata...")` oppure un pacchetto Node.js stampa un banner informativo via `console.log()`, tale testo finisce direttamente nello stream `stdout`. Il parser JSON di Antigravity fallisce istantaneamente con un errore di tipo `SyntaxError: Unexpected token...` e disconnette il server.
> 
> Tutti i messaggi di debug, avvisi e log devono essere categoricamente reindirizzati verso lo standard error:
> - In Node.js / TypeScript: usa `console.error(...)` anziché `console.log(...)`.
> - In Python: usa `sys.stderr.write(...)` oppure il modulo standard `logging.getLogger()`.

---

## 9.3 Configurazione Gerarchica & Precedenze (`mcp_config.json`)

Antigravity supporta una gerarchia di configurazione su due livelli distinti per consentire la coesistenza di strumenti condivisi a livello di sistema e utility vincolate al singolo repository di codice.

### 1. Livello Globale (Utente)
Il file globale risiede nella cartella di configurazione dell'utente:
* **Windows:** `%USERPROFILE%\.gemini\config\mcp_config.json` (es. `C:\Users\<NomeUtente>\.gemini\config\mcp_config.json`)
* **Linux / macOS:** `$HOME/.gemini/config/mcp_config.json` (es. `~/.gemini/config/mcp_config.json`)

Tutti i server dichiarati in questo file sono disponibili all'agente in qualsiasi workspace e sessione, indipendentemente dal progetto aperto. È la collocazione ideale per strumenti personali o aziendali permanenti (es. GitHub personale, documentazioni globali, server filesystem di utility).

### 2. Livello Workspace (Progetto)
Il file di workspace risiede all'interno della cartella `.agents/` nella radice del repository:
* **Percorso:** `.agents/mcp_config.json` (oppure `.agents/mcp.json`)

Questo file è versionabile con Git ed è destinato ai server strettamente correlati al progetto corrente (es. database SQLite con i mock di test, container Docker associati al backend, server MCP specifici per la build di progetto).

### Regola di Precedenza e Risoluzione Conflitti
Quando Antigravity inizializza la sessione:
1. Carica prima la configurazione globale.
2. Carica la configurazione del workspace.
3. Se un server dichiarato nel workspace ha lo **stesso identificativo univoco** (la chiave dell'oggetto JSON) di un server globale, la definizione del workspace ha la **precedenza assoluta (shadowing totale)**.

```json
{
  "$schema": "https://json.schemastore.org/mcp-config.json",
  "mcpServers": {
    "sqlite-analytics": {
      "command": "uvx",
      "args": ["mcp-server-sqlite", "--db-path", "./data/analytics.db"],
      "env": {
        "SQLITE_BUSY_TIMEOUT": "3000"
      },
      "disabled": false,
      "autoApprove": ["read_query"]
    },
    "enterprise-cluster": {
      "serverUrl": "https://mcp-gateway.corp.internal/sse",
      "headers": {
        "Authorization": "Bearer ${CORP_MCP_TOKEN}",
        "X-Project-ID": "ecommerce-core"
      }
    }
  }
}
```

> 🔒 **Sicurezza e Gestione dei Secret:**
> Non inserire mai token o password in chiaro nei file di configurazione versionati in Git. Utilizza la sintassi di espansione `${NOME_VARIABILE}` oppure inietta i secret attraverso il blocco `"env"` alimentato da file di ambiente locali non tracciati (es. `.env.local` inserito nel `.gitignore`).

---

## 9.4 Politiche di Caricamento: Eager vs Lazy Loading

Ogni tool registrato in un modello di linguaggio consuma spazio prezioso all'interno della **finestra di contesto (context window)**. Un server MCP con decine di funzioni complesse rischia di saturare migliaia di token prima ancora che l'utente scriva il primo prompt.

Per risolvere questo problema, Antigravity implementa due strategie di caricamento governabili con precisione.

```
┌────────────────────────────────────────────────────────────────────────┐
│                        GESTIONE CONTEXT WINDOW                         │
├───────────────────────────────────┬────────────────────────────────────┤
│           EAGER LOADING           │            LAZY LOADING            │
│  Schema completo nel system prompt│    Registrazione di soli meta-tool │
│                                   │                                    │
│  ┌─────────────────────────────┐  │  ┌──────────────────────────────┐  │
│  │ mcp_db_execute_query        │  │  │ call_mcp_tool                │  │
│  │ mcp_db_list_tables          │  │  │ list_resources               │  │
│  │ mcp_db_describe_table       │  │  │ read_resource                │  │
│  │ mcp_db_create_index         │  │  └──────────────────────────────┘  │
│  └─────────────────────────────┘  │                                    │
│  Costo: ~250-500 token per tool   │  Costo fisso: ~60 token totali     │
│  Prontezza: Immediata (1 turno)   │  Prontezza: 2 turni (schema query) │
└───────────────────────────────────┴────────────────────────────────────┘
```

### 1. Eager Loading (Caricamento Anticipato Nativo)
Nel caricamento anticipato, Antigravity interroga il server MCP all'avvio (`tools/list`), analizza l'intero schema JSON di ogni funzione e lo inietta stabilmente nel catalogo degli strumenti nativi dell'agente con il prefisso `mcp_<NomeServer>_<NomeTool>`.

* **Configurazione:** Si attiva specificando `"eager": true` all'interno dell'oggetto del tool o per l'intero server.
* **Vantaggi:** Latenza minima. L'agente riconosce i parametri del tool al primo turno e può invocarlo direttamente.
* **Costo in Token:** Elevato (~200-500 token per ciascun tool attivo).
* **Quando Usarlo:** Per server con un numero ridotto di strumenti primari (1-5 tool) usati in quasi ogni prompt (es. query SQL principali o lettura log).

### 2. Lazy Loading (Caricamento Ritardato tramite Meta-Tool)
Nel caricamento ritardato (comportamento predefinito quando `"eager"` non è specificato per cataloghi estesi), Antigravity non carica le definizioni dei singoli tool nel system prompt. Espone invece tre meta-strumenti generici a bassissimo costo:

* `call_mcp_tool`: Invia una richiesta di esecuzione dinamica indicando il nome del server, il nome del tool e il payload JSON degli argomenti.
* `list_resources`: Restituisce l'elenco delle risorse disponibili senza scaricarne il contenuto.
* `read_resource`: Legge il contenuto testuale o binario di una risorsa specifica a partire dal suo URI.

Antigravity memorizza localmente la cache degli schemi per i server configurati all'interno della directory:
`~/.gemini/antigravity/mcp/<serverName>/`
All'interno di questa cartella sono presenti i file JSON di schema (`<toolName>.json`) e un eventuale file `instructions.md` che documenta all'agente le linee guida d'uso.

> 💡 **Best Practice:**
> Utilizza il caricamento Eager per i server con meno di 5 strumenti critici. Quando colleghi server con API massive (come GitHub, GitLab, OpenAPI generate o Jira che espongono 30-100 tool), lascia attivo il Lazy Loading per risparmiare fino a 20.000 token di contesto.

---

## 9.5 Tutorial 1: Integrazione Database Relazionale (SQLite & PostgreSQL)

> **Ambiente Operativo:** [Usa in Antigravity IDE]

In questo tutorial configuriamo un server MCP reale collegato a un database SQLite locale contenente dati di vendita e-commerce. L'agente ispezionerà lo schema delle tabelle, verificherà gli indici ed eseguirà query analitiche aggregate.

### Step 1: Predisposizione del Database Locale
Apri il terminale integrato in Antigravity IDE e crea un file di database con dati di test.

**Su Linux / macOS (Bash):**
```bash
mkdir -p data
sqlite3 data/ecommerce.db << 'EOF'
CREATE TABLE customers (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    country TEXT NOT NULL,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE orders (
    id INTEGER PRIMARY KEY,
    customer_id INTEGER,
    amount DECIMAL(10,2) NOT NULL,
    status TEXT NOT NULL,
    order_date DATE NOT NULL,
    FOREIGN KEY(customer_id) REFERENCES customers(id)
);

INSERT INTO customers (id, name, country) VALUES
(1, 'Marco Rossi', 'IT'),
(2, 'Elena Bianchi', 'IT'),
(3, 'John Smith', 'US'),
(4, 'Anna Schmidt', 'DE');

INSERT INTO orders (customer_id, amount, status, order_date) VALUES
(1, 150.00, 'completed', '2026-09-01'),
(1, 75.50, 'completed', '2026-09-15'),
(2, 320.00, 'completed', '2026-09-18'),
(3, 45.00, 'refunded', '2026-09-20'),
(4, 210.00, 'completed', '2026-09-25');
EOF
```

**Su Windows (PowerShell):**
```powershell
New-Item -ItemType Directory -Force -Path "data"
# Se sqlite3 non è installato nel PATH, è possibile usare python per creare il database:
python -c "
import sqlite3
con = sqlite3.connect('data/ecommerce.db')
cur = con.cursor()
cur.execute('CREATE TABLE customers (id INTEGER PRIMARY KEY, name TEXT NOT NULL, country TEXT NOT NULL);')
cur.execute('CREATE TABLE orders (id INTEGER PRIMARY KEY, customer_id INTEGER, amount DECIMAL(10,2) NOT NULL, status TEXT NOT NULL, order_date DATE NOT NULL);')
cur.executemany('INSERT INTO customers VALUES (?, ?, ?)', [(1, 'Marco Rossi', 'IT'), (2, 'Elena Bianchi', 'IT'), (3, 'John Smith', 'US'), (4, 'Anna Schmidt', 'DE')])
cur.executemany('INSERT INTO orders VALUES (NULL, ?, ?, ?, ?)', [
    (1, 150.00, 'completed', '2026-09-01'),
    (1, 75.50, 'completed', '2026-09-15'),
    (2, 320.00, 'completed', '2026-09-18'),
    (3, 45.00, 'refunded', '2026-09-20'),
    (4, 210.00, 'completed', '2026-09-25')
])
con.commit()
con.close()
"
```

### Step 2: Configurazione di `.agents/mcp_config.json`
Crea o modifica il file `.agents/mcp_config.json` nella radice del progetto:

```json
{
  "mcpServers": {
    "ecommerce-db": {
      "command": "uvx",
      "args": ["mcp-server-sqlite", "--db-path", "data/ecommerce.db"]
    }
  }
}
```

*(Nota alternativa per PostgreSQL: se utilizzi PostgreSQL, sostituisci il blocco del server con il comando `npx -y @modelcontextprotocol/server-postgres postgresql://readonly_user:password@localhost:5432/nomedb`).*

### Step 3: Riavvio del Client MCP
Salva il file. In Antigravity IDE, apri la Command Palette (`Ctrl+Shift+P` o `Cmd+Shift+P`) ed esegui:
`> Antigravity: Reload MCP Servers`

### Step 4: Interazione Operativa con l'Agente

**🗣️ Tu (Prompt):**
> [Usa in Antigravity IDE]
> Ispeziona il database tramite il server MCP `ecommerce-db`. Mostrami le tabelle presenti, calcola il volume totale delle vendite per gli ordini con stato 'completed' e calcola il carrello medio suddiviso per paese dei clienti.

**🤖 Agente (Risposta):**
> Eseguo l'ispezione delle tabelle del database SQLite tramite lo strumento `mcp_ecommerce-db_list_tables` e `mcp_ecommerce-db_describe_table`.
> 
> Ho individuato le tabelle `customers` e `orders`. Procedo con l'invocazione di `mcp_ecommerce-db_read_query` con la seguente interrogazione SQL:
> 
> ```sql
> SELECT 
>     c.country AS Paese,
>     COUNT(o.id) AS Totale_Ordini,
>     ROUND(AVG(o.amount), 2) AS Carrello_Medio,
>     ROUND(SUM(o.amount), 2) AS Volume_Totale
> FROM customers c
> JOIN orders o ON c.id = o.customer_id
> WHERE o.status = 'completed'
> GROUP BY c.country
> ORDER BY Volume_Totale DESC;
> ```
> 
> **Risultati aggregati estratti dal database:**
> 
> | Paese | Totale Ordini | Carrello Medio (€) | Volume Totale (€) |
> | :--- | :--- | :--- | :--- |
> | **IT** | 3 | 181.83 | 545.50 |
> | **DE** | 1 | 210.00 | 210.00 |
> 
> **Osservazione e Suggerimento:**
> L'ordine numero 4 (cliente `US`, importo 45.00 €) è stato escluso poiché si trova nello stato `refunded`. Sulla tabella `orders`, la colonna `customer_id` non possiede attualmente un indice dedicato: se il volume dei record crescerà, ti consiglio di creare un indice con `CREATE INDEX idx_orders_customer ON orders(customer_id);`.

---

## 9.6 Tutorial 2: Accesso Filesystem Scoped & Automazione GitHub

> **Ambiente Operativo:** [Usa nell'App 2.0]

Spesso l'agente deve operare su porzioni del disco esterne al repository corrente (ad esempio cartelle di log di un server web) oppure deve consultare issue e pull request remote. In questo tutorial configuriamo sia il server di accesso controllato al filesystem sia il server GitHub ufficiale.

### Step 1: Configurazione Globale (`~/.gemini/config/mcp_config.json`)
Apri il file globale sul tuo sistema operativo e definisci i due server:

```json
{
  "mcpServers": {
    "scoped-filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "C:/ServerLogs/nginx",
        "D:/ProgettiCondivisi/assets"
      ]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_PAT_TOKEN}"
      }
    }
  }
}
```

*(Su sistemi Linux/macOS, sostituisci i percorsi con le directory Unix corrispondenti, come `/var/log/nginx` e `/opt/shared/assets`).*

### Step 2: Generazione e Protezione del Token GitHub
1. Accedi a GitHub in **Settings -> Developer Settings -> Personal Access Tokens -> Fine-grained tokens**.
2. Assegna un nome univoco (es. `Antigravity-MCP-Token`) e definisci una scadenza non superiore a 90 giorni.
3. Seleziona i soli repository aziendali necessari (evita l'accesso globale a tutti i repository).
4. Assegna esclusivamente i permessi di **Read** su *Issues* e *Pull Requests*.
5. Esporta la variabile d'ambiente sul tuo sistema operativo prima di avviare l'applicazione:
   - **Linux / macOS:** `export GITHUB_PAT_TOKEN="github_pat_11AB..."`
   - **Windows PowerShell:** `$env:GITHUB_PAT_TOKEN="github_pat_11AB..."`

### Step 3: Interazione Operativa con l'Agente

**🗣️ Tu (Prompt):**
> [Usa nell'App 2.0]
> Analizza il file di log esterno in `C:/ServerLogs/nginx/error.log` tramite `scoped-filesystem`. Estrai gli ultimi tre errori con status HTTP 502, individua l'endpoint coinvolto e poi interroga il repository GitHub `azienda-org/backend-service` per verificare se esiste già una issue aperta relativa a questo errore.

**🤖 Agente (Risposta):**
> Invoco lo strumento `mcp_scoped-filesystem_read_file` per esaminare la parte finale di `C:/ServerLogs/nginx/error.log`.
> 
> Ho individuato tre record critici registrati nelle ultime due ore:
> ```text
> 2026-09-29 11:14:02 [error] 1482#0: *8941 connect() failed (111: Connection refused) while connecting to upstream, client: 192.168.1.45, server: api.azienda.it, request: "POST /v1/checkout/pay HTTP/1.1", upstream: "http://127.0.0.1:5000/v1/checkout/pay"
> ```
> 
> L'endpoint che genera il crash 502 è `/v1/checkout/pay` a causa della disconnessione dell'upstream locale sulla porta 5000.
> 
> Procedo ora a interrogare il repository su GitHub con `mcp_github_search_issues`:
> - Query: `repo:azienda-org/backend-service is:issue is:open checkout pay 502`
> 
> **Risultato della ricerca:**
> Ho trovato la Issue aperta **#87**: *"Intermittent connection refused on payment worker pool under high concurrency"*.
> 
> **Sintesi:**
> L'errore nei log di produzione corrisponde esattamente alla Issue #87 assegnata al team Payments. Il memory leak nel worker pool causa il riavvio del processo sulla porta 5000. Non occorre aprire una nuova issue; suggerisco di aggiungere un commento con i nuovi stack trace alla #87 esistente.

---

## 9.7 Diagnostica, Troubleshooting & Sicurezza

L'integrazione di processi esterni mediante canali IPC e socket di rete può presentare complessità sistemistiche specifiche. La seguente matrice fornisce diagnosi immediate e contromisure verificate.

### 1. Risoluzione dell'Errore Windows `spawn npx ENOENT`
Su piattaforma Windows, comandi standard come `npx` o `uvx` non sono eseguibili binari diretti, bensì script batch con estensione `.cmd` o `.ps1` (es. `npx.cmd`). Se Antigravity tenta di generare il sottoprocesso chiamando semplicemente `npx`, il kernel di Windows solleva l'eccezione `Error: spawn npx ENOENT`.

**Soluzione Consigliata:**
Nel blocco di configurazione, invoca l'interprete dei comandi di Windows `cmd.exe` con il parametro `/c`, oppure specifica l'estensione `.cmd`:

```json
{
  "mcpServers": {
    "filesystem-windows": {
      "command": "cmd.exe",
      "args": ["/c", "npx", "-y", "@modelcontextprotocol/server-filesystem", "C:/Workspace"]
    }
  }
}
```

In alternativa con Python: se `uvx` non viene trovato, specifica il percorso assoluto all'eseguibile:
`"command": "C:\\Users\\VincenzoOriti\\AppData\\Roaming\\uv\\bin\\uvx.exe"`

### 2. Risoluzione della Corruzione dello Stream ("Stdout Pollution")
Se il server MCP all'avvio produce messaggi di testo non formattati (es. banner di benvenuto di librerie, log di terze parti o avvisi deprecati), l'Host visualizzerà il seguente errore:
`JSON-RPC Parse Error: Unexpected token 'W' at position 0`

**Soluzione:**
1. Isola il server eseguendo un wrapper che reindirizzi tutto il rumore di fondo:
   - In Node.js: configura la libreria per loggare solo su `process.stderr`.
   - In Python: assicurati che le chiamate di avvio non stampino a console e che `logging.basicConfig(stream=sys.stderr)` sia configurato correttamente prima di inizializzare l'engine MCP.

### 3. Collaudo Isolato con MCP Inspector
Prima di configurare un server all'interno di Antigravity, puoi testarne il comportamento in un ambiente sandbox privo di interferenze utilizzando l'**MCP Inspector** ufficiale:

```bash
# Collaudo di un server SQLite locale
npx @modelcontextprotocol/inspector uvx mcp-server-sqlite --db-path ./data/ecommerce.db
```

L'inspector avvia un server web diagnostico locale (generalmente accessibile all'indirizzo `http://localhost:5173`). Attraverso la dashboard web interattiva puoi:
* Ispezionare la lista completa dei tools esposti dal server e i rispettivi JSON Schema.
* Testare manualmente l'invocazione di ciascun tool inserendo parametri di prova e leggendo l'output raw in tempo reale.
* Verificare che nessun messaggio spurio inquini il canale `stdout`.

### 4. Governance e Principio del Minimo Privilegio (Least Privilege)
L'esposizione di server MCP richiede una postura di sicurezza rigorosa:

* **Account di Database Dedicati:** Non connettere mai server MCP a database operativi con credenziali di amministrazione (`sa`, `postgres` o `root`). Crea sempre un utente SQL dedicato con permessi di sola lettura:
  ```sql
  CREATE USER mcp_reader WITH PASSWORD 'PasswordSicura123!';
  GRANT CONNECT ON DATABASE ecommerce TO mcp_reader;
  GRANT USAGE ON SCHEMA public TO mcp_reader;
  GRANT SELECT ON ALL TABLES IN SCHEMA public TO mcp_reader;
  ```
* **Restrizione delle Cartelle Filesystem:** Con il server `@modelcontextprotocol/server-filesystem`, dichiara rigorosamente solo i percorsi assoluti delle cartelle da manipolare. Non dichiarare mai le root di unità (come `C:\` o `/`), cartelle di sistema (`/etc`, `C:\Windows`) o directory contenenti chiavi crittografiche (`~/.ssh`).
* **Uso Cauto di `autoApprove`:** La proprietà `"autoApprove": ["read_query"]` consente all'agente di eseguire il tool senza richiedere la conferma manuale dell'utente. Utilizza `autoApprove` **esclusivamente per operazioni di sola lettura privi di effetti collaterali**. Non inserire mai in `autoApprove` comandi di scrittura, cancellazione, commit Git o mutazione dati.

---

[⬅️ Torna all'Indice](../index.md) | [Capitolo Precedente: 8. Guida per gli Sviluppatori](./08_developer_guide.md) | [Prossimo Capitolo: 10. Uso in Locale](./10_local_usage.md)
