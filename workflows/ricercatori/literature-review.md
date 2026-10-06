# Workflow: /literature-review — Revisione della Letteratura Scientifica

**Descrizione**: Guida l'agente nella ricerca, estrazione e sintesi della letteratura scientifica recente tramite banche dati biomediche e accademiche integrate.

**Esempio di Utilizzo**:
> `/literature-review trova gli ultimi 5 articoli sul gene BRCA1 e fai una tabella riassuntiva dei risultati.`

## Istruzioni Operative per l'Agente

1. **Richiesta dell'Argomento**:
   - Chiedi all'utente l'argomento specifico o le parole chiave per la ricerca bibliografica.
   - Fermati e attendi la risposta.

2. **Ricerca Tramite Skills**:
   - Usa le skill integrate per la letteratura (es. `pubmed-database`, `literature-search-openalex`, o `literature-search-europepmc`).
   - Cerca i paper più rilevanti o recenti (punta a raccogliere i top 10 risultati pertinenti).

3. **Analisi e Sintesi**:
   - Estrai da ogni paper: Titolo, Primo Autore, Anno di Pubblicazione, DOI e una breve sintesi dell'abstract (1-2 frasi sui risultati chiave).

4. **Creazione del Report (Artifact)**:
   - Crea un Artifact Markdown chiamato `revisione_letteratura.md`.
   - Inserisci un'introduzione riassuntiva sul trend dell'argomento basata sui paper trovati.
   - Crea una Tabella Markdown con le seguenti colonne: Titolo, Autore (Anno), Sintesi, Link (DOI).

5. **Conclusione**:
   - Notifica all'utente che la tabella è pronta.
   - Chiedigli se desidera scaricare il full-text (se Open Access) di uno specifico articolo presente nell'elenco.
