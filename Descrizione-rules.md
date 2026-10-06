# 📜 Tutte le Regole (Rules) di Esempio dello Starter Pack

Questo Starter Pack include una selezione di **Regole (Rules)** pronte all'uso, progettate su misura per orientare, vincolare e potenziare il comportamento dell'agente IA durante lo sviluppo software e la ricerca accademica.

---

## 💡 Cosa sono le Rules e in cosa differiscono da Workflow e Skill?

| Meccanismo | Come si attiva | Quando usarlo | Esempio tipico |
|---|---|---|---|
| **Workflow** (`.agents/workflows/`) | Manualmente via slash command (`/commit`) | Task procedurali ripetitivi su richiesta dell'utente | `/generate-tests`, `/clean-code` |
| **Skill** (`.agents/skills/<nome>/SKILL.md`) | On-Demand (l'agente la attiva se serve) | Runbook specialistici complessi con documentazione | Query su PubMed, predizione 3D con PyMOL |
| **Regola (Rule)** (`.agents/rules/`, `GEMINI.md`) | **Always-On (sempre attiva in background)** | Guardrail, vincoli di sicurezza, stile e quality gate | Protezione privacy, Clean Architecture, TDD |

Le regole non richiedono comandi nella chat: **operano silenziosamente ad ogni interazione**, forzando l'agente a rispettare i tuoi standard fin dalla prima riga di codice generata.

---

## 🚀 Come Utilizzare le Regole nel Tuo Progetto

1. **Scegli la regola** più adatta alle tue necessità dalla cartella `Rules di esempio - DA PERSONALIZZARE/`.
2. **Copia il file `.md`** all'interno del tuo workspace in uno dei seguenti percorsi:
   - **Per l'intero progetto (condiviso con il team via Git):** copia il file in `.agents/rules/<nome-regola>.md` oppure includilo/incollalo nel file root `GEMINI.md` o `AGENTS.md`.
   - **Per un singolo modulo specifico:** incollalo come `GEMINI.md` all'interno della sottocartella desiderata (es. `src/api/GEMINI.md`). Grazie al *Directory Walk-Up*, la regola agirà solo su quel modulo!
   - **Per tutti i tuoi progetti personali sulla macchina:** copialo nella cartella utente globale `~/.gemini/config/GEMINI.md` (su Windows: `%USERPROFILE%\.gemini\config\GEMINI.md`).
3. **Personalizza i placeholder**: Apri il file copiato e compila i campi indicati tra parentesi quadre `[DA PERSONALIZZARE: ...]`.
4. **Attivazione immediata**: Non serve riavviare l'IDE! L'agente leggerà la regola alla prossima iterazione.

---

## 📋 Catalogo delle Regole Disponibili

| File Regola | Ambito Principale | Badge Categoria | Focus & Guardrail |
|---|---|---|---|
| [`privacy_and_security.md`](Rules%20di%20esempio%20-%20DA%20PERSONALIZZARE/privacy_and_security.md) | Sicurezza & Governance | `[Sicurezza & Privacy]` **(Critico)** | Protezione PHI/PII, divieto comandi distruttivi, blocco leak API keys, isolamento locale. |
| [`coding_style_and_standards.md`](Rules%20di%20esempio%20-%20DA%20PERSONALIZZARE/coding_style_and_standards.md) | Ingegneria del Software | `[Qualità Codice]` **(Fondamentale)** | Clean Architecture, tipizzazione forte (TS/Python), gestione errori, Conventional Commits. |
| [`testing_and_quality_gates.md`](Rules%20di%20esempio%20-%20DA%20PERSONALIZZARE/testing_and_quality_gates.md) | QA & Pipeline CI | `[Testing & TDD]` **(Quality Gate)** | Politica TDD rigorosa, stop al completamento su test falliti, coverage minima >= 80%. |
| [`scientific_research_and_data.md`](Rules%20di%20esempio%20-%20DA%20PERSONALIZZARE/scientific_research_and_data.md) | Ricerca Scientifica & Dati | `[Scienza & Dati]` **(Riproducibilità)** | Seed deterministici fissi, immutabilità dati grezzi, grafici 300 DPI, rigore citazionale BibTeX. |

---

## 0. Fondamentali & Sicurezza (`Rules di esempio - DA PERSONALIZZARE/`)

Direttive essenziali per proteggere la privacy dei dati sensibili e prevenire danni accidentali al filesystem o al repository:

- [`privacy_and_security.md`](Rules%20di%20esempio%20-%20DA%20PERSONALIZZARE/privacy_and_security.md) `[Sicurezza & Privacy]` **(Critico)**: 
  Impedisce la fuga di dati sensibili (PII, dati sanitari PHI) verso l'esterno, vieta tassativamente comandi terminale distruttivi (`rm -rf /`, `git reset --hard`, `DROP DATABASE`) e forza l'agente a operare in modalità sicura e local-first.
  **Esempio di intervento**: L'agente rifiuterà di eseguire una pulizia con `rm -rf` o di inviare frammenti contenenti token segreti o cartelle cliniche senza prima aver applicato la de-identificazione locale.

---

## 1. Sviluppatori (`Rules di esempio - DA PERSONALIZZARE/`)

Standard architetturali e di qualità per garantire codice manutenibile, testabile e conforme alle best practice enterprise:

### 🏗️ Architettura & Stile
- [`coding_style_and_standards.md`](Rules%20di%20esempio%20-%20DA%20PERSONALIZZARE/coding_style_and_standards.md) `[Qualità Codice]` **(Fondamentale)**: 
  Fissa i confini architetturali (Clean Architecture, separazione Dominio/Infrastruttura), impone il tipaggio statico rigoroso (TypeScript strict, Python type hints), vieta i blocchi try/catch vuoti e standardizza i messaggi di commit secondo le specifiche Conventional Commits.
  **Esempio di intervento**: Se chiedi all'agente di aggiungere un endpoint, creerà automaticamente entità pure di dominio, DTO validati e adapter disaccoppiati invece di una classe monolitica.

### 🧪 Testing & Continuous Integration
- [`testing_and_quality_gates.md`](Rules%20di%20esempio%20-%20DA%20PERSONALIZZARE/testing_and_quality_gates.md) `[Testing & TDD]` **(Quality Gate)**: 
  Impone il ciclo Test-Driven Development (Red-Green-Refactor). Introduce un blocco esplicito: l'agente non può dichiarare un task concluso se i test locali falliscono o se la code coverage scende sotto la soglia definita.
  **Esempio di intervento**: L'agente scriverà prima il test unitario che fallisce, implementerà la soluzione, eseguirà il test runner nel terminale e ti presenterà la soluzione solo dopo l'esito verde.

---

## 2. Ricercatori & Data Analyst (`Rules di esempio - DA PERSONALIZZARE/`)

Progettate specificamente per garantire l'integrità scientifica, la riproducibilità numerica e la qualità editoriale dei risultati:

### 🧬 Metodo Scientifico & Integrità del Dato
- [`scientific_research_and_data.md`](Rules%20di%20esempio%20-%20DA%20PERSONALIZZARE/scientific_research_and_data.md) `[Scienza & Dati]` **(Riproducibilità)**: 
  Stabilisce l'immutabilità assoluta dei file di dati grezzi (`data/raw/`), obbliga a fissare i seed pseudo-casuali (es. `seed=42`) in ogni script, impone grafici ad alta risoluzione (300 DPI vettoriali o PNG) con etichette chiare e richiede sempre il riferimento DOI o BibTeX per ogni paper citato.
  **Esempio di intervento**: Quando chiedi di analizzare un dataset CSV, l'agente creerà uno script in `src/` che scrive l'output in `data/processed/`, salvando il grafico a 300 DPI senza toccare il file di origine.

---

## 🧩 Composizione Modulare tramite Direttiva `@[Label](path)`

Per mantenere i file snelli e rispettare il limite fisico di **24 KB** per file, puoi creare un file master `AGENTS.md` o `GEMINI.md` nella root del tuo progetto che importa le regole modulari:

```markdown
# Regole di Progetto Master

@[Sicurezza e Privacy](.agents/rules/privacy_and_security.md)
@[Standard di Codice](.agents/rules/coding_style_and_standards.md)
@[Quality Gate e TDD](.agents/rules/testing_and_quality_gates.md)

## Direttive Specifiche del Repository
- [Aggiungi qui eventuali direttive uniche del tuo team]
```

---

[🏠 Torna all'inizio](README.md)

[🏠 Torna all'Indice del Manuale](index.md)

[📖 Consulta la Guida Completa alle Regole (Capitolo 4)](antigravity_reference_manual/04_rules.md)
