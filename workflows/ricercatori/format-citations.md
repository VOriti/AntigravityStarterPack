# Workflow: /format-citations — Formattazione e Allineamento Citazioni

**Descrizione**: Automatizza la formattazione e la gestione delle citazioni in un manoscritto accademico (Markdown o LaTeX) trasformandole nello stile bibliografico richiesto.

**Esempio di Utilizzo**:
> `/format-citations formatta le citazioni nel file paper.md usando lo stile APA 7th a partire dalla bibliografia in references.bib.`

## Istruzioni Operative per l'Agente

1. **Analisi del Manoscritto**: Chiedi all'utente di indicare il file del manoscritto e il file della bibliografia (es. `.bib`, `.ris`, o testo piano).
2. **Definizione Stile**: Chiedi all'utente quale stile di citazione è richiesto dalla rivista (es. APA 7th, IEEE, Nature, Chicago).
3. **Mapping ed Estrazione**:
   - Trova tutte le citazioni informali nel testo (es. "Secondo (Rossi, 2020)..." o "[1]").
   - Mappa le citazioni agli elementi effettivi nel database bibliografico fornito.
4. **Riformattazione In-Place**:
   - Usa i tuoi tool di modifica codice (es. `replace_file_content`) per standardizzare tutte le citazioni inline (es. in Markdown pandoc `[@rossi2020]` o in LaTeX `\cite{rossi2020}`).
   - Genera o formatta la lista bibliografica alla fine del file, ordinata alfabeticamente o per ordine di apparizione, conformemente allo stile richiesto.
