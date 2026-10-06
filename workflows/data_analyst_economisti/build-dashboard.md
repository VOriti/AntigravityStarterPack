# Workflow: /build-dashboard — Creazione Dashboard Interattiva

**Descrizione**: Supporta la creazione di una dashboard interattiva (es. Streamlit, Dash, Shiny) a partire da un dataset, per presentare i risultati in modo navigabile.

**Esempio di Utilizzo**:
> `/build-dashboard crea un'app Streamlit che legga il file sales.csv e mostri una mappa interattiva e un grafico a linee filtrabile per anno.`

## Istruzioni Operative per l'Agente

1. **Ispezione del Dataset**: Legge le intestazioni del dataset per capire il contesto e la struttura dei dati.
2. **Scrittura dell'Applicazione**: Scrive il codice Python/R necessario per avviare l'applicazione web locale.
3. **Integrazione Widget e Grafici**: Include widget di filtraggio (slider, dropdown) e librerie di plotting (Plotly, Altair, ggplot2).
4. **Configurazione ed Esecuzione**: Fornisce le istruzioni per installare le dipendenze (es. `requirements.txt`) ed eseguire la dashboard.
