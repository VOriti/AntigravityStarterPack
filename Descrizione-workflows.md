# 🤖 Tutti i Workflow dello Starter Pack

Questo Starter Pack contiene un potente set di workflow creati su misura per velocizzare il lavoro di scienziati, ricercatori, sviluppatori e data analyst.

Per utilizzarli nei tuoi progetti, devi:
1. Copiare i file `.md` dei workflow che ti interessano da questo Starter Pack (dalla cartella `workflows/`).
2. **Se usi l'IDE**: Incollarli all'interno della cartella `.agents/workflows/` nel tuo workspace corrente.
   **Se usi l'App 2.0**: Incollarli in `.agents/workflows/` ed eseguire `/migrate-workflows` in chat per trasformarli automaticamente in Skills.
3. A quel punto, potrai invocare ognuno di questi workflow o skills semplicemente digitando `/nome-workflow` nella chat (es. `/commit`).

Di seguito l'elenco dei workflow disponibili, suddivisi per target di riferimento.

## 0. Fondamentali (`workflows/`)
Workflow essenziali progettati per automatizzare e semplificare le operazioni quotidiane nello sviluppo del codice:
- [`/commit`](workflows/commit.md) **(Fondamentale)**: Guida l'agente a preparare i file e redigere un messaggio di commit, che potrai revisionare prima dell'esecuzione. Può gestire il push verso GitHub (impostando il remote se assente) o limitarsi a un salvataggio esclusivamente locale.
  **Esempio**: `/commit ho finito la funzionalità di login, salva tutto.`

- [`/clean-code`](workflows/clean-code.md): Formatta il codice, corregge piccoli errori e aggiunge docstring interagendo con te per scegliere lo stile migliore.
  **Esempio**: `/clean-code sistema il file utils.py e documenta tutte le funzioni esportate.`


## 1. Sviluppatori (`workflows/sviluppatori/`)
Progettati per automatizzare i comuni task di ingegneria del software, sia backend che frontend:

### 🔧 Testing & Qualità
- [`/generate-tests`](workflows/sviluppatori/generate-tests.md): Imposta ed esegue automaticamente test unitari completi per un modulo, analizzando iterativamente i fallimenti e auto-correggendosi (sia il test che il codice) finché non passano tutti.
  **Esempio**: `/generate-tests crea la suite di test unitari con pytest per il modulo auth_service.py coprendo tutti gli edge case.`
- [`/code-review`](workflows/sviluppatori/code-review.md): Analizza i file modificati per scovare bug, vulnerabilità o logica lenta. È in grado di applicare i fix proposti in maniera iterativa.
  **Esempio**: `/code-review analizza le modifiche staged nel branch e verifica la presenza di vulnerabilità o memory leak.`
- [`/lint-and-fix`](workflows/sviluppatori/lint-and-fix.md): Rileva automaticamente il linter in uso (es. ESLint, Ruff), prova a correggere gli errori banali (auto-fix) e redige un piano di intervento per le violazioni architetturali rimanenti.
  **Esempio**: `/lint-and-fix esegui ruff sul repository, correggi automaticamente le violazioni minori e mostra il piano per il resto.`
- [`/setup-ci`](workflows/sviluppatori/setup-ci.md): Genera un file di configurazione per pipeline CI/CD (GitHub Actions, GitLab CI) impostando step per linting, testing e build.
  **Esempio**: `/setup-ci imposta un workflow di GitHub Actions per testare su Python 3.11 e 3.12 con linting e coverage.`
- [`/refactor-architecture`](workflows/sviluppatori/refactor-architecture.md): Ristruttura il codice isolando le logiche di business ed allineandolo a pattern architetturali standard (MVC, Clean Architecture).
  **Esempio**: `/refactor-architecture separa la logica di business nel file order_controller.py secondo i principi della Clean Architecture.`

### 🖥️ Frontend & Accessibilità
- [`/scaffold-component`](workflows/sviluppatori/scaffold-component.md): Ti intervista per comprendere appieno le tue esigenze e genera un componente frontend strutturato (incluse props, stile e file di test).
  **Esempio**: `/scaffold-component crea un componente React per la barra di navigazione con Tailwind, TypeScript e test.`
- [`/seo-audit`](workflows/sviluppatori/seo-audit.md): Analisi SEO approfondita (HTML/React/Next.js) con validazione tag e suggerimenti.
  **Esempio**: `/seo-audit verifica i meta tag OpenGraph e i dati strutturati JSON-LD nelle pagine del blog Next.js.`
- [`/a11y-check`](workflows/sviluppatori/a11y-check.md): Scansione per la conformità WCAG (attributi ARIA, ruoli, contrasto visivo).
  **Esempio**: `/a11y-check analizza i form di login e checkout per garantire la conformità WCAG 2.1 AA.`
- [`/generate-api-client`](workflows/sviluppatori/generate-api-client.md): Scaffolding di un client TypeScript/Axios a partire da un file OpenAPI/Swagger.
  **Esempio**: `/generate-api-client genera un client TypeScript tipizzato a partire dal file swagger.json.`

### 🐘 Laravel
- [`/laravel-scaffold`](workflows/sviluppatori/laravel-scaffold.md): Generazione rapida (via Artisan) di Modello, Controller, Factory, Migration e rotte.
  **Esempio**: `/laravel-scaffold crea Model, Migration, Controller e Factory per l'entità Subscription.`
- [`/laravel-query-optimization`](workflows/sviluppatori/laravel-query-optimization.md): Rilevamento query N+1 e ottimizzazione con eager loading (`with()`).
  **Esempio**: `/laravel-query-optimization individua le query N+1 nel controller UserController e aggiungi le relazioni eager.`
- [`/laravel-pest-tests`](workflows/sviluppatori/laravel-pest-tests.md): Impostazione ed esecuzione iterativa di test PHPUnit/Pest.
  **Esempio**: `/laravel-pest-tests genera i test Pest per verificare l'endpoint di registrazione utente.`

### 🐳 Backend & API
- [`/dockerize`](workflows/sviluppatori/dockerize.md): Genera un `Dockerfile` e `.dockerignore` ottimizzati per il tuo stack applicativo.
  **Esempio**: `/dockerize crea un Dockerfile multi-stage per la nostra applicazione Node.js di produzione.`
- [`/api-docs`](workflows/sviluppatori/api-docs.md): Ispeziona la logica di routing e genera o aggiorna la documentazione in formato OpenAPI/Swagger.
  **Esempio**: `/api-docs estrai le rotte registrate in routes/api.php e genera la specifica openapi.yaml.`


## 2. Ricercatori (`workflows/ricercatori/`)
Progettati specificamente per scienziati, ricercatori medici e accademici:

### 🧬 Scienze della Vita & Ricerca Clinica
- [`/structure-prediction`](workflows/ricercatori/structure-prediction.md): Recupera e visualizza strutture proteiche 3D (AlphaFold/PDB) usando PyMOL.
  **Esempio**: `/structure-prediction scarica la struttura di P53 e mostrami i domini di legame principali.`
- [`/variant-analysis`](workflows/ricercatori/variant-analysis.md): Analizza la significatività clinica e l'impatto funzionale delle varianti genetiche usando dbSNP, ClinVar, ecc.
  **Esempio**: `/variant-analysis controlla la variante rs1234567 in ClinVar e dimmi se è patogenetica.`
- [`/clinical-trials-search`](workflows/ricercatori/clinical-trials-search.md): Interroga ClinicalTrials.gov per trovare trial in corso/in reclutamento per una specifica condizione.
  **Esempio**: `/clinical-trials-search trova i trial clinici in fase 3 attivi in Italia per il cancro ai polmoni.`

### 📚 Revisione & Bibliografia
- [`/literature-review`](workflows/ricercatori/literature-review.md): Interroga i database della letteratura (PubMed, OpenAlex) e genera tabelle riassuntive.
  **Esempio**: `/literature-review trova gli ultimi 5 articoli sul gene BRCA1 e fai una tabella riassuntiva dei risultati.`
- [`/format-citations`](workflows/ricercatori/format-citations.md): Trasformazione e allineamento automatico delle citazioni in Markdown o LaTeX (APA, IEEE, ecc.).
  **Esempio**: `/format-citations allinea le citazioni nel capitolo draft.md secondo lo standard APA 7th e genera il file .bib.`

### ✍️ Scrittura Accademica & Bandi
- [`/grant-proposal`](workflows/ricercatori/grant-proposal.md): Intervista e stesura dell'impalcatura di bandi (ERC, PRIN) tramite `/grill-me`.
  **Esempio**: `/grant-proposal intervistami sui macro-obiettivi della ricerca e genera l'impalcatura per un bando PRIN 2026.`
- [`/rebuttal-letter`](workflows/ricercatori/rebuttal-letter.md): Strutturazione automatica di una Response Letter a partire dai commenti dei revisori.
  **Esempio**: `/rebuttal-letter leggi i commenti dei revisori in reviews.txt e crea la bozza di replica punto per punto.`
- [`/protocol-design`](workflows/ricercatori/protocol-design.md): Supporta la stesura di un protocollo sperimentale accademico partendo da appunti informali.
  **Esempio**: `/protocol-design trasforma i miei appunti su estrazione RNA in un protocollo standard con controlli positivi e negativi.`
- [`/academic-polish`](workflows/ricercatori/academic-polish.md): Revisiona l'inglese di abstract o paper per renderlo più formale, fluido e in linea con le pubblicazioni Q1.
  **Esempio**: `/academic-polish eleva lo stile del paragrafo Discussion per renderlo conforme agli standard di Nature Biotechnology.`


## 3. Data Analyst / Economisti (`workflows/data_analyst_economisti/`)
Pensati per chi estrae valore dai dati e realizza modelli statistici:

### 📊 Preparazione & Esplorazione Dati
- [`/data-cleaning`](workflows/data_analyst_economisti/data-cleaning.md): Automatizza il preprocessing (imputazione valori mancanti, codifica, normalizzazione) su dataset strutturati.
  **Esempio**: `/data-cleaning gestisci i valori nulli, converti le date e normalizza i codici fiscali nel dataset pazienti.csv.`
- [`/analyze-dataset`](workflows/data_analyst_economisti/analyze-dataset.md): Carica file CSV/Excel locali, esegue EDA (Exploratory Data Analysis) e genera statistiche e visualizzazioni.
  **Esempio**: `/analyze-dataset esplora il file data/pazienti.csv e mostrami un grafico della distribuzione per età.`
- [`/build-dashboard`](workflows/data_analyst_economisti/build-dashboard.md): Scaffolding di una dashboard interattiva locale (es. Streamlit) per navigare visualmente il dataset.
  **Esempio**: `/build-dashboard crea un'app Streamlit per esplorare interattivamente la serie temporale dei prezzi dell'energia.`
- [`/export-results`](workflows/data_analyst_economisti/export-results.md): Aggrega dati, grafici e report generati in un singolo archivio `.zip`.
  **Esempio**: `/export-results comprimi tutti i log e i CSV nella cartella output e chiamala risultati_finali.zip.`

### 📈 Modelli Statistici & Econometria
- [`/panel-data-analysis`](workflows/data_analyst_economisti/panel-data-analysis.md): Modelli su dati longitudinali (Effetti Fissi vs Random, Hausman test).
  **Esempio**: `/panel-data-analysis stima un modello a effetti fissi su dati provinciali ed esegui l'Hausman test.`
- [`/causal-inference`](workflows/data_analyst_economisti/causal-inference.md): Implementazione di modelli causali (DiD, RDD) con controlli pre-trend e McCrary.
  **Esempio**: `/causal-inference applica una Difference-in-Differences con controlli pre-trend sulla riforma scolastica del 2024.`
- [`/time-series-forecast`](workflows/data_analyst_economisti/time-series-forecast.md): Decomposizione e previsione di serie storiche tramite ARIMA, SARIMA o Prophet.
  **Esempio**: `/time-series-forecast decompone il trend stagionale delle vendite ed esegui una stima ARIMA a 12 mesi.`

---
[🏠 Torna all'inizio](README.md)

[🏠 Torna all'Indice del Manuale](index.md)
