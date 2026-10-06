# Workflow: /laravel-scaffold — Scaffolding Completo Entità Laravel

**Descrizione**: Crea l'architettura completa per una nuova entità in Laravel (Model, Migration, Factory, Controller, Policy, Seeder e rotte) rispettando le convenzioni del framework.

**Esempio di Utilizzo**:
> `/laravel-scaffold genera l'intera architettura per l'entità Product con campi sku, prezzo e disponibilità.`

## Istruzioni Operative per l'Agente

1. **Identificazione Entità**: Chiedi all'utente il nome del Modello (es. `Product`).
2. **Esecuzione Artisan**: Usa il terminale per lanciare `php artisan make:model <Nome> -a` (che genera Model, Factory, Migration, Seeder, Request, Controller e Policy).
3. **Definizione Schema**: Usa `/grill-me` per farti dire le colonne del database, quindi aggiorna il file della migration.
4. **Generazione CRUD**: Scrivi la logica base di Create, Read, Update, Delete nel Controller e definisci le rotte nel file `routes/web.php` o `routes/api.php`.
