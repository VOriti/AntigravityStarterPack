# Workflow: /data-cleaning — Pulizia e Preprocessing Dati

**Descrizione**: Automatizza le operazioni di preparazione dati: gestione dei valori mancanti, rimozione dei duplicati, codifica delle variabili categoriche e normalizzazione.

**Esempio di Utilizzo**:
> `/data-cleaning analizza il file raw_data.csv: imputa i NA numerici con la mediana, esegui il one-hot encoding delle colonne categoriali e salva in cleaned_data.csv.`

## Istruzioni Operative per l'Agente

1. **Caricamento e Ispezione**: Carica il dataset in memoria (usando pandas o tidyverse).
2. **Diagnostica Problematiche**: Fornisce un sommario delle problematiche riscontrate (tipi errati, outlier, % di valori mancanti).
3. **Applicazione Pipeline di Trasformazione**: Applica pipeline di trasformazione standard o su misura, documentando ogni passaggio.
4. **Esportazione Dataset Pulito**: Esporta il dataset pulito in un formato ottimizzato (CSV, Parquet) pronto per la modellazione.
