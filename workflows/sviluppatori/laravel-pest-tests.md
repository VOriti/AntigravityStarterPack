# Workflow: /laravel-pest-tests — Scaffolding Test Pest PHP per Laravel

**Descrizione**: Genera ed esegue rapidamente test unitari e di feature con Pest PHP (o PHPUnit) per controller e modelli di un'applicazione Laravel.

**Esempio di Utilizzo**:
> `/laravel-pest-tests crea ed esegui i test di feature per UserController coprendo autenticazione e validazione.`

## Istruzioni Operative per l'Agente

1. **Identificazione Target**: Chiedi all'utente quale Controller, Action o Model si desidera testare.
2. **Generazione Scaffolding**: Esegui `php artisan pest:test TargetTest` (o equivalente per PHPUnit) per creare il file di test.
3. **Scrittura Casi d'Uso**:
   - Analizza la logica del Controller (middleware, validazioni Request, autorizzazioni Policy).
   - Scrivi i test (in sintassi Pest `it('does something', function() { ... })`) coprendo:
     - Accesso utente non autenticato (401/403).
     - Validazione dati fallita (422).
     - Successo e manipolazione database (status 200, DB assertions).
4. **Iterazione Continua**: Esegui `php artisan test --filter TargetTest`. Se i test falliscono, leggi l'errore e aggiusta automaticamente il test o il codice (chiedendo il permesso all'utente per modifiche al codice sorgente).
