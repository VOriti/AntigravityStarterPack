# 🚀 Guida di Base: Antigravity IDE

Benvenuto nel futuro dello sviluppo e dell'analisi! Questa guida è focalizzata sull'utilizzo di **Antigravity IDE**.

## 0. ⚖️ Antigravity IDE vs Antigravity 2.0 (App Standalone)

È fondamentale comprendere subito le differenze tra le due piattaforme.

*   **Antigravity IDE**: Ideale per la **scrittura attiva** di codice. Offre interfacce integrate nel codice (diff rosso/verde per accettare o rifiutare modifiche), autocompletamento in tempo reale (Antigravity Tab) e la possibilità di modificare al volo una porzione di testo tramite scorciatoie come `Ctrl+I` / `Cmd+I`.
*   **Antigravity 2.0 App Standalone**: Ideale per i **task autonomi complessi e multi-agente** (`/boost`, `/teamwork-preview`), Generative UI avanzate (rendering HTML/Artifacts) ed esecuzione isolata e sicura tramite la Terminal Sandbox.

**Usa Antigravity IDE quando:**
*   Sei in una sessione di scrittura del codice "attiva" (flow state).
*   Hai bisogno di autocompletamento in tempo reale, suggerimenti inline o refactoring rapido (es. tramite `Ctrl+I`).
*   Vuoi risolvere velocemente errori, warning o fare piccole modifiche riga per riga.

---

## 1. 🏗️ Creare e Configurare un Workspace

Prima di iniziare, è fondamentale capire come Antigravity IDE organizza i progetti. L'agente ragiona per **Workspace** (spazi di lavoro), che corrispondono semplicemente a delle cartelle sul tuo computer.

**Come impostare il tuo progetto:**
1. **Crea una Cartella**: Crea una nuova cartella vuota sul tuo computer per il tuo progetto (es. `MioProgetto`).
2. **Apri il Workspace**: In Antigravity IDE, vai nel menu principale e seleziona *File > Open Folder*. Seleziona la cartella appena creata.
3. **Salva il Workspace**: Vai su *File > Save Workspace As...* e salva il file (es. `progetto.code-workspace`) all'interno della cartella.
4. **Abilita i Workflow dello Starter Pack**: Crea una cartella chiamata `.agents/workflows/` (o `.agents/skills/` se stai usando l'App 2.0) all'interno del tuo nuovo workspace. Copia i file Markdown (`.md`) dei workflow che ti interessano dalla cartella `workflows/` di questo Starter Pack.
5. **Attiva le Regole di Qualità e Privacy (Always-On)**: Crea la cartella `.agents/rules/` nella radice del workspace. Copia i template pertinenti dalla cartella `Rules di esempio - DA PERSONALIZZARE/` (es. `privacy_and_security.md`, `coding_style_and_standards.md`) e personalizza i campi tra parentesi quadre. Queste regole orienteranno silenziosamente ogni risposta dell'agente.
6. **Salvataggio Continuo**: Tutte le modifiche al codice avvengono direttamente sul tuo disco locale in tempo reale.

---

## 2. 🛠️ I "Tricks" Fondamentali (Slash Commands)

Per ottenere il massimo da Antigravity, usa gli **Slash Commands** (`/`) nella chat.
*(Nota: comandi come `/boost`, `/teamwork-preview` e `/schedule` sono esclusivi dell'App Standalone).*

### 1. `/plan` - L'Architetto
**Quando usarlo:** Prima di iniziare a scrivere codice per funzionalità complesse.
**Cosa fa:** Crea un piano dettagliato step-by-step PRIMA di toccare qualsiasi file.

### 2. `/learn` - La Memoria a Lungo Termine
**Quando usarlo:** Per memorizzare una convenzione o una regola di progetto.
**Cosa fa:** Salva quel workflow o regola nel sistema.

### 3. `/grill-me` - Il Brainstorming Perfetto
**Quando usarlo:** Quando hai un'idea vaga per una feature.
**Cosa fa:** L'agente ti farà un'intervista interattiva per capire tutti i dettagli.

### 4. `/goal` - L'Operaio Instancabile
**Quando usarlo:** Task lungo, complesso o noioso (es. refactoring, test esaustivi).
**Cosa fa:** Attiva la modalità "Goal". L'agente lavorerà, correggerà i propri errori e itererà in totale autonomia.

---

## 3. ⚙️ I Workflows: Automazioni su Misura

I Workflow sono script personalizzati che insegnano all'agente come eseguire una procedura ripetitiva in modo standardizzato.
1. Nell'IDE, i workflow vivono nella cartella `.agents/workflows/` (N.B. se passi all'App Antigravity 2.0 verranno trattati come "Skills" e dovranno stare in `.agents/skills/<nome>/SKILL.md`).
2. Basta digitare nella chat il nome del workflow (es. `/analyze-dataset`).
3. Consulta l'elenco in [Descrizione-workflows.md](Descrizione-workflows.md).

---

## 4. 🧠 Gestione Progetti e Contesto

- **Mantieni le chat focalizzate:** Apri una nuova chat per task diversi (es. "Scrittura paper" e "Analisi dati").
- **Usa le Menzioni (`@`):** Se vuoi che l'agente legga un file specifico, menzionalo digitando `@nomefile` nella chat.

---

## 5. ⭐ Consigli d'Oro

1. **Non micro-gestire:** Dai il contesto ampio anziché istruzioni tecniche banali.
2. **Sii chiaro sull'obiettivo finale.**
3. **Se l'agente sbaglia:** Digli semplicemente "Hai sbagliato questo passaggio per questo motivo, riprova".

Buon Vibecoding! 🚀

---

[🏠 Torna al README](README.md) | [📖 Vai all'Indice del Manuale](index.md) | [🚀 Passa alla Guida di Base App Standalone](guida_di_base_app.md)
