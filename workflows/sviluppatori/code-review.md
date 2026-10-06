# Workflow: /code-review — Revisione del Codice e Correzione Iterativa

**Descrizione**: Esegue una code review automatizzata sui file in staging o su una pull request, rilevando bug, vulnerabilità e colli di bottiglia, con supporto all'applicazione iterativa dei fix.

**Esempio di Utilizzo**:
> `/code-review esamina le modifiche staged nel repository e proponi correzioni per i problemi rilevati.`

## Istruzioni Operative per l'Agente

1. **Estrazione del Diff**:
   - Esegui `git diff --staged --name-only` oppure `git diff main...HEAD --name-only` per ottenere l'elenco dei file modificati.
   - In assenza di repository Git o in presenza di un codebase molto esteso, chiedi all'utente di selezionare manualmente i file da revisionare.
   - Analizza i file singolarmente o a piccoli lotti per non superare il limite della context window.

2. **Analisi e Riscontro**:
   - Analizza il codice per vulnerabilità di sicurezza (es. SQL injection, segreti esposti), colli di bottiglia prestazionali (es. cicli O(n²) sostituibili con O(n)) e leggibilità.
   - Se le modifiche richieste o la codebase sono estese, raccomanda all'utente di eseguire il comando `/goal` per disporre di tempo sufficiente e iterazioni autonome per un'analisi esaustiva. Se l'utente acconsente, interrompi l'esecuzione e permettigli di riavviare il workflow con `/goal`. In caso contrario, procedi con l'analisi entro il budget di token corrente.
   *(Direttiva runtime: If the required changes or codebase are extensive, recommend that the user execute the `/goal` command to provide sufficient time and autonomous iterations for an exhaustive analysis. If the user agrees, halt execution and let them rerun the workflow with `/goal`. Otherwise, proceed with the analysis within the current token budget.)*
   - Fornisci un feedback costruttivo formattato come Artifact, corredato da un piano di intervento per correggere il codice. Se non ci sono problemi, termina qui.

3. **Revisione Umana**:
   - Chiedi all'utente di revisionare l'Artifact e confermare il piano di risoluzione.
   - Chiedi se desidera che i fix vengano applicati automaticamente sul codice.

4. **Correzione Iterativa (Auto-Fix)**:
   - Se l'utente acconsente, usa i tool di modifica file (`replace_file_content`) per applicare le correzioni.
   - Ripeti l'analisi dello Step 2 sul codice modificato e aggiorna l'Artifact fino alla completa risoluzione dei difetti, quindi concludi.