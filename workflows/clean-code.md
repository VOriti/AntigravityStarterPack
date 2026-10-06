# Workflow: /clean-code — Pulizia Codice e Riproducibilità

**Descrizione**: Formatta il codice, corregge errori di stile ed esegue il linting aggiungendo docstring strutturate per garantire codice pulito, leggibile e riproducibile.

**Esempio di Utilizzo**:
> `/clean-code sistema il file utils.py e documenta tutte le funzioni esportate.`

## Istruzioni Operative per l'Agente

1. **Selezione dei File**:
   - Chiedi all'utente su quale/i file o cartella eseguire la pulizia.
   - Fermati e attendi la risposta.

2. **Formattazione del Codice**:
   - Se si tratta di Python, verifica se `black` o `ruff` sono installati nel sistema. Se mancano, installali in un ambiente virtuale o via `uv`.
   - Esegui il formatter sui file specificati per standardizzare la formattazione.

3. **Linting e Analisi Statica**:
   - Analizza il codice per trovare variabili non usate, import superflui o complessità eccessiva.
   - Correggi automaticamente gli errori minori e chiedi all'utente per quelli maggiori (fornendo un'anteprima delle modifiche e il motivo).

4. **Generazione Documentazione (Docstrings)**:
   - Leggi il contenuto delle funzioni o classi principali prive di documentazione.
   - Chiedi all'utente, dandogli prima un esempio del tipo di docstring che vuoi aggiungere (stile NumPy o Google), se applicare la docstring ai file.
   - Usa le tue capacità di modifica del codice (es. `replace_file_content`) per aggiungere Docstring formattate in modo chiaro (es. stile NumPy o Google) spiegando input, output e logica.

5. **Conclusione**:
   - Comunica all'utente che il codice è stato formattato e documentato.
   - Mostra brevemente con un `diff` (o a parole) le modifiche più sostanziali (es. *"Ho rimosso 3 import non necessari e aggiunto 5 docstrings"*).
