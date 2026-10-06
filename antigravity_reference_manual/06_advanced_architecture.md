# 6. [AVANZATO] Architettura del Sistema, Context Window e Configurazione Globale

L'ecosistema di personalizzazione di Antigravity non è un semplice aggregatore di istruzioni testuali, bensì un'infrastruttura deterministica multi-livello progettata per orchestrare, vincolare e governare il runtime di intelligenza artificiale. Per padroneggiare scenari enterprise complessi, pipeline CI/CD distribuite e ambienti multi-repository, è indispensabile comprendere l'architettura profonda del motore: come la memoria di lavoro dell'LLM (la **Context Window**) elabora le informazioni, quali vincoli matematici e cognitivi ne regolano l'efficienza e come la **configurazione globale di macchina** interagisce con i repository di progetto.

Questo capitolo costituisce il riferimento architetturale e teorico completo di Antigravity, fornendo le equazioni, i protocolli e i modelli a stati che governano l'intero sistema.

---

## 6.1 Architettura Globale del Sistema di Personalizzazione

L'ecosistema di personalizzazione di Antigravity opera attraverso una federazione su tre livelli gerarchici, ciascuno dotato di scopi precisi di governance, persistenza e ciclo di vita:

1. **Livello Workspace (`.agents/` o directory di progetto)**:
   - Risiede all'interno del repository di codice (`.agents/rules/`, `.agents/hooks.json`, `.agents/skills/`, `AGENTS.md`).
   - È sottoposto a controllo di versione (VCS / Git) e condiviso obbligatoriamente tra tutti i membri del team.
   - Definisce gli standard architetturali, i contratti API, i gatekeeper di qualità e le automazioni esecutive vincolanti per il progetto.
2. **Livello Globale (`~/.gemini/config/`)**:
   - Risiede nella home directory dell'utente sulla workstation locale (`$HOME/.gemini/config/` su Unix/macOS, `%USERPROFILE%\.gemini\config\` su Windows).
   - È completamente isolato da Git e definisce le preferenze personali dello sviluppatore (convenzioni di stile, preferenze linguistiche, token API e credenziali private, server MCP globali e firewall di sicurezza personali).
   - Si applica trasversalmente a qualsiasi cartella o repository aperto sulla macchina.
3. **Livello Integrato (Built-in)**:
   - Fornito di fabbrica all'interno del runtime (`.gemini/antigravity/builtin/`).
   - Implementa le capacità primitive di base: tool di manipolazione file, orchestratore di subagenti, terminal execution layer e workflow di base.

---

### 6.1.1 Matrice Comparativa delle Personalizzazioni

La seguente matrice confronta in modo esaustivo le caratteristiche dimensionali dei cinque strumenti di estensione di Antigravity:

| Meccanismo | Posizione Tipica | Ambito (Scope) | Timing di Caricamento | Impatto sui Token | Casi d'Uso Primari |
|---|---|---|---|---|---|
| **Regole (Rules)** | `.agents/rules/*.md`, `GEMINI.md` | Globale o Directory Scoped | Always-On (a ogni interazione) | Alto (testo iniettato o puntatore) | Coding standards, architettura, divieti di sicurezza, vincoli API |
| **Skill (Runbooks)** | `skills/<nome>/SKILL.md` | Globale o Workspace | Rivelazione Progressiva (on-demand) | Minimo (~40 token fino all'uso) | Procedure multi-step, migrazioni DB, refactoring complessi |
| **Lifecycle Hooks** | `hooks.json` (Workspace/Globale) | Workflow & Tool Calls | Esecuzione deterministica ad eventi | Nullo (eseguiti fuori dal contesto LLM) | Linting automatico, validazione sicurezza, gatekeeper dei test |
| **Plugin** | `plugins/<nome>/plugin.json` | Distribuibile / Condiviso | Registrazione all'avvio | Variabile (somma delle risorse) | Pacchettizzazione di regole, skill e hook riutilizzabili |
| **MCP Servers** | `mcp_config.json` | Macchina o Workspace | Schemi registrati all'avvio | Medio (schema JSON dei tool) | Connessione a database, cloud provider, issue tracker |

---

### 6.1.2 Ordine di Precedenza e Risoluzione dei Conflitti (Precedence Cascade)

In presenza di direttive omonime, risorse concorrenti o impostazioni sovrapposte, il runtime applica un ordine di valutazione a 5 livelli rigorosamente gerarchico:

```
┌─────────────────────────────────────────────────────────────┐
│ 1. Workspace Progetto (scoperto da CWD risalendo alla root) │  [PRIORITÀ MASSIMA]
├─────────────────────────────────────────────────────────────┤
│ 2. Workspace Dichiarato (manifest skills.json / plugins.json)│
├─────────────────────────────────────────────────────────────┤
│ 3. Configurazione Globale (~/.gemini/config/)               │
├─────────────────────────────────────────────────────────────┤
│ 4. Personalizzazioni Built-in (distribuite con l'IDE)        │
├─────────────────────────────────────────────────────────────┤
│ 5. Globale Dichiarato (~/.gemini/config/skills.json)        │  [PRIORITÀ MINIMA]
└─────────────────────────────────────────────────────────────┘
```

#### Meccanismi di Risoluzione Conflitti:
- **Shadowing delle Regole**: Una regola definita a livello di workspace in `.agents/rules/coding_standards.md` sovrascrive qualsiasi indicazione confliggente specificata a livello globale in `~/.gemini/config/GEMINI.md`. Il contesto di progetto è sempre considerato più autorevole delle preferenze generiche dell'utente.
- **Override delle Skill**: Se una skill locale all'interno di `.agents/skills/` possiede il medesimo attributo `name` di una skill fornita a livello globale o built-in, la versione di workspace prende il controllo completo dell'esecuzione.
- **Cumulo degli Hook**: A differenza delle regole e delle skill, i Lifecycle Hook registrati a livello globale e a livello workspace vengono eseguiti entrambi sequenzialmente in pipeline, garantendo che i controlli di sicurezza globali di macchina non possano essere bypassati dal codice del repository.

---

## 6.2 La Finestra di Contesto (Context Window): Teoria e Meccanica Interna

La **Context Window** (finestra di contesto) rappresenta lo spazio computazionale a breve termine a disposizione del Large Language Model (LLM) durante ogni singola invocazione di inferenza. A differenza dello storage persistente su disco, la finestra di contesto racchiude la totalità dei dati che il modello può ponderare e correlare simultaneamente:
- System prompt e istruzioni interne del modello.
- Schemi JSON dei tool e delle integrazioni MCP.
- Regole Markdown attive (inlined o referenziate).
- Metadati delle skill scoperte.
- Cronologia della conversazione e turni precedenti.
- Frammenti di codice e file letti tramite comandi di esplorazione.

---

### 6.2.1 Teoria dell'Attenzione e Degrado Cognitivo ("Lost in the Middle")

Tutti i modelli allo stato dell'arte basati su architettura Transformer calcolano le correlazioni contestuali tra token mediante il meccanismo di **Self-Attention scalato**:

$$\text{Attention}(Q, K, V) = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right)V$$

dove $Q$ (Query), $K$ (Key) e $V$ (Value) sono le proiezioni lineari dei token della sequenza e $d_k$ è la dimensione dei vettori chiave.

Nonostante le moderne architetture supportino finestre di contesto da centinaia di migliaia o milioni di token, la capacità di allocare pesi di attenzione uniformi sull'intera sequenza decresce in modo non lineare. 

La letteratura accademica (*Liu et al., "Lost in the Middle: How Language Models Use Long Contexts"*) evidenzia che gli LLM manifestano una marcata asimmetria attentiva:
- **Effetto Primacy**: Altissima accuratezza e fedeltà nel recepire le istruzioni situate all'inizio della sequenza (es. System Prompt e Regole di Apertura).
- **Effetto Recency**: Altissima precisione nel rispondere all'ultimo messaggio o richiesta operativa collocata alla fine della finestra.
- **Fenomeno "Lost in the Middle"**: Marcata degradazione della densità attentiva verso le informazioni collocate nella parte centrale della finestra.

```
Attenzione
   ▲
1.0│  █████                                            █████  (Primacy & Recency)
   │  █████                                            █████
0.5│  █████      ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░       █████
   │  █████      ░░ "LOST IN THE MIDDLE" ░░░░░       █████  (Degrado centrale)
0.0└──┴──────────┴─────────────────────────────┴───────┴──────►
     Inizio (System Prompt)                  Fine (Ultimo Prompt) Posizione Token
```

#### Implicazioni sulla Token Economics
Mantenere costantemente satura la finestra di contesto comporta impatti severi sulle prestazioni e sull'accuratezza:
1. **Latenza del Time-to-First-Token (TTFT)**: Il tempo necessario a calcolare il prefisso di attenzione ($O(N^2)$ o $O(N)$ con caching KV) aumenta linearmente o quadraticamente al crescere dei token accumulati.
2. **Rule Dilution (Interferenza Semantica)**: L'accumulo disordinato di centinaia di righe di regole crea interferenze lessicali, aumentando esponenzialmente la probabilità che vincoli architetturali critici vengano trascurati o contraddetti.
3. **Rischio di Allucinazione**: Maggiore è il rumore di fondo nel contesto, più elevata è la probabilità che il modello estrapoli collegamenti non sussistenti tra token non correlati.

---

### 6.2.2 I 5 Meccanismi Deterministici di Protezione del Contesto

Per proteggere il modello dal degrado cognitivo ed evitare che la gestione delle personalizzazioni esaurisca la memoria operativa, Antigravity implementa cinque meccanismi architetturali deterministici:

1. **Budget Dedicato per le Regole (`defaultRulesBudget` = 20.000 Token)**:
   - Antigravity assegna un tetto massimo invalicabile di **20.000 token** riservato esclusivamente alle Always-On Rules.
   - Questo budget è computazionalmente isolato dallo spazio dedicato a schemi tool MCP, metadati skill e cronologia conversazionale, impedendo che regole ipertrofiche cannibalizzino la memoria necessaria per analizzare codice voluminoso.
2. **Limite Fisico per Singolo File (24 KB) con Troncamento a Confini di Riga**:
   - Ogni singolo file `.md` di regola ha un limite rigido di **24.000 byte (24 KB)**.
   - In caso di superamento, il motore tronca deterministicamente il contenuto sull'ultimo confine di riga valido precedente i 24 KB, prevenendo la corruzione di blocchi di codice Markdown o frammenti sintattici parziali.
3. **Meccanismo di Degradazione a Puntatori (Demotion to File Path Pointers)**:
   - Quando la somma di tutte le regole scoperte nel workspace eccede il budget di 20.000 token, Antigravity le ordina in base alla prossimità gerarchica al file attivo.
   - Le regole a più alta priorità rimangono inlined nel prompt. Le regole a priorità inferiore vengono compresse nella dicitura sintetica `[Rule available at /path/to/rule.md]`. L'agente ne conosce l'esistenza e potrà leggerne il corpo tramite `view_file` solo se e quando strettamente necessario.
4. **Rivelazione Progressiva (Progressive Revelation)**:
   - Le Skill caricano all'avvio esclusivamente il blocco YAML frontmatter (`name` e `description`), consumando soltanto 30-60 token ciascuna.
   - Il corpo dettagliato del runbook (`SKILL.md`), gli script e la documentazione in `references/` rimangono su disco fino al momento in cui l'intento dell'utente non attiva la skill.
5. **Deduplicazione per Percorsi Canonici (`realpath`)**:
   - Il motore risolve ciascun percorso tramite risoluzione canonica dell'inode (`realpath`), impedendo che link simbolici, percorsi relativi multipli o inclusioni circolari provochino l'iniezione duplicata della medesima risorsa nel contesto.

---

### 6.2.3 Best Practice di Igiene del Contesto

Per garantire massime prestazioni e minimizzare le allucinazioni:
- **Gestione del Ciclo di Vita delle Sessioni**: Conclusa una macro-attività o completato un refactoring, apri una nuova chat per azzerare lo storico e ripristinare il contesto a zero rumore.
- **Menzioni Mirate (`@`)**: Invece di chiedere all'agente di scansionare l'intero progetto, cita file specifici (`@src/core/auth.py`, `@tests/test_auth.py`) per caricare esclusivamente i dati rilevanti.
- **Regole Concettualmente Dense**: Formula regole assertive, basate su checklist ed esempi "DO / DON'T", evitando preamboli discorsivi e testi prolissi.

---

## 6.3 La Configurazione Globale (`~/.gemini/config/`): Ruolo di Sistema e Anatomia

Mentre la directory `.agents/` all'interno di un repository governa le specificità del progetto ed è condivisa con il team via Git, la configurazione globale risiede nel profilo dell'utente della macchina ospitante:
- **Sistemi POSIX (Linux/macOS):** `~/.gemini/config/` (espanso a `$HOME/.gemini/config/`)
- **Sistemi Windows:** `%USERPROFILE%\.gemini\config\` (es. `C:\Users\<Username>\.gemini\config\`)

### Ruolo e Separazione dei Piani

| Caratteristica | Livello Workspace (`<repo>/.agents/`) | Livello Globale (`~/.gemini/config/`) |
|---|---|---|
| **Ambito di validità** | Esclusivo del repository corrente | Trasversale a qualsiasi cartella o progetto aperto |
| **Controllo Versione (Git)** | Committato nel repository del team | Locale alla macchina, escluso da Git |
| **Dati tipici** | Standard architetturali, linter di progetto, hook CI | API token personali, preferenze di editor, firewall globale |
| **Condivisione** | Obbligatoria per tutti i collaboratori | Personale per il singolo sviluppatore |

> 🔒 **Sicurezza e Riservatezza**: Non committare mai file provenienti da `~/.gemini/config/` in repository pubblici. Questa directory custodisce credenziali private, configurazioni di server MCP locali con segreti incorporati e policy di sicurezza personali dell'utente.

---

### 6.3.1 Anatomia Completa del Filesystem di `~/.gemini/config/`

La struttura interna completa della configurazione globale è organizzata come segue:

```
~/.gemini/config/
├── config.json              # Configurazione master runtime, policy di sicurezza e toggle plugin
├── mcp_config.json          # Registro globale dei server Model Context Protocol (MCP)
├── GEMINI.md                # Regola globale utente (applicata a qualsiasi progetto)
├── AGENTS.md                # Regola globale alternativa compatibile
├── hooks.json               # Lifecycle hooks globali di sicurezza e monitoraggio
├── skills.json              # Manifest globale di importazione skill esterne
├── plugins.json             # Manifest globale di importazione plugin esterni
├── plugins/                 # Directory dei plugin installati a livello utente
│   └── <plugin-name>/
│       ├── plugin.json
│       ├── skills/
│       └── rules/
└── projects/                # Registro interno dei workspace e metadati sessioni
    └── <project-hash>.json
```

---

### 6.3.2 Master Application Settings: `config.json`

Il file `config.json` governa il runtime globale di Antigravity, definendo le policy di sicurezza di default e l'attivazione dei plugin:

```json
{
  "userSettings": {
    "autoExecutionPolicy": "ask_for_approval",
    "enableTerminalSandbox": true,
    "nonWorkspaceFileAccessPolicy": "deny",
    "remoteControlEnabled": false,
    "themeMode": "dark"
  },
  "plugins": {
    "enterprise-security": { "enabled": true },
    "experimental-profiler": { "enabled": false }
  }
}
```

#### Documentazione dei Parametri Master:
- **`autoExecutionPolicy`**: Determina il livello di autonomia nell'esecuzione dei comandi da terminale:
  - `"always_ask"`: Richiede l'autorizzazione manuale dell'utente prima di qualsiasi comando shell.
  - `"ask_for_approval"`: Richiede conferma per operazioni che modificano lo stato del sistema o comandi potenzialmente pericolosi.
  - `"trusted_auto"`: Consente l'esecuzione immediata di comandi non distruttivi in ambienti completamente sandboxati.
- **`enableTerminalSandbox`**: Abilita la gabbia di isolamento per i processi generati dalla shell, impedendo accessi a risorse al di fuori delle cartelle consentite.
- **`nonWorkspaceFileAccessPolicy`**: Regola l'accesso a percorsi del filesystem esterni alla directory di lavoro attiva:
  - `"deny"`: Blocca la lettura e la scrittura al di fuori del repository aperto.
  - `"ask"`: Richiede conferma esplicita prima di aprire file esterni.
  - `"allow"`: Permette l'esplorazione trasversale del disco.
- **`remoteControlEnabled`**: Abilita o disabilita il controllo remoto della sessione tramite socket sicuri o protocolli web.
- **`themeMode`**: Preferenza visiva dell'interfaccia (`"dark"`, `"light"`, `"system"`).
- **`plugins`**: Mappa chiave-valore che consente di abilitare (`"enabled": true`) o disabilitare selettivamente i plugin installati.

---

### 6.3.3 Registro Globale Server MCP: `mcp_config.json`

Il file `mcp_config.json` dichiara i server Model Context Protocol accessibili globalmente da tutti i progetti:

```json
{
  "mcpServers": {
    "gemini-docs": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-gemini-docs"],
      "env": { "DOCS_LOCALE": "it" }
    },
    "local-postgres": {
      "command": "docker",
      "args": ["exec", "-i", "dev-postgres", "mcp-pg-server"],
      "env": { "DATABASE_URL": "postgresql://postgres:secret@localhost:5432/main" }
    }
  }
}
```

Per l'approfondimento dettagliato sull'integrazione di server MCP personalizzati, transport stdio/SSE e tool creation, consulta il [Capitolo 9: Configurazione Server MCP](./09_mcp_servers.md).

---

## 6.4 Il Contratto Protojson e la Macchina a Stati dei Lifecycle Hooks

I Lifecycle Hook operano come una macchina a stati finiti deterministica sincrona integrata nel loop di esecuzione di Antigravity.

### Diagramma a Stati del Ciclo di Vita

```
           [Inizio Task]
                 │
                 ▼
          [PreInvocation] ─────────────► (Iniezione ephemeralMessage)
                 │
                 ▼
         [Generazione LLM]
                 │
                 ▼
         [PostInvocation] ─────────────► (Ispezione output / continuazione)
                 │
     ┌───────────┴───────────┐
     ▼                       ▼
[Tool Call Proposta]   [Nessun Tool / Stop]
     │                       │
     ▼                       ▼
 [PreToolUse]              [Stop] ─────► (Gatekeeper test suite)
  (allow / deny / ask)       │
     │                 ┌─────┴─────┐
     ▼                 ▼           ▼
  [Esegui Tool]    [Continue]   [Allow]
     │                 │           │
     ▼                 ▼           ▼
 [PostToolUse]    (Riprendi)    [Fine Task]
     │
     ▼
 (Torna a PreInvocation)
```

---

### Specifica del Contratto Protojson

La comunicazione tra il runtime di Antigravity e i processi hook avviene tramite pipe standard di input e output serializzate in JSON:

#### 1. Payload in Ingresso (`stdin`)
Il runtime serializza un oggetto JSON con chiavi in formato **camelCase**:

```json
{
  "event": "PreToolUse",
  "toolCall": {
    "name": "run_command",
    "args": {
      "CommandLine": "pytest -q",
      "Cwd": "/workspace/project"
    }
  },
  "terminationReason": "model_stop",
  "timestamp": "2026-09-29T10:00:00Z"
}
```

#### 2. Payload in Uscita Richiesto (`stdout`)
Lo script deve stampare su `stdout` una stringa JSON valida che esprime la decisione:

```json
{
  "decision": "allow"
}
```

Opzioni ammesse per `decision` e parametri opzionali:
- `"decision": "allow"`: Consente l'azione senza modifiche.
- `"decision": "deny"`: Blocca immediatamente l'esecuzione dell'azione.
- `"decision": "ask"`: Interrompe l'agente e presenta all'utente una richiesta di conferma interattiva con messaggio esplicativo (`"reason": "..."`).
- `"decision": "continue"`: (Specifico per l'evento `Stop`) Rifiuta la chiusura del task e rimanda l'agente al lavoro con una motivazione (`"reason": "..."`).
- `"overwriteArgs"`: Modifica in modo trasparente gli argomenti della chiamata tool prima dell'esecuzione.
- `"injectSteps"` / `"ephemeralMessage"`: (Specifico per `PreInvocation`) Inietta messaggi volatili nel contesto dell'agente prima dell'inferenza.

#### 3. Isolamento dello Stream di Diagnostica (`stderr`)
Qualsiasi informazione diagnostica, stack trace o log di debug generato dallo script deve essere inviato su **`stderr`** (`sys.stderr` in Python). Il runtime di Antigravity ignora `stderr` durante il parsing del payload protojson, prevenendo crash o fallimenti dovuti a stampe accidentali.

#### 4. Wrapping del Processo di Esecuzione
I comandi definiti negli hook con `"type": "command"` vengono avviati dal runtime mediante wrapper di sistema nativo:
- Ambienti POSIX (Linux/macOS): `sh -c "<command>"`
- Ambienti Windows: `cmd /c "<command>"`

La directory di lavoro corrente (CWD) del processo figlio coincide sempre con la directory in cui risiede il file `hooks.json`.

---

## 6.5 Troubleshooting Sistemistico e Architetturale

In caso di anomalie avanzate o discrepanze tra configurazioni multiple:

### 1. Diagnosi dei Conflitti di Precedenza
Se una direttiva non sembra produrre effetti:
- Controlla la gerarchia della cascata di precedenza: un file `AGENTS.md` o `.agents/rules/*.md` locale ha sempre la priorità rispetto a `~/.gemini/config/GEMINI.md`.
- Assicurati che non vi siano file manifest `skills.json` di workspace che escludono (`"exclude": [...]`) la risorsa invocata.

### 2. Ispezione dei Log per Regole Degradate a Puntatori
Se l'agente conosce l'esistenza di una regola ma non ne segue i dettagli senza esplicita richiesta:
- Verifica se la dimensione aggregata delle regole attive supera la soglia di 20.000 token.
- Consulta i log del runtime cercando stringhe del tipo: `[Rule available at /path/to/rule.md]`. Questo indica che la regola è stata declassata a puntatore per sforamento del budget.
- Soluzione: Archivia le regole storiche non più necessarie e mantieni ogni file al di sotto dei 24 KB.

### 3. Violazioni di Sandbox e Policy di Accesso
Se un tool di lettura/scrittura restituisce errori di accesso negato:
- Ispeziona `~/.gemini/config/config.json` e verifica `nonWorkspaceFileAccessPolicy`. Se impostato su `"deny"`, l'accesso a file esterni alla root del workspace viene terminato con errore di sicurezza.
- Verifica `enableTerminalSandbox`: se attivo, comandi shell che tentano di scrivere in directory di sistema o fuori dalla sandbox di processo vengono intercettati e terminati dal kernel di sicurezza.

---

[⬅️ Torna all'Indice](../index.md) | [Capitolo Precedente: 5. Personalizzazioni Pratiche](./05_practical_customizations.md) | [Prossimo Capitolo: 7. Guida per i Ricercatori](./07_researcher_guide.md)
