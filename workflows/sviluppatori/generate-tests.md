# Workflow: /generate-tests — Generazione Automatica Test Unitari

**Descrizione**: Genera ed esegue automaticamente suite complete di test unitari per moduli o componenti, correggendo iterativamente fallimenti di codice o di test fino al successo.

**Esempio di Utilizzo**:
> `/generate-tests crea i test unitari con pytest per il modulo auth/jwt_service.py coprendo casi limite ed errori.`

## Istruzioni Operative per l'Agente

1. **Identificazione del Target**: Chiedi all'utente quale file, funzione o modulo richiede la suite di test.
2. **Rilevamento del Framework**: Ispeziona il progetto per determinare il framework di test in uso (es. pytest, Jest, Vitest, JUnit, PHPUnit).
3. **Scrittura dei Casi di Test**: Scrivi i test unitari coprendo:
   - Happy path (comportamento standard atteso).
   - Casi limite (*edge cases*: input nulli, array vuoti, valori fuori scala).
   - Gestione degli errori (eccezioni sollevate e codici di errore attesi).
4. **Esecuzione dei Test**: Lancia il comando di esecuzione dei test nel terminale dell'ambiente di lavoro.
5. **Iterazione e Auto-Debugging**: In caso di fallimento di uno o più test, genera un Artifact con l'analisi degli errori e proponi le modifiche. Con il consenso dell'utente, correggi iterativamente l'implementazione o i test fino a quando tutti i test passano con successo.
