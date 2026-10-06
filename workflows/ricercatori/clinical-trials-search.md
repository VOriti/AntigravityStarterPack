# Workflow: /clinical-trials-search — Ricerca Trial Clinici su ClinicalTrials.gov

**Descrizione**: Interroga il database ufficiale ClinicalTrials.gov tramite API per individuare studi clinici attivi o in fase di reclutamento per specifiche patologie o farmaci.

**Esempio di Utilizzo**:
> `/clinical-trials-search trova i trial clinici in fase 3 attivi in Italia per il cancro ai polmoni.`

## Istruzioni Operative per l'Agente

1. **Raccolta Criteri**: Chiedi all'utente la patologia o condizione medica (es. "Alzheimer's"), l'intervento o farmaco (es. "Aducanumab") e la fase di sperimentazione desiderata.
2. **Interrogazione API**: Utilizza la skill `clinical-trials-database` per interrogare l'APIv2 di ClinicalTrials.gov con i parametri raccolti.
3. **Filtraggio Risultati**: Escludi i trial terminati o ritirati (*terminated*, *withdrawn*), mantenendo esclusivamente quelli attivi o in fase di reclutamento (*"Recruiting"* o *"Active, not recruiting"*).
4. **Generazione Report**: Crea una tabella riassuntiva che elenchi NCT ID, titolo del trial, fase e sponsor per i primi 5-10 risultati più pertinenti.
