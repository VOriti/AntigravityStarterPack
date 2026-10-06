# Workflow: /api-docs — Generazione Documentazione OpenAPI/Swagger

**Descrizione**: Ispeziona i file di routing e i controller dell'applicazione per generare o aggiornare automaticamente la documentazione delle API in formato OpenAPI 3.0.

**Esempio di Utilizzo**:
> `/api-docs analizza i controller in src/controllers/ e genera il file docs/openapi.yaml.`

## Istruzioni Operative per l'Agente

1. **Ispezione del Routing**: Individua tutti i file di routing o controller nel workspace (es. Express, FastAPI, Laravel, Spring).
2. **Estrazione degli Endpoint**: Rileva per ciascun endpoint il metodo HTTP, il percorso, i parametri di query/path, il request body e le risposte tipizzate.
3. **Generazione Specifica**: Crea o aggiorna il file `openapi.yaml` (o `swagger.json`) seguendo lo standard OpenAPI 3.0.
4. **Validazione**: Verifica che lo schema JSON/YAML generato sia sintatticamente valido prima di concludere.
