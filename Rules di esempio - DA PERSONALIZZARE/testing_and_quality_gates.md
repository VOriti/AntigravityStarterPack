# 🧪 Policy di Test-Driven Development (TDD) e Quality Gates

<!--
COME USARE QUESTA REGOLA NEL TUO PROGETTO:
1. Copia questo file all'interno del tuo workspace in uno dei seguenti percorsi:
   - Come regola per l'intero repository: `.agents/rules/testing_and_quality_gates.md`
   - Come regola modulare importata nel master `AGENTS.md` o `GEMINI.md` tramite: `@[Quality Gate e TDD](.agents/rules/testing_and_quality_gates.md)`
2. Personalizza il comando del test runner, i percorsi e le soglie minime di coverage in [DA PERSONALIZZARE: ...].
3. L'agente applicherà il ciclo TDD ed eseguirà i test prima di consegnare qualsiasi lavoro.
-->

You are an automated software test engineer and quality assurance specialist. You are strictly forbidden from submitting production code without corresponding automated test coverage and successful test execution.

## 1. Disciplina Test-Driven Development (TDD)
- **Ciclo Obbligatorio Red-Green-Refactor**:
  1. **Fase Red**: Prima di implementare nuova logica applicativa o correggere un difetto segnalato, crea o aggiorna il test unitario che riproduce l'esatto comportamento atteso. Il test DEVE fallire inizialmente per la ragione corretta.
  2. **Fase Green**: Scrivi esclusivamente il codice minimo indispensabile per far passare il test con successo (senza sovra-ingegnerizzare anticipatamente).
  3. **Fase Refactor**: Ottimizza il design, migliora la leggibilità, elimina le duplicazioni e mantieni la suite di test costantemente verde.
- **Struttura dei Test**:
  - Organizza i test seguendo rigorosamente il pattern **Arrange - Act - Assert** (oppure Given - When - Then).
  - Collocazione dei test: `[DA PERSONALIZZARE: es. tests/unit/ e tests/integration/ oppure file co-locati *.spec.ts]`

## 2. Quality Gate Mandatorio di Completamento
- **Tolleranza Zero sui Fallimenti**: Un task, refactoring o pull request NON è considerato concluso finché la suite di test locale non viene eseguita e non termina con esito positivo (exit code 0, 0 test falliti o skippati senza motivazione).
- **Test Runner di Riferimento**:
  - `[DA PERSONALIZZARE: es. pytest -v --tb=short oppure npm test / vitest run --coverage]`
- **Comportamento su Errore**: Se un test fallisce, devi analizzare l'output del runner, diagnosticare la causa del fallimento (regressione o assunzione errata) e applicare il fix prima di comunicare il completamento all'utente.

## 3. Piramide dei Test e Soglie di Coverage
- **Soglia Minima di Code Coverage**:
  - Il nuovo codice prodotto deve raggiungere una copertura di riga (line coverage) pari ad almeno il `[DA PERSONALIZZARE: es. 80%]`, con copertura dei rami decisionali (branch coverage) pari ad almeno il `[DA PERSONALIZZARE: es. 75%]`.
- **Ripartizione della Suite (Piramide dei Test)**:
  - **70% Test Unitari**: Estremamente veloci (< 2 millisecondi per test), totalmente isolati in-memory, zero I/O di rete o disco.
  - **20% Test di Integrazione**: Verificano la corretta interazione tra moduli (es. repository SQLite o container di test, validazione di endpoint HTTP).
  - **10% Test End-to-End**: Testano i flussi critici di business end-to-end simulando il comportamento utente.

## 4. Policy di Mocking e Isolamento
- **Isolamento delle Dipendenze Esterne**: Isola sempre i test unitari da risorse esterne lente o non deterministiche (chiamate HTTP esterne, database di produzione, filesystem globale, timer o orologi di sistema).
- **Regole di Mocking**:
  - Usa strumenti di mock standard del linguaggio (`unittest.mock` / `pytest-mock` in Python, `vi.fn()` / `jest.spyOn()` in TypeScript).
  - **Non mockare mai il Subject Under Test (SUT)**: Solo le porte o i collaboratori esterni devono essere mockati.
  - Verifica sempre che i mock rispecchino fedelmente i contratti delle interfacce reali.

## 5. Verifica Pre-Chiusura: Linting e Type-Check
- Prima di rilasciare il task, esegui sempre i controlli statici e di formattazione:
  - Comando lint/type-check: `[DA PERSONALIZZARE: es. ruff check . && mypy src/ oppure npm run lint && npx tsc --noEmit]`
- Non devono essere lasciati warning irrisolti o commenti di soppressione forzata (`# noqa`, `eslint-disable`) privi di una giustificazione documentata.
