# Workflow: /dockerize — Containerizzazione Automatica Docker

**Descrizione**: Containerizza automaticamente l'applicazione corrente generando Dockerfile ottimizzati multi-stage, file .dockerignore e configurazioni docker-compose.yml.

**Esempio di Utilizzo**:
> `/dockerize crea un Dockerfile multi-stage per questa applicazione Node.js con supporto a PostgreSQL in docker-compose.`

## Istruzioni Operative per l'Agente

1. **Ispezione del Progetto**: Determina lo stack tecnologico e il runtime dell'applicazione (es. Node.js, Python/Django, Go, PHP/Laravel).
2. **Creazione del Dockerfile**: Scrivi un `Dockerfile` multi-stage ottimizzato seguendo le best practice (esecuzione come utente non-root, sfruttamento del caching dei layer).
3. **File di Esclusione (.dockerignore)**: Crea il file `.dockerignore` escludendo directory pesanti o sensibili (`node_modules`, `.git`, `.env`, build artifacts).
4. **Composizione Servizi (Opzionale)**: Chiedi all'utente se l'applicazione richiede servizi ausiliari (PostgreSQL, MySQL, Redis) e genera un `docker-compose.yml` completo se necessario.
5. **Test di Build**: Esegui `docker build -t app-name .` per verificare che l'immagine si compili correttamente senza errori.
