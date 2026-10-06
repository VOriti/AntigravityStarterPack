# Workflow: /scaffold-component — Scaffolding Componenti UI Frontend

**Descrizione**: Guida la creazione rapida di un nuovo componente UI frontend completo di interfaccia TypeScript, file di stile modulare e test unitario.

**Esempio di Utilizzo**:
> `/scaffold-component crea un componente Button in React con varianti primary e secondary, stili CSS Modules e test Jest.`

## Istruzioni Operative per l'Agente

1. **Dettagli del Componente**: Chiedi all'utente il nome e lo scopo del componente. Se la logica o la UI del componente sono complesse, raccomanda all'utente di digitare `/grill-me` per un'intervista interattiva su props necessarie, gestione dello stato e librerie UI preferite. Spiega all'utente che se acconsente, interromperai l'esecuzione affinché possa riavviare il workflow con `/grill-me`. In caso contrario, procedi con valori predefiniti sensati per il componente entro il budget di token corrente.
   *(Direttiva runtime: Explain to the user that if they agree, you will halt execution so they can rerun the workflow with `/grill-me`. Otherwise, proceed with sensible component defaults within the current token budget.)*
2. **Contesto del Framework**: Ispeziona il progetto per rilevare il framework frontend (React, Vue, Svelte), la strategia di stile (CSS Modules, Tailwind CSS, Styled Components) e la presenza di TypeScript.
3. **Pianificazione e Anteprima**: Genera un Artifact di piano di implementazione dettagliando l'interfaccia delle props, i file da creare e il layout di base. Richiedi l'approvazione esplicita dell'utente prima di generare qualsiasi file.
4. **Generazione File**: Una volta approvato il piano, usa i tool di creazione file per generare:
   - Il file principale del componente (es. `ComponentName.tsx`).
   - Il file di stile associato (es. `ComponentName.module.css`).
   - Il file di test unitario con asserzioni base di rendering (es. `ComponentName.test.tsx`).
5. **Integrazione ed Esportazione**: Esporta il nuovo componente dal file indice di riferimento (`index.ts`, se applicabile) e notifica all'utente che il componente è pronto all'uso.
