# Workflow: /time-series-forecast — Previsione e Analisi Serie Storiche

**Descrizione**: Esegue analisi predittive su serie storiche (Time Series) gestendo stagionalità, stazionarietà, trend e metriche di validazione out-of-sample.

**Esempio di Utilizzo**:
> `/time-series-forecast analizza la serie mensile in inflazione.csv, verifica la stazionarietà con ADF e fai una previsione a 12 mesi con ARIMA.`

## Istruzioni Operative per l'Agente

1. **Comprensione del Task**: Chiedi all'utente la frequenza temporale dei dati (giornaliera, mensile, ecc.), l'orizzonte di previsione desiderato (es. 12 mesi) e la libreria preferita (es. `forecast`/`fable` in R, o `statsmodels`/`prophet` in Python).
2. **Esplorazione e Stazionarietà**:
   - Decomponi la serie (trend, stagionalità, rumore).
   - Esegui l'Augmented Dickey-Fuller (ADF) test per verificare la stazionarietà.
3. **Modellazione**: Adatta modelli appropriati (ARIMA, SARIMA, Prophet, o Exponential Smoothing) basandoti su AIC/BIC e sulla natura dei dati.
4. **Validazione e Output**: Calcola le metriche di errore out-of-sample (RMSE, MAE), genera i grafici delle previsioni con intervalli di confidenza e salva il report finale in Markdown.
