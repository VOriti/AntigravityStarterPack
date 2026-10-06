# 🚀 Guida di Base: Antigravity 2.0 (App Standalone)

Benvenuto nel futuro dello sviluppo e dell'analisi! Questa guida è focalizzata sull'utilizzo di **Antigravity 2.0 App Standalone**.

## 0. ⚖️ Antigravity 2.0 App Standalone vs Antigravity IDE

È fondamentale comprendere subito le differenze tra le due piattaforme.

*   **Antigravity 2.0 App Standalone**: L'App è orientata all'**autonomia**. È l'ideale per lanciare **task autonomi complessi e multi-agente in background** (che magari durano ore, es. tramite `/boost` e `/teamwork-preview`), per generare interfacce e widget avanzati (Generative UI) ed eseguire comandi in modo isolato (Terminal Sandbox). 
*   **Antigravity IDE**: È un editor di codice pensato per la **scrittura attiva**, autocompletamento in tempo reale, scorciatoie (`Ctrl+I`) e revisioni visive (diff rosso/verde).

**Usa Antigravity 2.0 App Standalone quando:**
*   Devi delegare task ampi, architetturali o che coinvolgono l'intera repository.
*   Vuoi avviare task lunghi che devono procedere in background.
*   Vuoi sfruttare il ragionamento profondo (`/boost`) e la delega multi-agente (`/teamwork-preview`).
*   Vuoi esplorare dati e diagrammi interattivi (Artifacts & Generative UI).

### Revisione del codice nell'App
Non essendoci il diff rosso/verde in riga, puoi usare:
*   **Artifacts con richiesta di feedback**: L'agente genera un "Artefatto" con un pulsante "Proceed".
*   **Impostazioni di Approvazione**: Modifica la **Tool Execution Policy** su `request-review` per far sì che l'app ti chieda conferma prima di eseguire comandi o modificare file.
*   **Git Flow**: Chiedi all'agente di lavorare su un branch Git separato.

---

## 1. 🏗️ Creare e Configurare un Workspace

In Antigravity 2.0, l'agente ragiona per **Workspace** (spazi di lavoro).

1. **Seleziona la Cartella**: Apri l'app e seleziona la cartella radice del tuo progetto (es. `MioProgetto`).
2. **Abilita i Workflow (Skills) dello Starter Pack**: 
   - **Se usi l'estensione IDE (VS Code)**: Crea una cartella chiamata `.agents/workflows/` all'interno del tuo workspace e copia lì i file Markdown (`.md`) che ti interessano.
   - **Se usi l'App (Antigravity 2.0)**: I workflow sono stati rinominati in "Skills". Devi creare una struttura `.agents/skills/<nome-comando>/SKILL.md` e aggiungere un'intestazione YAML iniziale (frontmatter) con `name` e `description` all'inizio del file. In alternativa, puoi usare il comando integrato `/migrate-workflows` nell'App per convertire automaticamente i vecchi file `.md` posizionati in `.agents/workflows/`.
3. **Attiva le Regole di Sicurezza e Ricerca (Always-On)**: Crea una cartella `.agents/rules/` all'interno del workspace e copiaci i file `.md` adatti al tuo lavoro dalla cartella `Rules di esempio - DA PERSONALIZZARE/` (es. `privacy_and_security.md`, `scientific_research_and_data.md`). L'agente autonomo applicherà automaticamente questi guardrail in background ad ogni iterazione.

---

## 2. 🛠️ I "Tricks" Fondamentali (Slash Commands)

Digita questi comandi nella chat per attivare comportamenti specializzati. Si dividono in comandi universali e comandi esclusivi per l'App Standalone.

### Comandi Universali

#### 1. `/plan` - L'Architetto
**Quando usarlo:** Prima di iniziare a scrivere codice per funzionalità complesse.
**Cosa fa:** Crea un piano dettagliato step-by-step PRIMA di toccare qualsiasi file.

#### 2. `/learn` - La Memoria a Lungo Termine
**Quando usarlo:** Per memorizzare una convenzione o una regola di progetto.
**Cosa fa:** Salva quel workflow o regola nel sistema.

#### 3. `/grill-me` - Il Brainstorming Perfetto
**Quando usarlo:** Quando hai un'idea vaga per una feature.
**Cosa fa:** L'agente ti farà un'intervista interattiva per capire tutti i dettagli.

#### 4. `/goal` - L'Operaio Instancabile
**Quando usarlo:** Task lungo, complesso o noioso (es. refactoring, migrazione).
**Cosa fa:** Attiva la modalità "Goal". L'agente lavorerà in background, correggerà i propri errori e itererà in totale autonomia. 

### Comandi Esclusivi (Solo Antigravity 2.0)

#### 1. `/boost` - Ragionamento Profondo
**Quando usarlo:** Analisi algoritmica profonda o bug complessi.
**Cosa fa:** Avvia una pipeline multi-agente a 3 fasi per scomporre, implementare e verificare la soluzione.

#### 2. `/teamwork-preview` - Team Parallelo
**Quando usarlo:** Obiettivo grande (es. migrazione di un'intera codebase).
**Cosa fa:** Lancia un team di agenti che lavorano in parallelo su milestone separate.

#### 3. `/schedule` - Il Cron Job
**Quando usarlo:** Controlli periodici.
**Cosa fa:** Imposta un timer o un cron job in background.

---

## 3. ⚙️ I Workflows: Automazioni su Misura

I Workflow aiutano a standardizzare task ripetitivi. Avviali digitando `/nome-workflow`. Cerca in [Descrizione-workflows.md](Descrizione-workflows.md) quelli disponibili.
- **In IDE**: Posizionali nella cartella `.agents/workflows/`.
- **In App**: Posizionali come Skills in `.agents/skills/<nome-workflow>/SKILL.md` (con YAML iniziale) o posizionali in `.agents/workflows/` e poi usa il comando `/migrate-workflows` in chat per farli convertire automaticamente dall'agente!
---

## 4. 🔬 Skills & Plugins Scientifici

L'App supporta Skills integrate (es. PubMed, AlphaFold, Ensembl). Basta usare il prompting naturale per concatenarle (es. "Cerca articoli con PubMed ed estrai le sequenze con Ensembl").

---

## 5. 📊 Analisi Dati e Strumenti Avanzati

*   **Analisi Dati Locali:** L'agente lavora sulla tua macchina (può creare script Python e lanciarli nella Sandbox).
*   **Artifacts & Diagrammi:** Genera diagrammi Mermaid e UI esplorative interattive.
*   **Permessi e Sicurezza:** L'App 2.0 include la Terminal Sandbox, ma ricorda sempre di chiedere all'agente di *salvare i risultati in nuovi file* per non sovrascrivere originali senza review.

---

## 6. ⭐ Consigli d'Oro

1. **Non micro-gestire:** Dai il contesto ampio anziché istruzioni tecniche banali.
2. **Sii chiaro sull'obiettivo finale.**
3. **Se l'agente sbaglia:** Digli semplicemente "Hai sbagliato questo passaggio per questo motivo, riprova".

Buon Vibecoding! 🚀

---

[🏠 Torna al README](README.md) | [📖 Vai all'Indice del Manuale](index.md) | [💻 Passa alla Guida di Base IDE](guida_di_base_ide.md)
