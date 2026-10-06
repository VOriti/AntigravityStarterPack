# Workflow: /causal-inference — Modelli di Inferenza Causale

**Descrizione**: Implementa tecniche di inferenza causale (Difference-in-Differences, Regression Discontinuity Design, Variabili Strumentali) per la ricerca quantitativa ed econometrica.

**Esempio di Utilizzo**:
> `/causal-inference stima l'effetto del trattamento sul dataset policy.csv usando un modello Difference-in-Differences con controlli per regione.`

## Istruzioni Operative per l'Agente

1. **Definizione dell'Identificazione**: Tramite `/grill-me`, intervista il ricercatore per chiarire la variabile di trattamento, il gruppo di controllo e il disegno di ricerca.
2. **Pre-Trend e Assunzioni**:
   - Per DiD: Verifica l'assunto di parallel trend tracciando un grafico delle medie pre-trattamento.
   - Per RDD: Genera un McCrary density test per verificare la manipolazione attorno alla soglia.
3. **Esecuzione Modello**: Scrivi il codice R/Python per eseguire la stima (includendo errori standard robusti/clusterizzati).
4. **Valutazione Finale**: Fornisci i risultati dell'effetto causale e scrivi una sintesi interpretativa da inserire nel paper.
