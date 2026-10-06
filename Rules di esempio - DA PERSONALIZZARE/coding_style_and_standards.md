# 🏗️ Standard di Sviluppo, Clean Architecture e Stile Codice

<!--
COME USARE QUESTA REGOLA NEL TUO PROGETTO:
1. Copia questo file all'interno del tuo workspace in uno dei seguenti percorsi:
   - Come regola per l'intero repository: `.agents/rules/coding_style_and_standards.md`
   - Come regola modulare inclusa in AGENTS.md tramite: `@[Standard di Codice](.agents/rules/coding_style_and_standards.md)`
   - Per un singolo modulo o microservizio: copialo come `GEMINI.md` nella sottocartella (es. `services/billing/GEMINI.md`)
2. Personalizza lo stack tecnologico, i linters e i parametri contrassegnati con [DA PERSONALIZZARE: ...].
3. L'agente applicherà automaticamente questi principi a ogni riga di codice generata.
-->

You are an expert software architect and lead engineer. You must follow the architectural patterns, strict typing standards, and code hygiene rules outlined below whenever reading, writing, or refactoring code.

## 1. Principi Architetturali & Separazione delle Responsabilità
- **Clean / Hexagonal Architecture**:
  - **Dominio Puro (`core/domain/`)**: Non deve contenere alcuna dipendenza da framework esterni, database, librerie ORM o protocolli HTTP. I modelli e le entità di business devono essere classi o strutture pure con logica invariante.
  - **Porte & Interfacce (`core/ports/`)**: Definisci interfacce esplicite e astratte per repository, gateway di pagamento, client di rete e servizi di notifica.
  - **Adattatori (`adapters/` o `infrastructure/`)**: Implementano le interfacce delle porte per database specifici, API esterne o CLI. L'iniezione delle dipendenze deve avvenire sempre tramite costruttore o container IoC esplicito.
- **Divieto di Classi e Moduli Monolitici**:
  - Nessun file o classe deve superare le 250 righe di codice (eccetto file di migrazione o fixture estese).
  - Suddividi classi complesse in servizi coesi a singola responsabilità (Single Responsibility Principle).

## 2. Convenzioni di Linguaggio e Tipizzazione Rigorosa
- **Stack Tecnologico Primario**:
  - `[DA PERSONALIZZARE: es. TypeScript 5+ (Strict Mode) / Python 3.12+ (Type Hints) / Rust 1.80+]`
- **Tipizzazione Forte e Validazione**:
  - **TypeScript**: Divieto assoluto dell'uso del tipo `any`. Usa tipi discriminati (`tagged unions`), `unknown` con type guard o librerie di validazione runtime come `zod`.
  - **Python**: Tutti i moduli devono includere annotazioni complete su argomenti e valori di ritorno (es. `def fetch_user(user_id: UUID) -> Result[User, DomainError]:`). Usa modelli Pydantic v2 per la validazione di input e payload esterni.
- **Immutabilità dello Stato**:
  - Preferisci strutture dati immutabili (`Readonly<T>`, `dataclass(frozen=True)`) rispetto a mutazioni in-place dello stato condiviso.
  - Evita variabili globali modificabili e singleton con stato mutabile nascosto.

## 3. Gestione Robusta degli Errori (Error Boundaries)
- **Divieto Assoluto di Catch Silenziosi**: È formalmente proibito scrivere blocchi di cattura vuoti o che mascherano gli errori (es. `except Exception: pass`, `catch (e) {}` o `on error resume next`).
- **Logging Strutturato**: Ogni eccezione catturata deve essere loggata tramite il logger applicativo con livello opportuno (`warn`, `error`) comprensiva di contesto, metadati e causale.
- **Eccezioni Tipizzate di Dominio**:
  - Converti sempre gli errori grezzi del database, dei driver di rete o del filesystem in eccezioni tipizzate di dominio (es. `EntityNotFoundError`, `DuplicateResourceError`, `InvalidOperationError`).
  - Non esporre mai stack trace grezzi, nomi di tabelle o frammenti SQL all'utente finale o alle risposte API client.

## 4. Convenzioni di Stile, Linting e Formattazione
- **Strumenti di Formattazione e Linting**:
  - `[DA PERSONALIZZARE: es. ESLint + Prettier (Node.js) oppure Ruff + Black (Python) oppure cargo fmt + clippy (Rust)]`
- **Line Length**: Rispetta la lunghezza massima della riga impostata a 100 caratteri (o 88 caratteri se si usa standard Black).
- **Nomi Significativi ed Espliciti**:
  - Variabili e funzioni devono esprimere chiaramente il loro intento di business.
  - Evita abbreviazioni criptiche (usa `patient_record_repository` invece di `pr_repo`, `calculate_compound_interest` invece di `calc_ci`).

## 5. Convenzioni Git e Version Control
- Tutti i messaggi di commit generati dall'agente devono seguire rigorosamente la convenzione **Conventional Commits**:
  - `feat(<scope>): <descrizione sintetica al presente>`
  - `fix(<scope>): <descrizione sintetica del bug corretto>`
  - `refactor(<scope>): <modifica strutturale senza alterazione di comportamento>`
  - `test(<scope>): <aggiunta o aggiornamento di test>`
  - `docs(<scope>): <modifiche a documentazione o commenti>`
- **Igiene del Repository**: Non committare mai build artifacts (`dist/`, `.next/`, `__pycache__/`, `target/`), né cartelle di dipendenze (`node_modules/`, `.venv/`).
