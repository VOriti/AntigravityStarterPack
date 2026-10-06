# Workflow: /export-results — Esportazione e Archiviazione Risultati

**Descrizione**: Guida l'agente nella raccolta, organizzazione e archiviazione compressa dei risultati (dati, tabelle e grafici) generati durante l'analisi.

**Esempio di Utilizzo**:
> `/export-results comprimi tutti i log e i CSV nella cartella output e chiamala risultati_finali.zip.`

## Istruzioni Operative per l'Agente

1. **Individuazione dei Risultati**:
   - Esplora la directory di lavoro (o la cartella `scratch/` degli artifacts) per trovare tutti i file grafici (es. `.png`, `.pdf`) e i dataset elaborati (es. `.csv`, `.tsv`) generati di recente.

2. **Organizzazione dei File**:
   - Crea una nuova cartella chiamata `Risultati_Esportazione_[DataOdierna]`.
   - Usa script di shell o Python per spostare o copiare i file individuati all'interno di questa nuova cartella.

3. **Changelog e Metadati**:
   - Crea un file `README_export.md` all'interno della cartella dei risultati.
   - Documenta brevemente il contenuto della cartella (quali grafici rappresentano cosa, quali filtri sono stati applicati ai CSV).

4. **Archiviazione (.zip)**:
   - Usa uno script Python o comandi di sistema per creare un file `.zip` della cartella `Risultati_Esportazione_[DataOdierna]`.

5. **Conclusione**:
   - Informa l'utente del percorso assoluto del file `.zip` appena creato.
   - Chiedi se desidera eliminare i file temporanei originali per fare pulizia.
