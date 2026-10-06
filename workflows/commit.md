# Workflow: /commit — Git Commit & Push Automatico

**Descrizione**: Guida l'agente nella gestione del salvataggio (commit) e caricamento (push) del codice su repository Git e GitHub, gestendo automaticamente l'inizializzazione del repo e la configurazione del remote se mancanti.

**Esempio di Utilizzo**:
> `/commit ho finito la funzionalità di login, salva tutto.`

## Istruzioni Operative per l'Agente

1. **Analisi dello Stato di Git**:
   - Esegui `git status` per verificare se la directory corrente è un repository Git.
   - **Se NON è un repository Git:**
     - Chiedi all'utente se desidera inizializzarlo ora con `git init`. Attendi la sua conferma.
     - Se accetta, esegui `git init`.

2. **Controllo e Configurazione del Remote**:
   - Esegui `git remote -v` per verificare se è impostato un remote (es. `origin`).
   - **Se il remote NON è presente:**
     - Spiega all'utente che non c'è un repository remoto collegato.
     - Chiedigli se desidera fornire l'URL di un repository remoto vuoto (es. su GitHub) OPPURE se preferisce mantenere tutto solo in locale.
     - **Fermati qui e attendi** la risposta dell'utente.
     - Se fornisce un URL, esegui: `git remote add origin <URL>` e, se il branch locale si chiama `master`, chiedi se vuole rinominarlo in `main` (`git branch -M main`).
     - Se preferisce mantenere tutto in locale, procedi normalmente ma ricorda di saltare la fase di `git push` alla fine.

3. **Generazione della Bozza del Messaggio**:
   - Analizza le modifiche recenti ai file usando le tue capacità o con `git status` / `git diff`.
   - Genera una bozza descrittiva e dettagliata delle modifiche.
   - Crea un file chiamato `commit_msg.txt` nella radice del progetto e scrivici dentro questa bozza usando i tuoi strumenti di scrittura.

4. **Richiesta di Revisione Umana**:
   - Informa l'utente che la bozza è pronta in `commit_msg.txt`.
   - Chiedi all'utente di aprire il file, correggerlo/integrarlo e dirti quando ha finito (es. digitando "Ok" o "Procedi").
   - **Fermati qui e attendi** l'ok esplicito dell'utente.

5. **Esecuzione di Commit e Push**:
   - Una volta ottenuto l'ok, esegui i seguenti comandi nel terminale (in PowerShell ricorda di separarli con `;`):
     - `git add .`
     - `git reset HEAD commit_msg.txt` oppure `git restore --staged commit_msg.txt` (per evitare che il file della bozza venga incluso nel commit)
     - `git commit -F commit_msg.txt`
   - Se il repository ha un remote configurato, esegui il push. Se è il primo push assoluto sul remote, esegui `git push -u origin <nome-branch>` (di solito `main`). Altrimenti esegui un semplice `git push`. Se il repository è solo locale, salta questo passaggio.

6. **Conferma Finale e Pulizia**:
   - Rimuovi definitivamente il file `commit_msg.txt` temporaneo tramite comando (es. `Remove-Item commit_msg.txt` o `rm commit_msg.txt`).
   - Comunica all'utente l'esito del push confermando che la pulizia è stata completata.
