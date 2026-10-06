# 1. Concetti Fondamentali & Architettura

Antigravity cambia radicalmente il paradigma dalla programmazione manuale al **Vibecoding**: tu esprimi l'intento, mentre l'agente si occupa dell'esecuzione.

## 1.1 Antigravity IDE vs Antigravity App (Standalone)
Esistono due ambienti di sviluppo principali: l'**Antigravity IDE** (basato su VS Code) e l'applicazione desktop **Antigravity 2.0**. Entrambi condividono le stesse capacità ma si adattano a flussi di lavoro differenti.

**Usa Antigravity IDE quando:**
*   Sei in una sessione di scrittura del codice "attiva".
*   Hai bisogno di autocompletamento in tempo reale, suggerimenti inline o refactoring rapido di una singola funzione (tramite `Cmd/Ctrl+I`).
*   Vuoi risolvere errori di compilazione, warning del linter o effettuare revisioni visive dirette sul codice (tramite i visual diff overlays).

**Usa Antigravity 2.0 (App) quando:**
*   Devi delegare task ampi, architetturali o che coinvolgono l'intera repository.
*   Hai bisogno di usare slash commands avanzati come `/teamwork-preview` (squadre di sotto-agenti paralleli), `/boost` (ricerca e pianificazione profonda) o `/schedule` (task ricorrenti e timer).
*   Vuoi avviare task lunghi che devono procedere in background in autonomia.
*   Hai bisogno di esplorare dati visivamente (tramite rendering di widget, grafici interattivi e documenti markdown avanzati).
*   Richiedi impostazioni di sicurezza granulari (es. *Terminal Sandbox*, internet policy).

**Revisione del Codice nell'App:** A differenza dell'IDE, nell'App standalone puoi controllare i cambiamenti prima dell'esecuzione usando i branch Git separati (per revisionare i diff a posteriori), attivando la *Tool Execution Policy = request-review* nelle impostazioni, oppure richiedendo all'agente di generare un "Artefatto" con un pulsante di esecuzione (Proceed) che ti permetta di leggere il piano prima che venga eseguito.

## 1.2 Il Paradigma del Vibecoding
Il Vibecoding è un approccio in cui tu fornisci la visione, l'architettura e i vincoli, mentre l'agente IA autonomo si occupa dell'implementazione tecnica, dell'analisi dei dati, della ricerca e della scrittura del codice.
L'agente agisce come un pair programmer iper-capace che può leggere file, scrivere codice, eseguire comandi nel terminale e cercare sul web.

## 1.3 Gestione della Finestra di Contesto (Context Window)
L'agente IA ha una "memoria a breve termine" limitata, nota come Context Window.
- **Mantieni il Focus**: Non usare la stessa conversazione per mesi. Quando passi da "Analisi dati RNA" a "Scrittura paper", apri una nuova chat per mantenere l'agente veloce e concentrato.

- **Menzioni Esplicite (`@`)**: Usa `@nomefile` nella chat per forzare esplicitamente l'agente a leggere un contesto specifico. Questo evita di fargli perdere tempo a cercare i file.

## 1.4 Autonomia dell'Agente e Permessi
Antigravity è potente: **può eseguire comandi nel tuo terminale**.
- **Operazioni Distruttive**: Usa cautela. L'agente esegue comandi bash/PowerShell sulla tua macchina.

- **La Sicurezza Prima di Tutto**: Chiedi sempre all'agente di salvare i risultati in nuovi file piuttosto che sovrascrivere gli originali, a meno che non sia esplicitamente intenzionale.

## 1.5 Il Sistema di Personalizzazione
Antigravity scopre automaticamente le personalizzazioni attraversando directory specifiche:
1. **Personalizzazioni del Workspace**: La cartella `.agents/` alla radice del tuo progetto.

2. **Regole di Directory & Progetto**: `GEMINI.md`, `AGENTS.md`, `.agents/rules/*.md`.

3. **Configurazione Globale**: `~/.gemini/config/`.

Queste personalizzazioni definiscono come si comporta l'agente e sono trattate in dettaglio in [Capitolo 4: Rules](./04_rules.md), [Capitolo 5: Personalizzazioni Pratiche](./05_practical_customizations.md) e [Capitolo 6: Architettura Avanzata](./06_advanced_architecture.md).

## 1.6 Creare e Salvare un Workspace
In Antigravity, un "Workspace" non è altro che una cartella aperta nell'ambiente di sviluppo. 

**Procedura di Setup:**
1. **Creazione:** Crea una nuova directory sul tuo file system locale.

2. **Apertura:** Avvia Antigravity (IDE o App Standalone) e apri la directory (es. tramite *File > Open Folder* nell'IDE). Questo stabilisce i confini (la radice) entro i quali l'agente opererà.

3. **Salvataggio del Workspace (solo IDE):** È consigliato usare *File > Save Workspace As...* per generare un file `.code-workspace`. Questo manterrà traccia di tutte le cartelle aperte e delle configurazioni. Ricordati di aggiornarlo se aggiungi nuove cartelle esterne al progetto.

4. **Persistenza:** Tutte le modifiche al codice sono salvate direttamente sul disco locale in tempo reale. Il contesto della chat, gli "Artifacts" e le regole apprese vengono salvati in cartelle di log, non c'è bisogno di salvare manualmente lo stato del progetto.

5. **Importare Workflow:** Affinché il tuo nuovo workspace riconosca i comandi personalizzati descritti in questo manuale, devi copiare i file che desideri dalla cartella `workflows/` di questo Starter Pack alla cartella `.agents/workflows/` del tuo nuovo progetto.

---
[⬅️ Torna all'Indice](../index.md) | [Prossimo Capitolo: 2. Riferimento degli Slash Command](./02_slash_commands.md)
