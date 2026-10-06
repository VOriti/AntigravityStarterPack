# Workflow: /analyze-dataset — Analisi e Profilazione Dataset

**Descrizione**: Guida l'agente nell'analisi esplorativa automatica, pulizia e profilazione statistica di un dataset in formato CSV o Excel.

**Esempio di Utilizzo**:
> `/analyze-dataset esplora il file data/pazienti.csv e mostrami un grafico della distribuzione per età.`

## Istruzioni Operative per l'Agente

1. **Richiesta del File**:
   - Chiedi all'utente il percorso o il nome del file (CSV o Excel) da analizzare.
   - Fermati e attendi la risposta.

2. **Ispezione Iniziale**:
   - Scrivi uno script Python temporaneo (usando `pandas`) per caricare il file.
   - Estrai le informazioni di base: numero di righe/colonne, tipi di dato e conteggio dei valori mancanti (NaN).
   - Esegui lo script e analizza l'output.

3. **Pulizia e Statistiche**:
   - Scrivi ed esegui un secondo script Python per:
     - Gestire i valori mancanti (es. rimuovendo le righe o riempiendole con la media, se appropriato).
     - Calcolare statistiche descrittive base (media, mediana, deviazione standard, min/max) per le colonne numeriche.

4. **Creazione Report (Artifact)**:
   - Genera un report strutturato (usando un Artifact Markdown) contenente:
     - Una tabella riassuntiva dei tipi di dato e dei valori mancanti trovati (e come sono stati gestiti).
     - Una tabella con le statistiche descrittive delle colonne principali.
     - Suggerimenti su quali grafici potrebbero essere utili in base ai dati.

5. **Conclusione**:
   - Chiedi all'utente se desidera procedere con l'esportazione del dataset pulito o con la creazione di un grafico specifico.
