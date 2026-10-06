# Workflow: /panel-data-analysis — Analisi Dati Panel ed Econometria

**Descrizione**: Esegue analisi su dati panel longitudinali per la ricerca statistico-econometrica, supportando modelli a effetti fissi (FE) e a effetti random (RE) con test di Hausman.

**Esempio di Utilizzo**:
> `/panel-data-analysis analizza il dataset panel_aziende.csv con id=firm_id e tempo=year, confrontando effetti fissi e random con il test di Hausman.`

## Istruzioni Operative per l'Agente

1. **Setup Dati**: Chiedi all'utente di indicare il dataset (CSV/Excel) e di specificare l'indice temporale (es. `year`) e l'indice di entità (es. `country` o `firm_id`).
2. **Diagnostica Iniziale**:
   - Esegui statistiche descrittive.
   - Controlla la presenza di valori mancanti e il bilanciamento del panel.
3. **Scelta del Modello**: Usa R (libreria `plm`) o Python (`linearmodels`) per stimare il modello.
   - Suggerisci l'esecuzione di un Test di Hausman per scegliere tra Effetti Fissi ed Effetti Random.
4. **Analisi e Reportistica**:
   - Stampa una tabella di regressione professionale (stile `stargazer` o `summary_col`).
   - Genera un Artifact Markdown con i risultati, le stime dei coefficienti, p-value e raccomandazioni analitiche.
