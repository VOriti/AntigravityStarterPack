# Workflow: /laravel-query-optimization — Ottimizzazione Query Eloquent e Rilevamento N+1

**Descrizione**: Ispeziona i controller e i modelli Laravel per prevenire il problema delle query N+1, applicare l'eager loading con with() e ottimizzare gli indici del database.

**Esempio di Utilizzo**:
> `/laravel-query-optimization analizza OrderController per individuare query N+1 sulle relazioni customer e items.`

## Istruzioni Operative per l'Agente

1. **Analisi Eloquent**: Analizza i Controller per individuare chiamate al database complesse o loop su relazioni non caricate.
2. **Rilevamento N+1**: Identifica codice come `$users = User::all(); foreach($users as $u) { $u->posts; }` e suggerisci/applica l'Eager Loading (`User::with('posts')->get()`).
3. **Analisi Indici**: Verifica le migration per capire se le colonne usate frequentemente nei `where()` hanno un indice.
4. **Ottimizzazione**: Proponi query raw (`DB::raw`) o raggruppamenti per report pesanti, e fornisci il codice corretto.
