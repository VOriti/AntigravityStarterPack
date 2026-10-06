# Workflow: /refactor-architecture — Ristrutturazione e Pattern Architetturali

**Descrizione**: Aiuta a ristrutturare il codice esistente in conformità a pattern architetturali standard (es. MVC, Clean Architecture, Hexagonal), separando le responsabilità e riducendo il debito tecnico.

**Esempio di Utilizzo**:
> `/refactor-architecture sposta tutta la logica di business dai controller ai service e crea delle interfacce chiare.`

## Istruzioni Operative per l'Agente

1. **Ispezione Architettura**: Ispeziona i file indicati o la cartella corrente per mappare le responsabilità attuali.
2. **Piano di Ristrutturazione**: Propone un piano di refactoring strutturato tramite un Artifact, dettagliando interfacce, layer e separazione dei ruoli.
3. **Applicazione Refactoring**: Se approvato, sposta le funzioni, crea i service/repository, rinomina i file e aggiorna le importazioni in tutta la codebase.
4. **Copertura Test**: Suggerisce dove aggiungere o aggiornare i test unitari e di integrazione per proteggere la nuova logica isolata.
