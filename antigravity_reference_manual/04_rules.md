# 4. Regole (Rules): Sintassi, Scoping e Guida Pratica

Le **Regole (Rules)** costituiscono le fondamenta operative del comportamento dell'agente in Antigravity. A differenza dei normali prompt inviati nella chat — che hanno natura conversazionale ed effimera — le regole sono **direttive persistenti ("always-on")** scritte in formato Markdown standard. Vengono caricate automaticamente nel contesto cognitivo dell'agente a ogni singolo turno di interazione, garantendo la stretta conformità agli standard architetturali, alle convenzioni stilistiche, alle policy di sicurezza e ai flussi operativi dell'organizzazione.

In questa guida pratica imparerai come funzionano le regole, dove posizionarle per controllarne l'ambito di visibilità (**Directory Walk-Up Scoping**), come modularizzarle tramite inclusioni direttive (`@[Label](path)`), come rispettare i limiti fisici del runtime e come redigere regole efficaci attraverso un catalogo di template pronti all'uso e un tutorial passo-passo.

---

## 4.1 Cosa sono le Regole e Perché Usarle

Nello sviluppo quotidiano assistito da intelligenza artificiale, ripetere manualmente gli standard di progetto (convenzioni di commit, architettura delle cartelle, framework di test, divieto di cancellazione file critici) genera fatica cognitiva, inefficienze e derive stilistiche. Le regole automatizzano questo allineamento trasformando i vincoli umani in guardrail cognitivi permanenti per l'agente.

### La Triade Operativa: Prompt vs Regole vs Skill

Per utilizzare Antigravity al massimo potenziale, è essenziale distinguere con precisione i tre livelli di istruzione a disposizione:

| Meccanismo | Natura e Persistenza | Timing di Caricamento | Scopo Tipico |
|---|---|---|---|
| **Prompt (Chat)** | Effimero (valido nella sessione attiva) | A ogni invio manuale dell'utente | Descrizione dell'intento immediato (es. *"Refattorizza la funzione di login"*). |
| **Regole (Rules)** | Persistente ("always-on", file Markdown) | Automatico a ogni turno di generazione | Standard trasversali, vincoli architetturali, divieti di sicurezza, stili di codice. |
| **Skill (Runbooks)** | On-demand (procedurale a più fasi) | Rivelazione progressiva su trigger semantico | Flussi complessi eseguiti raramente (es. migrazioni di database, rilascio release). |

### Quando Scrivere una Regola

Crea o aggiorna un file di regole quando intendi:
- Imporre un'architettura rigorosa (es. Clean Architecture, Domain-Driven Design, separazione frontend/backend).
- Definire convenzioni di codice non negoziabili (es. tipizzazione rigorosa in Python/TypeScript, formattazione con Ruff/Prettier).
- Enfatizzare policy di testing (es. copertura minima >= 85%, obbligo di test unitari con pytest/vitest).
- Istituire barriere di sicurezza e privacy (es. conformità GDPR/HIPAA, divieto di commit di credenziali, obbligo di anonimizzazione).
- Controllare l'interazione con la shell (es. divieto di comandi distruttivi `rm -rf`, `DROP TABLE`, `git reset --hard`).

---

## 4.2 Dove Posizionare le Regole: Scoping e Gerarchia Pratica

Antigravity adotta una struttura a strati gerarchici che consente di modulare la portata delle regole, da direttive globali per l'intera macchina fino a vincoli circoscritti a una singola sottocartella di controller API.

```
Macchina Locale (Home Utente)
 └── ~/.gemini/config/GEMINI.md           [1. Regola Globale Macchina]
      │
      ▼
Repository / Workspace Git
 ├── .agents/rules/coding_standards.md   [2. Regole di Progetto Workspace]
 ├── AGENTS.md (oppure root GEMINI.md)   [2. Regola Master Workspace]
 ├── frontend/
 │    └── GEMINI.md                      [3. Regola Modulo Frontend]
 └── src/api/
      └── GEMINI.md                      [3. Regola Modulo Backend API]
```

### 1. Livello Globale Macchina (`~/.gemini/config/GEMINI.md`)
- **Percorso POSIX (Linux/macOS):** `~/.gemini/config/GEMINI.md` o `$HOME/.gemini/config/GEMINI.md`
- **Percorso Windows:** `%USERPROFILE%\.gemini\config\GEMINI.md` (es. `C:\Users\<Username>\.gemini\config\GEMINI.md`)
- **Ambito:** Si applica trasversalmente a **qualsiasi cartella o progetto** aperto con Antigravity sulla tua postazione.
- **Uso consigliato:** Preferenze linguistiche (es. rispondere sempre in italiano), divieti tassativi su comandi shell distruttivi, preferenze di formattazione personali dell'utente.

### 2. Livello di Progetto Workspace (`.agents/rules/` e Root Progetto)
- **Percorso:** `.agents/rules/*.md`, root `GEMINI.md` o `AGENTS.md`
- **Ambito:** Valido per l'intero repository corrente. È sottoposto a versionamento Git e condiviso con tutti i collaboratori del team.
- **Uso consigliato:** Linee guida architetturali aziendali, convenzioni di commit (Conventional Commits), target di coverage dei test, divieto di commit di segreti.

### 3. Livello Directory-Scoped (Scoping Locale)
- **Percorso:** Qualsiasi file denominato `GEMINI.md` o `AGENTS.md` all'interno di una sotto-cartella specifica (es. `src/api/GEMINI.md`, `packages/ui/GEMINI.md`, `tests/GEMINI.md`).
- **Ambito:** Si applica esclusivamente quando l'agente analizza, genera o modifica file situati in quella cartella o nelle sue sotto-cartelle.
- **Uso consigliato:** Specificità tecnologiche verticali (es. regole Pydantic e contratti HTTP per l'API REST, standard Tailwind CSS per i componenti UI).

### Il Meccanismo di Directory Walk-Up Scoping

Quando chiedi all'agente di operare su un file sorgente (ad esempio `src/api/v1/auth/login.py`), il runtime di Antigravity calcola l'insieme attivo delle regole eseguendo una risalita gerarchica dell'albero delle directory (**Directory Walk-Up**):

1. **Cartella immediata:** Controlla `src/api/v1/auth/` cercando `GEMINI.md` o `AGENTS.md`.
2. **Cartella padre:** Risale a `src/api/v1/` e accumula le regole ivi presenti.
3. **Cartella modulo:** Risale a `src/api/` e aggrega le regole specifiche dell'API.
4. **Cartella sorgente e root:** Risale a `src/` e alla root del workspace (`.agents/rules/*.md`, root `GEMINI.md` o `AGENTS.md`).
5. **Configurazione globale utente:** Concatena infine le regole della tua home (`~/.gemini/config/GEMINI.md`).

> 🛡️ **Isolamento Modulare Garantito**: Grazie al Directory Walk-Up Scoping, una regola presente in `frontend/GEMINI.md` non verrà **mai** caricata nel contesto quando l'agente lavora su `src/api/v1/auth/login.py`. Questo previene interferenze semantiche e protegge la finestra di contesto.

---

## 4.3 Sintassi, Stile e Modularità delle Regole

Una regola efficace deve essere formulata in modo deterministico, assertivo e sintetico. Gli LLM rispondono con estrema aderenza a istruzioni scritte con linguaggio imperativo e prive di ambiguità.

### Best Practice di Scrittura: Il Linguaggio Prescrittivo

- **Usa verbi imperativi e marcatori forti:** Utilizza parole in maiuscolo per enfatizzare i vincoli assoluti: **OBBLIGATORIO**, **MAI**, **SEMPRE**, **PREFERISCI**, **NON**.
- **Fornisci esempi "DO / DON'T":** Un breve blocco di codice con la pratica vietata e quella corretta vale più di tre paragrafi di testo astratto.
- **Struttura a elenchi puntati e checklist:** Facilita la scansione rapida da parte dei meccanismi di attenzione del modello.
- **Elimina preamboli e convenevoli:** Evita frasi come *"Sarebbe gradito se tenessi a mente che..."*. Scrivi direttamente: *"Usa esclusivamente annotazioni di tipo moderne (Python 3.12+)."*

### Inclusioni Modulari Direttive: `@[Label](path)`

Per evitare file monolitici e difficili da manutenere, Antigravity supporta la composizione modulare delle regole tramite la direttiva speciale:

```markdown
@[Nome Modulo](percorso/relativo/file.md)
```

- **Risoluzione percorsi:** I percorsi relativi sono risolti rispetto alla posizione del file che include la direttiva.
- **Espansione inline:** In fase di caricamento, il runtime intercetta la direttiva ed espande il testo del file referenziato all'interno del contesto dell'agente.
- **Deduplicazione automatica:** Se lo stesso file viene incluso più volte o scoperto sia via Walk-Up che via direttiva, Antigravity risolve il percorso canonico (`realpath`) ed evita duplicazioni.

### Limiti Fisici Operativi del Runtime

La finestra di contesto è una risorsa finita. Per preservarne l'integrità, Antigravity impone due vincoli rigorosi di cui tenere sempre conto:

1. **Limite Fisico per Singolo File (24 KB):**
   - Ogni singolo file `.md` non può superare i **24.000 byte (24 KB)**.
   - Se un file supera i 24 KB, il runtime tronca deterministicamente il contenuto sull'ultimo confine di riga valido precedente la soglia. La parte restante viene ignorata.
   - *Soluzione pratica:* Se una regola diventa troppo estesa, spezzala in più file specializzati e uniscili usando `@[Label](path)`.
2. **Budget Aggregato per le Regole (`defaultRulesBudget` = 20.000 Token):**
   - Lo spazio totale riservato all'insieme di tutte le regole caricate è di **20.000 token**.
   - Se la somma di tutte le regole scoperte nel workspace supera i 20.000 token, scatta il meccanismo automatico di **Degradazione a Puntatori (Demotion to File Path Pointers)**: le regole a priorità inferiore non vengono iniettate interamente nel prompt, ma compresse nella dicitura sintetica `[Rule available at /path/to/rule.md]`.
   - *Soluzione pratica:* Mantieni le regole dense, sintetiche e circoscritte agli argomenti indispensabili.

---

## 4.4 Catalogo di Regole Pronte all'Uso (Snippet Reali e Pronti all'Uso)

Di seguito trovi sei snippet completi, reali e pronti per essere copiati e adattati al tuo ambiente.

### 4.4.1 Snippet 1: Regola Globale Utente (`~/.gemini/config/GEMINI.md`)

Posiziona questo file nella tua home utente (`~/.gemini/config/GEMINI.md` su Unix/macOS oppure `%USERPROFILE%\.gemini\config\GEMINI.md` su Windows) per impostare il tono e le direttive di sicurezza personali su tutti i progetti:

```markdown
# Global User Guidelines & Safety Directives

You are operating on my primary workstation. Always adhere to these global standards across all projects:

## Language & Communication
- Respond in Italian for technical explanations, architecture summaries, and PR drafts, unless repository docs are strictly English-only.
- Keep responses dense, direct, and free of conversational filler. Prioritize runnable code and actionable diagnostics.

## Safety & Terminal Discipline
- **NEVER** run destructive terminal commands (`rm -rf /`, `git reset --hard`, `DROP DATABASE`, `mkfs`) without explicit confirmation.
- Prefer non-interactive flags for CLI commands (e.g. `npm install --no-fund --quiet`, `pytest -q`).
- Do NOT leave background processes or file watchers hanging indefinitely.

## Code Standards
- Prefer strict typing in TypeScript (`strict: true`) and modern type hints in Python 3.12+.
- Favor composition over deep inheritance; avoid monolithic god-classes.
```

### 4.4.2 Snippet 2: Regola di Progetto Workspace (`.agents/rules/coding_standards.md`)

Salva questo file nel repository in `.agents/rules/coding_standards.md` per condividere con tutto il team i requisiti architetturali e di qualità del codice:

```markdown
# Workspace Architectural & Quality Standards

## Architecture & Separation of Concerns
- This project enforces Clean/Hexagonal Architecture.
- The Domain layer (`core/domain/`) must remain pure: zero framework dependencies, zero ORM annotations, zero HTTP imports.
- Infrastructure adapters (`adapters/`) must implement domain ports interfaces strictly.

## Testing & Quality Gates
- Every new feature or bugfix must be accompanied by automated unit or integration tests.
- Run tests via `pytest tests/ -v --cov=src` (target coverage: >= 85%).
- Do not mark tasks as complete if any existing test fails.

## Git & Version Control
- All commit messages must follow the Conventional Commits specification:
  - `feat(<scope>): <short description>`
  - `fix(<scope>): <short description>`
  - `refactor(<scope>): <short description>`
- Never commit secrets, `.env` files, or local build artifacts (`dist/`, `.venv/`).
```

### 4.4.3 Snippet 3: Regola con Scoping Locale (`src/api/GEMINI.md`)

Crea questo file in `src/api/GEMINI.md` per vincolare esclusivamente la progettazione dei controller e delle rotte REST:

````markdown
# REST API Module Standards (Local Scope: src/api/)

These rules apply strictly to all controllers and endpoints inside `src/api/` and child directories:

1. **Input Validation**: Every incoming HTTP request payload must be parsed and validated against a Pydantic v2 model before passing data to services.
2. **Standard Response Envelope**: All JSON responses must follow this structure:
   ```json
   {
     "success": true,
     "data": {},
     "meta": { "timestamp": "ISO-8601", "requestId": "UUID" }
   }
   ```
3. **Error Boundaries**: Never expose raw database stack traces or SQL errors to API consumers. Catch internal exceptions and translate them into typed `HTTPException` with explicit 4xx or 5xx status codes.
````

### 4.4.4 Snippet 4: Regola Modulare Master (`AGENTS.md`)

Usa questo file come punto d'ingresso principale nella radice del repository, importando sotto-regole specializzate:

```markdown
# Master Project Architecture Rules

@[Database Guidelines](.agents/rules/database.md)
@[Security & Authentication](.agents/rules/security.md)
@[CI/CD & Deployment](.agents/rules/cicd.md)

## Operational Directives
- Follow the modular sub-rules imported above.
- Always execute local verification commands before proposing code changes.
```

### 4.4.5 Snippet 5: Regola di Privacy & Riservatezza Dati (`.agents/rules/privacy_and_security.md`)

Salva questo file in `.agents/rules/privacy_and_security.md` per garantire conformità a standard GDPR, HIPAA e policy aziendali di non-divulgazione dati:

```markdown
# Privacy, Data Protection & Security Guidelines

## 1. Data Protection & Compliance (GDPR / HIPAA)
- **ZERO PII Exposure**: NEVER print, log, or transmit Personally Identifiable Information (names, tax IDs, email addresses, phone numbers, medical records).
- **Synthetic Data**: When writing unit tests or documentation fixtures, ALWAYS use synthetic anonymized data generators (e.g., Faker) with explicitly fake domains (`@example.com`).
- **Data Isolation**: Never copy real database dumps into local development workspaces or unencrypted temporary folders.

## 2. Secrets & Credential Quarantine
- **NEVER Hardcode Secrets**: API tokens, private keys, database passwords, and client secrets must be loaded strictly from environment variables (`os.environ`) or secret managers.
- **Git Hygiene**: Prevent `.env`, `.pem`, `.key`, and credential caches from ever being staged or committed. Verify `.gitignore` before proposing commits.
- **Pre-execution Redaction**: When displaying terminal command outputs, sanitize and mask any authorization headers (`Bearer *******`) or connection strings containing passwords.

## 3. Local Model Boundary for Sensitive Tasks
- For operations involving proprietary datasets or clinical data, ensure the agent delegates computation strictly to local pipelines or offline containerized environments without external cloud telemetry.
```

### 4.4.6 Snippet 6: Regola di Stile, Linting e Formattazione (`.agents/rules/style_and_lint.md`)

Salva questo file in `.agents/rules/style_and_lint.md` per assicurare che il codice prodotto sia stilisticamente uniforme ed esente da violazioni statiche:

```markdown
# Code Style, Formatting & Static Analysis Standards

## 1. Automated Formatting & Linters
- **Python Code**:
  - Enforce formatting and linting via Ruff: `ruff check --fix .` and `ruff format .`.
  - Target modern Python (version 3.12+): use built-in generics (`list[str]`, `dict[str, Any]`) instead of `typing.List` / `typing.Dict`.
  - All public functions, methods, and classes MUST include clear Google-style docstrings and complete type signatures.
- **JavaScript / TypeScript / Frontend**:
  - Format with Prettier using 2-space indentation and single quotes.
  - Strict TypeScript mode is mandatory: avoid `any`; use `unknown` with type guards or discriminated unions.

## 2. Refactoring Discipline
- Do not introduce unrelated cosmetic refactoring in files not directly involved in the user task.
- Preserve existing comments and docstrings unless they become factually incorrect due to logic changes.
- Never disable lint rules inline (`# noqa`, `/* eslint-disable */`) without documenting the exact technical justification.
```

---

## 4.5 Tutorial Pratico: Creare e Testare la Tua Prima Regola Step-by-Step

Dopo aver esaminato le Best Practice di scrittura e il linguaggio prescrittivo nella sezione 4.3, in questo tutorial realizzeremo da zero una regola di progetto per imporre la tipizzazione statica e la documentazione obbligatoria in Python, collaudandone poi l'efficacia con l'agente.

### Step 1: Identificare il Vincolo Ricorrente
Spesso l'agente propone funzioni Python prive di type hints (es. `def calculate_total(items, tax):`) o prive di docstring esplicative. Vogliamo istruire l'agente a produrre **esclusivamente** funzioni completamente tipizzate e documentate secondo lo standard Google Style.

### Step 2: Creare la Cartella e il File della Regola
All'interno del workspace di progetto, crea la directory `.agents/rules/` (se non esiste già) e genera il file `type_safety.md`:

```bash
mkdir -p .agents/rules
touch .agents/rules/type_safety.md
```

### Step 3: Redigere la Regola con Sintassi Prescrittiva
Apri `.agents/rules/type_safety.md` e inserisci il seguente contenuto:

```markdown
# Python Type Safety & Documentation Rule

## Strict Typing Mandate
- Every function and method MUST specify explicit type hints for all parameters and the return value.
- NEVER use untyped signatures such as `def process(data):`. Use `def process(data: dict[str, Any]) -> ProcessResult:`.
- For optional values, use modern union syntax (`str | None`), not `Optional[str]`.

## Docstring Standard
- All non-trivial functions must include a Google-style docstring detailing `Args:`, `Returns:`, and `Raises:` (if applicable).
```

Collega la regola al file `AGENTS.md` nella radice del progetto per attivarla:

```markdown
# Project Master Directives

@[Python Type Safety](.agents/rules/type_safety.md)
```

### Step 4: Collaudare l'Applicazione della Regola
Invia ora un prompt volutamente aperto all'agente nella chat:

> **Prompt:** *"Scrivi una funzione Python che calcola lo sconto su un carrello di prodotti."*

**Verifica del Risultato Atteso:**
L'agente non genererà una funzione informale senza tipi. Invece, applicherà determinatamente la regola `type_safety.md`:
- Definirà dataclass o tipi per i prodotti (es. `list[CartItem]`).
- Applicherà type hints completi (`def calculate_discount(items: list[CartItem], discount_rate: float) -> Decimal:`).
- Includerà la docstring conforme a Google Style con le sezioni `Args:` e `Returns:`.

---

## 4.6 Checklist Operativa di Troubleshooting per le Regole

Se noti che l'agente sembra disattendere una regola o non applicarla in modo coerente, esegui questa verifica diagnostica:

| # | Punto di Controllo | Sintomo Tipico | Azione Correttiva |
|---|---|---|---|
| 1 | **Verifica del Directory Walk-Up** | La regola è definita in `frontend/GEMINI.md`, ma stai modificando un file in `backend/api/`. | Sposta la regola in una cartella antenata comune (es. root `GEMINI.md` o `.agents/rules/`) oppure crea un file specifico nel percorso del modulo backend. |
| 2 | **Limite 24 KB per Singolo File** | L'agente rispetta la prima metà del file di regola, ma ignora completamente le sezioni finali. | Ispeziona la dimensione del file `.md`: se supera i 24.000 byte, il runtime ha tagliato le righe eccedenti. Suddividi il documento in più file ed esegui l'inclusione modulare con `@[Label](path)`. |
| 3 | **Budget Complessivo di 20.000 Token** | Nei log o nel comportamento si osserva che regole importanti vengono ignorate o declassate. | Il totale delle regole caricate supera i 20k token: Antigravity le trasforma in puntatori sintetici `[Rule available at /path]`. Elimina istruzioni ridondanti, asciuga le descrizioni e rimuovi testi non prescrittivi. |
| 4 | **Conflitto tra Livelli di Precedenza** | Una direttiva definita in `~/.gemini/config/GEMINI.md` viene ignorata all'interno del progetto. | La regola di Workspace (`.agents/` o `AGENTS.md`) ha precedenza assoluta sul livello Globale. Verifica che nel repository non sia presente una direttiva opposta che sovrascrive quella globale. |
| 5 | **Sintassi di Inclusione Modulare Errata** | La direttiva `@[Label](path)` appare come testo piano e il file collegato non viene caricato. | Controlla la sintassi esatta: chiocciola, parentesi quadre per il titolo, parentesi tonde per il percorso relativo corretto (`@[Titolo](percorso/relativo/regola.md)`). Assicurati che il percorso del file esista realmente. |

---

## 4.7 La Cartella "Rules di Esempio" e Risorse Collegate

Nel repository di Antigravity troverai una libreria completa di modelli di regole pronti per essere personalizzati e adottati all'interno dei tuoi progetti:

- **Cartella Fisica:** `Rules di esempio - DA PERSONALIZZARE/` nella radice del progetto, contenente template dedicati a:
  - `privacy_and_security.md`: Policy GDPR/HIPAA, anonimizzazione e confini di sicurezza dati.
  - `coding_style_and_standards.md`: Architettura Clean, convenzioni di tipizzazione e standard commit.
  - `testing_and_quality_gates.md`: Copertura minima di test, mocking e prevenzione regressioni.
  - `scientific_research_and_data.md`: Riproducibilità sperimentale, formati scientifici e gestione notebook.
- **Catalogo Visivo Completo:** Consulta il file [Descrizione-rules.md](../Descrizione-rules.md) nella root del progetto per esplorare la guida comparativa e scegliere i file più adatti al tuo stack tecnologico.

---

[⬅️ Torna all'Indice](../index.md) | [Capitolo Precedente: 3. Workflow & Automazioni](./03_workflows.md) | [Prossimo Capitolo: 5. Personalizzazioni Pratiche](./05_practical_customizations.md)
