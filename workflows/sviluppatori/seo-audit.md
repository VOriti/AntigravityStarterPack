# Workflow: /seo-audit — Audit SEO per Applicazioni Web

**Descrizione**: Analizza il codice di un progetto web (HTML, React, Next.js) per individuare problemi di SEO (Search Engine Optimization) e proporre interventi mirati su tag, meta dati e accessibilità semantica.

**Esempio di Utilizzo**:
> `/seo-audit analizza le pagine in src/app/ e verifica meta tag, Open Graph e conformità dei titoli.`

## Istruzioni Operative per l'Agente

1. **Scansione Pagine**: Trova le rotte principali e le pagine del progetto.
2. **Verifica Tag Meta**: Controlla la presenza e validità di `title`, `meta description`, `canonical URL`, e tag Open Graph per i social network.
3. **Verifica Strutturale**: Controlla l'uso corretto degli header (`<h1>`, `<h2>`), attributi `alt` per le immagini e tag semantici (es. `<main>`, `<article>`).
4. **Generazione Report**: Crea un Artifact con un punteggio stimato e i suggerimenti per sistemare gli elementi mancanti (oppure applicali in automatico con il consenso dell'utente).
