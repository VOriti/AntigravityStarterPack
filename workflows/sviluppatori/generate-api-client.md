# Workflow: /generate-api-client — Generazione Client API da OpenAPI

**Descrizione**: Analizza una specifica OpenAPI/Swagger e genera automaticamente un client API frontend tipizzato in TypeScript (Axios, Fetch o React Query).

**Esempio di Utilizzo**:
> `/generate-api-client genera un client TypeScript con Axios a partire dal file docs/openapi.yaml salvandolo in src/api/.`

## Istruzioni Operative per l'Agente

1. **Ricerca Specifica**: Trova il file `openapi.yaml`, `swagger.json` o chiedi all'utente l'URL da cui scaricarlo.
2. **Setup Scaffolding**: Chiedi all'utente se preferisce un client basato su classi (OOP) o funzioni pure (Functional Programming), e quale libreria utilizzare (Fetch nativa, Axios, React Query/TanStack).
3. **Generazione Tipi**: Usa gli strumenti di parsing per generare tutte le interface/type TypeScript corrispondenti ai `components/schemas` del documento OpenAPI.
4. **Generazione Endpoints**: Crea i metodi (es. `getUser(id)`, `createPost(payload)`) con firme fortemente tipizzate, gestendo correttamente query parameters, body e headers.
5. **Output**: Salva il client generato (o aggiorna quello esistente) nella cartella specificata dall'utente (es. `src/api/`).
