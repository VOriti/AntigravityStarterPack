# Workflow: /setup-ci — Configurazione Pipeline CI/CD

**Descrizione**: Genera un file di configurazione per Continuous Integration e Continuous Deployment (es. GitHub Actions, GitLab CI) in base alle specifiche del progetto, impostando step per linting, testing e build.

**Esempio di Utilizzo**:
> `/setup-ci configura una pipeline GitHub Actions per Node.js che esegua i test su ogni push e faccia il deploy su Vercel se siamo sul branch main.`

## Istruzioni Operative per l'Agente

1. **Ispezione Ambiente**: Analizza la struttura del repository (file `package.json`, `pom.xml`, `requirements.txt`, ecc.) per determinare l'ambiente di esecuzione e gli strumenti necessari.
2. **Raccolta Requisiti**: Ti intervista se mancano dettagli operativi (es. chiavi segrete, target di deploy, provider di hosting).
3. **Generazione Configurazione**: Genera il file YAML (o equivalente) per la pipeline CI/CD (es. `.github/workflows/ci.yml`).
4. **Guida Secrets e Variabili**: Spiega come impostare le eventuali variabili d'ambiente necessarie (Secrets) per garantire l'esecuzione autonoma e sicura della pipeline.
