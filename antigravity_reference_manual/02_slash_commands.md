# 2. Riferimento degli Slash Command

Gli slash command attivano comportamenti specializzati o macro nella chat di Antigravity.
**NOTA**: gli slash command sono presenti di base in Antigravity, ma possono essere personalizzati o estesi tramite i workflow.
Alcuni di questi comandi sono universali (disponibili sia nell'IDE che nell'App Standalone), mentre altri sono esclusivi di Antigravity 2.0 App Standalone.

## 2.1 /goal *(Universale)*
**Caso d'uso**: Task lunghi, complessi o noiosi (es. refactoring di 50 file, test esaustivi).
**Comportamento**: Attiva la modalità "Goal". L'agente non si fermerà finché non sarà assolutamente certo che l'obiettivo sia completato. Lavora autonomamente, correggendo i propri errori. Perfetto per i task notturni.
**Esempio**: `/goal migra tutte le chiamate API in questo progetto da REST a GraphQL. Se trovi errori durante i test, correggili finché l'intera test suite non passa con successo.`

## 2.2 /plan *(Universale)*
**Caso d'uso**: Prima di scrivere codice per funzionalità complesse.
**Comportamento**: Forza l'agente a fermarsi e creare un "Implementation Plan" dettagliato e passo-passo prima di toccare qualsiasi file. Il piano deve essere revisionato e approvato dall'utente.
**Esempio**: `/plan dobbiamo implementare un sistema di autenticazione a due fattori (2FA) basato su TOTP. Analizza il backend e proponi l'architettura del database e le rotte necessarie.`

## 2.3 /grill-me *(Universale)*
**Caso d'uso**: Brainstorming o chiarimento di requisiti ambigui.
**Comportamento**: Inverte i ruoli. L'agente ti farà domande mirate tramite modal interattivi su design, architettura, casi limite e preferenze, finché non avrà un piano infallibile.
**Esempio**: `/grill-me voglio creare un nuovo componente per la dashboard utente in React, fammi tutte le domande necessarie per capire esattamente come lo voglio.`

## 2.4 /learn *(Universale)*
**Caso d'uso**: Mantenere un workflow o una regola di successo.
**Comportamento**: Salva il workflow o la regola di successo recente nel sistema (come Skill del Workspace o regola globale) in modo che l'agente lo ricordi per i task futuri.
**Esempio**: `/learn da ora in poi, ogni volta che generi un componente Vue, ricordati di utilizzare sempre la Composition API con \`<script setup>\`. Salva questa regola per il futuro.`

## 2.5 /boost **`[SOLO ANTIGRAVITY 2.0]`** 🔒 *(Piani a pagamento)*
**Caso d'uso**: Bug complessi, algoritmi difficili, refactoring profondo.
**Comportamento**: Avvia una pipeline di ragionamento multi-agente a 3 fasi: un Orchestratore principale decompone il problema in sotto-task, dei subagent specializzati lavorano in parallelo su scopi isolati (implementazione, debug, verifica), e infine la soluzione viene aggregata e validata con test locali prima di essere consegnata. Perfetto per problemi dove l'agente standard non riesce ad andare a fondo.
**Nota**: Se non vedi questo comando nella chat, potrebbe dipendere dal modello attivo. Consulta la [documentazione ufficiale](https://antigravity.google.com/docs/boost) per i dettagli.
**Esempio**: `/boost questo algoritmo di parsing ha una race condition che non riesco a individuare. Analizza il call graph e trova la causa radice.`

## 2.6 /teamwork-preview **`[SOLO ANTIGRAVITY 2.0]`** 🔒 *(Piani a pagamento)*
**Caso d'uso**: Progetti grandi e autonomi che richiedono ore o giorni di lavoro.
**Comportamento**: Lancia un team di agenti specializzati che lavorano in parallelo su milestone separate, con worktree isolati e verifica indipendente. Prima di iniziare, conduce un'intervista a due fasi per scomporre l'obiettivo in milestone precise.
**Nota**: Diversamente da `/boost` (che risolve problemi precisi in sessione), `/teamwork-preview` è pensato per grandi obiettivi autonomi "fire-and-forget".
**Esempio**: `/teamwork-preview migra l'intera codebase da JavaScript vanilla a TypeScript. Gestisci un milestone per ogni modulo e dimmi quando è tutto verificato.`


## 2.7 /schedule **`[SOLO ANTIGRAVITY 2.0]`**
**Caso d'uso**: Controlli periodici o promemoria.
**Comportamento**: Imposta un timer o un cron job (es. `*/5 * * * * controlla lo stato del deploy`). L'agente viene eseguito come task in background e ti notifica al completamento.
**Esempio**: `/schedule tra 30 minuti esegui i test di integrazione, controlla l'esito e avvisami se ci sono dei test falliti.`


---
[⬅️ Torna all'Indice](../index.md) | [Capitolo Precedente: 1. Concetti Fondamentali & Architettura](./01_core_concepts.md) | [Prossimo Capitolo: 3. Workflow & Automazioni](./03_workflows.md)
