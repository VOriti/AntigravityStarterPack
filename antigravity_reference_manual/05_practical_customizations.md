# 5. Personalizzazioni Pratiche: Skills, Hooks e Plugin

Mentre le Regole Markdown (trattate nel [Capitolo 4](./04_rules.md)) forniscono istruzioni cognitive e linee guida di comportamento all'agente, i progetti software reali necessitano di strumenti in grado di compiere **azioni deterministiche di sistema**, guidare **procedure operative complesse a più stadi** e **distribuire pacchetti di automazione riutilizzabili** tra repository diversi.

In questo capitolo esploreremo gli strumenti di personalizzazione avanzata ed esecutiva di Antigravity:
1. **Le Skill**: Runbook procedurali caricati on-demand a basso consumo di token.
2. **I Lifecycle Hooks**: Guardrail reali ed eseguibili di sistema collegati agli eventi chiave del ciclo di vita dell'agente.
3. **I Plugin**: Pacchetti autosufficienti per distribuire e sincronizzare regole, skill e hook all'interno dell'organizzazione.

---

## 5.1 Panoramica degli Strumenti di Personalizzazione

La tabella seguente sintetizza quando e come utilizzare ciascuno strumento in base allo scenario operativo:

| Strumento | Posizione nel File System | Meccanismo | Impatto Token | Quando Utilizzarlo |
|---|---|---|---|---|
| **Regole (Rules)** | `.agents/rules/*.md`, `GEMINI.md` | Istruzioni Markdown statiche nel prompt | Alto (always-on nel contesto) | Standard architetturali, convenzioni di codice, requisiti di stile. |
| **Skill (Runbooks)** | `.agents/skills/<nome>/` | Rivelazione progressiva on-demand | Minimo (~40 token a riposo) | Flussi multi-step complessi: migrazioni database, release, refactoring articolati. |
| **Lifecycle Hooks** | `.agents/hooks.json` + `scripts/` | Script reali di sistema (Python, Bash, Node) | Nullo (eseguiti fuori dall'LLM) | Linter post-modifica, blocco comandi pericolosi, gatekeeper di test su Stop. |
| **Plugin** | `plugins/<nome>/` o `~/.gemini/config/` | Bundle composti di regole, skill e hook | Variabile (somma componenti) | Distribuzione di toolchain aziendali condivise tra molteplici repository. |
| **Server MCP** | `mcp_config.json` | Protocollo client-server Model Context Protocol | Medio (schemi JSON dei tool) | Integrazione con servizi esterni: database SQL, API cloud, repository remoti. |

---

## 5.2 Le Skill Operative: Runbook Multi-Step per l'Agente

Una **Skill** è una Procedura Operativa Standard (SOP) che insegna all'agente come portare a termine un flusso di lavoro complesso e specialistico. A differenza delle regole globali — che occupano costantemente spazio nella finestra di contesto — le skill sfruttano il principio della **Rivelazione Progressiva (Progressive Revelation)**:
- **A riposo (Start-up):** Il runtime espone all'agente esclusivamente il blocco YAML frontmatter (`name` e `description`), consumando soltanto 30-60 token per skill.
- **In esecuzione:** Solo quando l'intento dell'utente richiede l'uso della skill, l'agente carica in memoria il corpo operativo completo (`SKILL.md`), eventuali script ausiliari in `scripts/` e documentazione di supporto in `references/`.

### Anatomia di una Cartella Skill

Ogni skill è organizzata come una cartella autonoma collocata in `.agents/skills/<nome-skill>/` (a livello di progetto) o in `~/.gemini/config/skills/<nome-skill>/` (a livello globale utente):

```
.agents/skills/database-migration-manager/
├── SKILL.md                  # Istruzioni operative principali con frontmatter YAML
├── scripts/                  # Script ausiliari invocabili dall'agente durante il flusso
│   └── check_integrity.py
└── references/               # Documentazione estesa e approfondimenti caricati on-demand
    └── migration_policy.md
```

### Frontmatter YAML e Trigger Semantico ad Alto Segnale

Il file `SKILL.md` deve iniziare tassativamente con un'intestazione delimitata da tre trattini (`---`):

```yaml
---
name: database-migration-manager
description: Esegue migrazioni del database in sicurezza, generando rollback automatici e verificando l'integrità dei dati. Usa questa skill quando l'utente richiede modifiche allo schema del database, creazione di tabelle o migrazioni SQL.
---
```

> 💡 **Formulazione ad Alto Segnale**: Il campo `description` funge da trigger semantico per l'LLM. Non limitarti a spiegare cosa fa la skill; specifica espressamente **quando** l'agente deve attivarla (es. *"Usa questa skill quando l'utente richiede modifiche allo schema del database, creazione di tabelle o migrazioni SQL"*).

### Tutorial Step-by-Step: Creare una Nuova Skill

1. **Crea la directory della skill:**
   ```bash
   mkdir -p .agents/skills/database-migration-manager/scripts
   mkdir -p .agents/skills/database-migration-manager/references
   ```
2. **Definisci la policy operativa in `references/`:**
   Crea `references/migration_policy.md` con i vincoli per tabelle critiche ad alto volume (> 1 milione di righe).
3. **Scrivi il runbook operativo `SKILL.md`:**
   Struttura le fasi numerate in modo sequenziale, con comandi precisi e controlli di rollback.

### Snippet di Codice Pronto all'Uso: `SKILL.md`

Ecco il contenuto completo per `.agents/skills/database-migration-manager/SKILL.md`:

```markdown
---
name: database-migration-manager
description: Esegue migrazioni del database in sicurezza, generando rollback automatici e verificando l'integrità dei dati. Usa questa skill quando l'utente richiede modifiche allo schema del database, creazione di tabelle o migrazioni SQL.
---

# Database Migration Manager

Procedura operativa standard per l'applicazione di migrazioni database relazionali in ambienti di sviluppo e staging.

## Fasi Operative

1. **Analisi dello Schema e Stato Corrente**:
   - Ispeziona lo storico delle migrazioni applicate con la CLI del framework (`alembic current` o `php artisan migrate:status`).
   - Verifica l'assenza di transazioni pendenti o tabelle bloccate.

2. **Scrittura della Migrazione Bidirezionale**:
   - Crea un nuovo file di migrazione con timestamp sequenziale.
   - Definisci tassativamente la funzione di avanzamento (`upgrade`/`up`) e la funzione di annullamento (`downgrade`/`down`).
   - **Regola Zero-Data-Loss**: Non rimuovere colonne direttamente; applica deprecazione a due fasi o colonne nullable.

3. **Verifica Locale Dry-Run**:
   - Applica la migrazione su un database di test: `alembic upgrade head`
   - Esegui immediatamente il rollback per validare la simmetria: `alembic downgrade -1`
   - Riapplica `head` ed esegui la test suite di regressione.
   - Per policy di gestione su tabelle con oltre 1 milione di righe, consulta [references/migration_policy.md](./references/migration_policy.md).
```

### Come Invocare la Skill
Puoi attivare la skill in due modalità:
1. **Attivazione Naturale (Semantica):** Chiedi all'agente: *"Dobbiamo aggiungere la colonna last_login alla tabella users"*. L'agente riconosce la corrispondenza con la `description` della skill e carica il runbook.
2. **Invocazione Esplicita con Slash Command:** Utilizza lo slash command dedicato `/database-migration-manager` se registrato nell'ambiente.

---

## 5.3 Lifecycle Hooks: Guardrail e Automazioni con Script Reali

I **Lifecycle Hooks** sono punti di estensione deterministici che intercettano le fasi chiave dell'esecuzione dell'agente. A differenza delle regole Markdown — che rappresentano vincoli probabilistici per l'LLM — gli hook eseguono **reali processi di sistema** (Python, comandi shell, script Node) sul computer locale.

### I 5 Eventi del Ciclo di Vita

L'agente attraversa un ciclo di esecuzione ben definito rappresentato dal seguente diagramma:

```
[Inizio] ──► [PreInvocation] ──► [Generazione LLM] ──► [PostInvocation]
                                                             │
                  ┌──────────────────────────────────────────┴───────────────┐
                  ▼                                                          ▼
             [Tool Call?] ──(Sì)──► [PreToolUse] ──► [Esegui] ──► [PostToolUse] ──► (Loop)
                  │
                (No)
                  ▼
               [Stop] ──► [Fine]
```

1. **`PreToolUse`**: Scatta **prima** che l'agente esegua un tool (es. un comando da terminale). Può autorizzare (`"decision": "allow"`), bloccare (`"decision": "deny"`), richiedere conferma interattiva all'utente (`"decision": "ask"`) o modificare gli argomenti al volo (`"overwriteArgs"`).
2. **`PostToolUse`**: Scatta **subito dopo** il completamento con successo di un tool. Perfetto per lanciare formattatori automatici o linter (`ruff format`, `prettier`) dopo che un file è stato scritto o modificato.
3. **`PreInvocation`**: Scatta **prima** che il prompt venga inviato all'LLM. Consente di iniettare dinamicamente messaggi effimeri di sistema (`ephemeralMessage`) o dati contestuali in tempo reale.
4. **`PostInvocation`**: Scatta **dopo** che il modello ha generato la risposta o ha proposto chiamate a tool, prima dell'esecuzione.
5. **`Stop`**: Scatta quando l'agente dichiara di aver terminato il task e desidera chiudere la sessione. Se la test suite o i controlli di integrità falliscono, l'hook può negare l'arresto (`"decision": "continue"`) obbligando l'agente a risolvere le regressioni.

### Il Contratto Protojson (stdin / stdout)

Il runtime di Antigravity comunica con gli script hook attraverso gli stream standard di input e output:
- **Input (`sys.stdin`):** Il runtime invia un payload JSON serializzato con chiavi in formato **camelCase** (es. `toolCall`, `args`, `terminationReason`).
- **Output (`sys.stdout`):** Lo script deve restituire **esclusivamente** un oggetto JSON valido contenente la decisione presa (es. `{"decision": "allow"}` o `{"decision": "ask", "reason": "..."}`).
- **Working Directory:** La directory di lavoro corrente (CWD) durante l'esecuzione dell'hook coincide con la cartella che ospita il file `hooks.json`.
- **Stream di Diagnostica (`stderr`):** Eventuali stampe di debug devono essere indirizzate rigorosamente su `sys.stderr`. Se un testo non-JSON viene stampato su `stdout`, il parser fallirà e scatterà il fallback di emergenza.

---

### Configurazione Master: `.agents/hooks.json`

Salva il file di configurazione master in `.agents/hooks.json` (per il repository) oppure in `~/.gemini/config/hooks.json` (per la macchina globale):

```json
{
  "safety-firewall": {
    "enabled": true,
    "PreToolUse": [
      {
        "matcher": "run_command",
        "hooks": [{ "type": "command", "command": "python scripts/security_guard.py", "timeout": 15 }]
      }
    ]
  },
  "code-quality-gate": {
    "enabled": true,
    "PostToolUse": [
      {
        "matcher": "replace_file_content|write_to_file",
        "hooks": [{ "type": "command", "command": "python scripts/post_edit_lint.py", "timeout": 20 }]
      }
    ]
  },
  "sprint-context-injector": {
    "enabled": true,
    "PreInvocation": [{ "type": "command", "command": "python scripts/sprint_context.py", "timeout": 10 }]
  },
  "test-completion-gate": {
    "enabled": true,
    "Stop": [{ "type": "command", "command": "python scripts/stop_guard.py", "timeout": 30 }]
  }
}
```

---

### Le 4 Ricette Operative (Script Python Completi)

Tutti gli script seguenti devono essere posizionati all'interno della cartella `scripts/` del workspace di progetto.

#### Ricetta 1: Guardia di Sicurezza Pre-Tool (`scripts/security_guard.py`)
Intercetta le chiamate a `run_command` ed esamina la stringa di comando alla ricerca di pattern distruttivi. In caso di corrispondenza, blocca l'esecuzione immediata e richiede l'approvazione esplicita dell'utente:

```python
#!/usr/bin/env python3
"""PreToolUse Hook: Blocca pattern distruttivi prima dell'esecuzione."""
import sys, json, re

DANGEROUS_PATTERNS = [
    r"\brm\s+-(?:r|f|rf|fr)\s+[/~]",   # rm -rf su root o home
    r"\bDROP\s+(?:DATABASE|TABLE)\b",  # SQL DROP distruttivi
    r"\bgit\s+reset\s+--hard\b",       # git reset distruttivo
    r"\bgit\s+push\s+.*--force\b",     # git force push
    r"\bmkfs\b",                       # formattazione filesystem
    r"\bdd\s+if=",                     # scrittura raw su blocchi disco
]

def main():
    try:
        raw_input = sys.stdin.read()
        if not raw_input.strip():
            print(json.dumps({"decision": "allow"}))
            return
        payload = json.loads(raw_input)
        tool_call = payload.get("toolCall", {})
        if tool_call.get("name") == "run_command":
            cmd = tool_call.get("args", {}).get("CommandLine", "")
            for pattern in DANGEROUS_PATTERNS:
                if re.search(pattern, cmd, re.IGNORECASE):
                    response = {
                        "decision": "ask",
                        "reason": f"Comando ad alto rischio rilevato: '{cmd}'. Richiesta approvazione esplicita."
                    }
                    print(json.dumps(response))
                    return
        print(json.dumps({"decision": "allow"}))
    except Exception as e:
        print(json.dumps({"decision": "ask", "reason": f"Fallback sicurezza hook: {str(e)}"}))

if __name__ == "__main__":
    main()
```

#### Ricetta 2: Auto-Linting Post-Tool (`scripts/post_edit_lint.py`)
Intercetta le modifiche ai file (`replace_file_content` o `write_to_file`) ed esegue silenziosamente `ruff format .` per formattare istantaneamente il codice modificato:

```python
#!/usr/bin/env python3
"""PostToolUse Hook: Esegue auto-formatting/linting dopo ogni edit."""
import sys, json, subprocess

def main():
    try:
        raw = sys.stdin.read()
        if raw.strip():
            payload = json.loads(raw)
            if payload.get("error"):
                print(json.dumps({}))
                return
        subprocess.run(["ruff", "format", "."], stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL, check=False)
    except Exception:
        pass
    print(json.dumps({}))

if __name__ == "__main__":
    main()
```

#### Ricetta 3: Iniezione Contesto Pre-Invocation (`scripts/sprint_context.py`)
Inietta un promemoria effimero di sistema prima di ogni generazione dell'LLM per ricordare i requisiti di qualità:

```python
#!/usr/bin/env python3
"""PreInvocation Hook: Inietta istruzioni effimere nel contesto prima del modello."""
import sys, json

def main():
    response = {
        "injectSteps": [
            {
                "ephemeralMessage": "[SISTEMA]: Verifica rigorosa: assicurati che la test suite passi prima di dichiarare completato il task."
            }
        ]
    }
    print(json.dumps(response))

if __name__ == "__main__":
    main()
```

#### Ricetta 4: Stop Gatekeeper per Test Automatici (`scripts/stop_guard.py`)
Impedisce all'agente di terminare prematuramente la sessione se i test di regressione falliscono:

```python
#!/usr/bin/env python3
"""Stop Hook: Blocca l'arresto dell'agente se i test di regressione falliscono."""
import sys, json, subprocess

def main():
    try:
        raw = sys.stdin.read()
        payload = json.loads(raw) if raw.strip() else {}
        if payload.get("terminationReason") != "model_stop":
            print(json.dumps({"decision": "allow"}))
            return
        result = subprocess.run(["pytest", "-q"], stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True)
        if result.returncode != 0:
            preview = (result.stdout or result.stderr)[:600]
            response = {
                "decision": "continue",
                "reason": f"La suite di test automatizzati fallisce. Risolvi le regressioni prima di concludere:\n{preview}"
            }
            print(json.dumps(response))
            return
        print(json.dumps({"decision": "allow"}))
    except Exception:
        print(json.dumps({"decision": "allow"}))

if __name__ == "__main__":
    main()
```

### Come Collaudare un Hook dal Terminale
Puoi verificare il funzionamento di uno script hook prima di attivarlo, simulando il payload JSON via pipe da terminale:

**Su Linux / macOS:**
```bash
echo '{"toolCall": {"name": "run_command", "args": {"CommandLine": "rm -rf /tmp/test"}}}' | python scripts/security_guard.py
```

**Su Windows (PowerShell):**
```powershell
'{"toolCall": {"name": "run_command", "args": {"CommandLine": "rm -rf /tmp/test"}}}' | python scripts/security_guard.py
```

**Risultato atteso:**
```json
{"decision": "ask", "reason": "Comando ad alto rischio rilevato: 'rm -rf /tmp/test'. Richiesta approvazione esplicita."}
```

---

## 5.4 I Plugin: Pacchettizzare e Condividere Personalizzazioni

I **Plugin** sono bundle autosufficienti che raccolgono regole, skill, hook del ciclo di vita e configurazioni di server MCP per facilitarne la distribuzione e il riutilizzo in team complessi.

### Struttura di un Plugin e File `plugin.json`

Ciascun plugin è racchiuso in una directory dedicata con la seguente struttura:

```
plugins/security-pack/
├── plugin.json               # Manifest dei metadati e capacità del plugin
├── rules/                    # Regole fornite dal pacchetto
│   └── owasp_top10.md
├── skills/                   # Skill specializzate incluse
│   └── vulnerability-scanner/
│       └── SKILL.md
└── hooks.json                # Hook esecutivi di sicurezza
```

Il manifest `plugin.json` dichiara l'identità del plugin e le funzionalità attivate:

```json
{
  "name": "security-pack",
  "version": "1.2.0",
  "description": "Suite di sicurezza: regole OWASP, scanner vulnerabilità e hook di controllo",
  "author": "SecOps Team",
  "capabilities": {
    "rules": true,
    "skills": true,
    "hooks": true
  }
}
```

### Attivazione dei Plugin

Per attivare un plugin installato a livello utente, abilitalo nel file master `~/.gemini/config/config.json`:

```json
{
  "plugins": {
    "security-pack": { "enabled": true },
    "experimental-profiler": { "enabled": false }
  }
}
```

### Manifest di Condivisione: `skills.json` e `plugins.json`

Nelle organizzazioni multi-repository, i manifest dichiarativi `skills.json` (o `plugins.json`) consentono di condividere e sincronizzare personalizzazioni tra molteplici progetti senza duplicare codice:

```json
{
  "inherits": [
    {
      "path": "/shared/corporate-toolchain/skills.json",
      "include_only": ["security-audit", "docker-deploy"],
      "exclude": ["deprecated-.*"]
    }
  ],
  "entries": [
    { "path": "tools/internal-skills", "exclude": ["experimental-.*"] },
    { "path": "~/.gemini/custom-skills" }
  ]
}
```

- **`inherits`**: Importa configurazioni da share di rete, sottosottomoduli Git o percorsi condivisi dell'organizzazione.
- **`include_only` / `exclude`**: Filtri basati su espressioni regolari per includere o escludere selettivamente determinate skill o plugin.
- **`entries`**: Registra percorsi di directory locali aggiuntivi da sottoporre a scansione al boot.

---

## 5.5 Checklist di Troubleshooting per Hooks, Skills e Plugin

Se un componente personalizzato non risponde come previsto, verifica i punti seguenti:

### 1. L'Hook non Viene Eseguito
- [ ] **Disponibilità dell'Interprete nel PATH**: L'eseguibile specificato nel comando (`python`, `python3`, `node`) è presente e raggiungibile nelle variabili d'ambiente di sistema?
- [ ] **Corrispondenza del Matcher Regex**: Nel file `hooks.json`, la regex del campo `matcher` corrisponde esattamente al nome del tool invocato (es. `run_command` o `replace_file_content|write_to_file`)?
- [ ] **Timeout Esecutivo**: Se l'operazione richiede più tempo del valore `timeout` specificato in secondi, Antigravity interrompe il processo e scatta il fallback.
- [ ] **Inquinamento dello Stream stdout**: Lo script ha stampato messaggi di debug o log su `stdout` anziché su `stderr`? *Regola d'oro: su stdout deve transitare solo ed esclusivamente il JSON di risposta finale.*
- [ ] **Proprietà `"enabled"`**: Nel blocco dell'hook all'interno di `hooks.json`, il flag `"enabled"` è impostato su `true`?

### 2. La Skill non si Attiva
- [ ] **Qualità del Trigger Semantico (`description`)**: La `description` nel frontmatter YAML è sufficientemente descrittiva? Includi espressioni esplicite come *"Usa questa skill quando l'utente richiede..."*.
- [ ] **Validità del Frontmatter YAML**: Il blocco iniziale inizia e finisce con `---` senza righe vuote precedenti?

### 3. Il Plugin Risulta Inattivo
- [ ] **Stato di Abilitazione in `config.json`**: Controlla la sezione `"plugins"` del file `~/.gemini/config/config.json`. Se il plugin è marcato con `"enabled": false`, viene ignorato dal runtime.
- [ ] **Integrità del Manifest**: Il file `plugin.json` contiene sintassi JSON valida ed elenca correttamente le cartelle esistenti (`rules`, `skills`, `hooks.json`)?

---

[⬅️ Torna all'Indice](../index.md) | [Capitolo Precedente: 4. Regole (Rules)](./04_rules.md) | [Prossimo Capitolo: 6. Architettura Avanzata](./06_advanced_architecture.md)
