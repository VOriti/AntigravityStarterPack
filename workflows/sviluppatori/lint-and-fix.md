# Workflow: /lint-and-fix — Linting Automatico e Correzione del Codice

**Descrizione**: Rileva il linter configurato nel repository, esegue l'auto-fix degli errori formattabili e redige un piano di risoluzione per le violazioni residue.

**Esempio di Utilizzo**:
> `/lint-and-fix esegui il linter sul codice, correggi le violazioni automatiche e segnala gli errori rimanenti.`

## Istruzioni Operative per l'Agente

1. **Rilevamento del Linter**: Individua quale linter è configurato nel repository (es. ESLint, Biome, Flake8, Ruff).
2. **Esecuzione Auto-Fix**: Esegui il linter con il flag di correzione automatica (es. `eslint . --fix` oppure `ruff check --fix .`).
3. **Revisione Violazioni Residue**: Se restano errori non correggibili automaticamente, esaminali, analizza il codice e proponi un piano di intervento per risolverli. Se le modifiche richieste o la codebase sono estese, raccomanda all'utente di eseguire il comando `/goal` per disporre di tempo sufficiente e iterazioni autonome per un'analisi esaustiva. Se l'utente acconsente, interrompi l'esecuzione e permettigli di riavviare il workflow con `/goal`. In caso contrario, procedi con l'analisi entro il budget di token corrente.
   *(Direttiva runtime: If the required changes or codebase are extensive, recommend that the user execute the `/goal` command to provide sufficient time and autonomous iterations for an exhaustive analysis. If the user agrees, halt execution and let them rerun the workflow with `/goal`. Otherwise, proceed with the analysis within the current token budget.)*
4. **Verifica Finale**: Esegui nuovamente il linter per verificare e garantire 0 errori residui.
