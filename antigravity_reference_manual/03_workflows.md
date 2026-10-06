# 3. Workflow & Automazioni

I Workflow sono script personalizzati che insegnano all'agente come eseguire procedure operative standard o task ripetitivi (es. creare un commit, analizzare un dataset).

## 3.1 Gestione dei Workflow (e Transizione a Skills)
- **Posizione (IDE vs App)**: 
  - Nell'**Antigravity IDE** (VS Code), i workflow vivono come semplici file `.md` nella cartella `.agents/workflows/`.
  - Nell'**Antigravity App 2.0**, i workflow sono diventati **Skills**. Vanno posizionati in `.agents/skills/<nome-comando>/SKILL.md` e devono obbligatoriamente contenere un'intestazione YAML (frontmatter) iniziale con `name` e `description`. Se hai vecchi file workflow, puoi usare il comando `/migrate-workflows` in chat per farteli aggiornare in automatico!

- **Esecuzione**: Per eseguire un workflow o una skill, digita il suo nome nella chat (es. `/analyze-dataset`).

- **Creazione**: 
  - Usa lo slash command `/learn` dopo aver completato con successo un task.
  - Chiedi direttamente all'agente: *"Crea una skill chiamata `data_cleaner` che formatti sempre i dataset in questo modo."*

## 3.2 Workflow Pre-costruiti dello Starter Pack
Lo starter pack include una vasta gamma di 32 workflow pronti all'uso, suddivisi nelle categorie Fondamentali, Sviluppatori, Ricercatori e Data Analyst / Economisti.

## 3.3 Collegamento con la Libreria dei Workflow
Per mantenere questa guida sintetica, l'elenco completo, le istruzioni operative e gli esempi di prompt di ciascuna automazione sono documentati nella guida visiva dedicata:

?? **[Consulta la Libreria Completa dei Workflow](../Descrizione-workflows.md)**

---
[⬅️ Torna all'Indice](../index.md) | [Capitolo Precedente: 2. Riferimento degli Slash Command](./02_slash_commands.md) | [Prossimo Capitolo: 4. Regole (Rules): Sintassi, Scoping e Guida Pratica](./04_rules.md)
