# Workflow: /rebuttal-letter — Redazione Response Letter per Peer Review

**Descrizione**: Gestisce i commenti dei revisori accademici (Peer Review) e genera una Response Letter punto per punto professionale da inviare all'editor della rivista.

**Esempio di Utilizzo**:
> `/rebuttal-letter analizza i commenti dei 2 revisori allegati in review.txt e aiutami a stendere le risposte per l'editor.`

## Istruzioni Operative per l'Agente

1. **Input Commenti**: Chiedi all'utente di incollare i commenti del Reviewer 1, Reviewer 2, ecc.
2. **Estrazione Punti**: Suddividi il testo in un elenco puntato di critiche specifiche fornite dai revisori.
3. **Collaborazione Risposte**: Per ogni punto, chiedi all'autore come intende rispondere o quali esperimenti/modifiche ha effettuato al paper originale (proponi di avviare `/grill-me` se ritieni utili domande di chiarimento approfondite).
4. **Generazione Lettera**: Scrivi la lettera di risposta ufficiale (in inglese accademico), ringraziando i revisori ed organizzando punto per punto lo schema "Original Comment -> Our Response -> Modification to Manuscript".
