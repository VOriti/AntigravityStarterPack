# 🚀 Antigravity: Manuale di Riferimento

Benvenuto nel manuale di riferimento completo per l'ecosistema **Antigravity** (IDE e App 2.0 Standalone), la piattaforma di sviluppo e ricerca AI-first.
Questo manuale fornisce documentazione tecnica approfondita per ricercatori, sviluppatori e utenti avanzati che vogliono padroneggiare il Vibecoding.

---

## 📚 Guide Rapide e Risorse dello Starter Pack

- [Guida di Base - IDE (Starter Guide)](./guida_di_base_ide.md): La guida essenziale per iniziare a sviluppare nell'IDE.
- [Guida di Base - App (Starter Guide)](./guida_di_base_app.md): La guida essenziale per task autonomi e Generative UI nell'App 2.0.
- [Libreria dei Workflow](./Descrizione-workflows.md): Catalogo completo delle automazioni e degli slash command su misura.
- [Libreria delle Regole (Rules)](./Descrizione-rules.md): Catalogo delle regole Always-On di sicurezza, architettura e qualità.

---

## 📖 Indice Generale dei 12 Capitoli

### 1. [Concetti Fondamentali & Architettura](./antigravity_reference_manual/01_core_concepts.md)
* 1.1 Antigravity IDE vs Antigravity App (Standalone)
* 1.2 Il Paradigma del Vibecoding
* 1.3 Gestione della Finestra di Contesto (Context Window)
* 1.4 Autonomia dell'Agente e Permessi
* 1.5 Il Sistema di Personalizzazione
* 1.6 Creare e Salvare un Workspace

### 2. [Riferimento degli Slash Command](./antigravity_reference_manual/02_slash_commands.md)
* 2.1 `/goal` *(Task complessi autonomi)*
* 2.2 `/plan` *(Piani atomici step-by-step)*
* 2.3 `/grill-me` *(Intervista e chiarimento requisiti)*
* 2.4 `/learn` *(Salvataggio regole e skill apprese)*
* 2.5 `/boost` *(Deep reasoning multi-agente)* `[Solo App 2.0]`
* 2.6 `/teamwork-preview` *(Orchestrazione multi-milestone)* `[Solo App 2.0]`
* 2.7 `/schedule` *(Timer e automazioni ricorrenti)* `[Solo App 2.0]`

### 3. [Workflow & Automazioni](./antigravity_reference_manual/03_workflows.md)
* 3.1 Gestione dei Workflow (e Transizione a Skills)
* 3.2 Workflow Pre-costruiti dello Starter Pack
* 3.3 Collegamento con la [Libreria dei Workflow](./Descrizione-workflows.md)

### 4. [Regole (Rules): Sintassi, Scoping e Guida Pratica](./antigravity_reference_manual/04_rules.md)
* 4.1 Cosa sono le Regole e Perché Usarle (Always-On vs On-Demand)
* 4.2 Dove Posizionare le Regole: Scoping e Gerarchia Pratica (`.agents/rules/`, `GEMINI.md`)
* 4.3 Sintassi, Stile, Best Practice e Inclusioni Modulari con direttiva `@[Label](path)`
* 4.4 Catalogo di Regole Pronte all'Uso (6 Snippet Reali: Globale, Workspace, Modulo Locale)
* 4.5 Tutorial Pratico: Creare e Testare la Tua Prima Regola Step-by-Step
* 4.6 Checklist Operativa di Troubleshooting per le Regole (Limite 24 KB e Budget Token)
* 4.7 La Cartella "Rules di Esempio" e Risorse Collegate ([Libreria delle Regole](./Descrizione-rules.md))

### 5. [Personalizzazioni Pratiche: Skills, Hooks e Plugin](./antigravity_reference_manual/05_practical_customizations.md)
* 5.1 Panoramica degli Strumenti di Personalizzazione
* 5.2 Le Skill Operative: Runbook Multi-Step per l'Agente
* 5.3 Lifecycle Hooks: Guardrail e Automazioni con Script Reali
* 5.4 I Plugin: Pacchettizzare e Condividere Personalizzazioni
* 5.5 Checklist di Troubleshooting per Hooks, Skills e Plugin

### 6. [[AVANZATO] Architettura del Sistema, Context Window e Configurazione Globale](./antigravity_reference_manual/06_advanced_architecture.md)
* 6.1 Architettura Globale del Sistema di Personalizzazione
* 6.2 La Finestra di Contesto (Context Window): Teoria e Meccanica Interna
* 6.3 La Configurazione Globale (`~/.gemini/config/`): Ruolo di Sistema e Anatomia
* 6.4 Il Contratto Protojson e la Macchina a Stati dei Lifecycle Hooks
* 6.5 Troubleshooting Sistemistico e Architetturale

### 7. [Guida per i Ricercatori: Vibecoding Scientifico e Analisi Dati](./antigravity_reference_manual/07_researcher_guide.md)
* 7.1 Introduzione: Il Ricercatore Aumentato (PI vs Agente, Sovranità del Dato, Matrice IDE vs App 2.0)
* 7.2 L'Ecosistema Scientifico di Antigravity (Progressive Disclosure e Catalogo delle 35 Skill Native)
* 7.3 Guida Operativa alla Privacy: Conformità GDPR/HIPAA (De-identificazione e Setup Sensibile)
* 7.4 Tutorial 1: Literature Discovery, Sintesi Critica & BibTeX
* 7.5 Tutorial 2: Automated EDA e Grafici Publication-Ready
* 7.6 Use-Case Complessi (Biologia Computazionale, Systematic Review, Grant Proposal)
* 7.7 Best Practice Operative & Prompt Engineering per la Ricerca
* 7.8 Tabella Sinottica e Cheat-Sheet per Ricercatori

### 8. [Guida per gli Sviluppatori: Ingegneria del Software & Vibecoding Avanzato](./antigravity_reference_manual/08_developer_guide.md)
* 8.1 Filosofia & Modello Mentale: Dal Coding Manuale all'AI Orchestration (Ruolo, Matrice IDE vs App 2.0, Feedback Loop)
* 8.2 Pattern di Prompting & Tecniche di Interazione (Anatomia del Developer Prompt, File Context `@`)
* 8.3 Tutorial 1: Sviluppo Feature End-to-End con TDD
* 8.4 Tutorial 2: Archeologia di Legacy Code e Refactoring
* 8.5 Scenari Complessi & Architetture Enterprise (Hexagonal/Clean, Event-Driven, Microservizi)
* 8.6 Terminale Avanzato & Debugging Operativo
* 8.7 Tutorial Privacy: Modelli Locali & Ambiente Air-Gapped
* 8.8 Best Practice Operative & Anti-Pattern da Evitare
* 8.9 Riferimenti Incrociati & Risorse Correlate

### 9. [Server MCP (Model Context Protocol): Integrazione e Strumenti Esterni](./antigravity_reference_manual/09_mcp_servers.md)
* 9.1 Architettura dello Standard MCP e Paradigma JSON-RPC 2.0
* 9.2 Meccanismi di Trasporto & Connettività: stdio vs SSE
* 9.3 Configurazione Gerarchica & Precedenze (`mcp_config.json`)
* 9.4 Politiche di Caricamento: Eager vs Lazy Loading
* 9.5 Tutorial 1: Integrazione Database Relazionale (SQLite & PostgreSQL)
* 9.6 Tutorial 2: Accesso Filesystem Scoped & Automazione GitHub
* 9.7 Diagnostica, Troubleshooting & Sicurezza

### 10. [Uso in Locale: Modelli Offline, Hardware Setup e Privacy Assoluta](./antigravity_reference_manual/10_local_usage.md)
* 10.1 Filosofia, Sovranità dei Dati e Conformità Normativa (GDPR, HIPAA, Zero-Cloud)
* 10.2 Configurazione dei Backend LLM Locali (Ollama, vLLM, LM Studio)
* 10.3 Hardware Mapping & Calcolo Matematico della VRAM (Modelli, PagedAttention, KV Cache)
* 10.4 Configurazione di Antigravity per Provider Locali (`config.json`)
* 10.5 Air-Gapped, Switch Offline & Isolamento di Rete (Firewall e Sandbox)
* 10.6 Tutorial Step-by-Step: Workflow di Coding e Test 100% Offline
* 10.7 Best Practice di Prompt Engineering per Modelli Locali
* 10.8 Riferimenti Incrociati & Risorse Correlate

### 11. [Controllo Remoto: Cloud Web Control (antigravity.google.com), SSH & Headless](./antigravity_reference_manual/11_remote_control.md)
* 11.1 Il Paradigma del Controllo Remoto: Architettura Ibrida Host-Cloud
* 11.2 Metodo Principale: Cloud Control via `antigravity.google.com`
* 11.3 Procedura di Accoppiamento (Pairing) e Handshake Crittografico
* 11.4 La Dashboard Web: Monitoraggio Live e Interazione Remota
* 11.5 Human-in-the-Loop Remoto & Approvazione Push su Dispositivi Mobili
* 11.6 Sicurezza delle Sessioni, Token e Revoca Istantanea (Kill-Switch)
* 11.7 Metodi Secondari: Connessioni Headless SSH e Multiplexer (`tmux`)
* 11.8 Esecuzione Headless come Demone di Sistema (`systemd` e Servizi Windows)
* 11.9 Mesh VPN Privata e Tunneling (Tailscale & Cloudflare Zero Trust)
* 11.10 Tutorial Step-by-Step: Supervisione Mobile di un Refactoring Notturno
* 11.11 Matrice Comparativa dei Metodi di Controllo Remoto
* 11.12 Troubleshooting e Risoluzione dei Problemi

### 12. [Riferimento CLI (`agy`): Automazione da Terminale, Scripting e CI/CD](./antigravity_reference_manual/12_cli_reference.md)
* 12.1 Introduzione & Architettura della CLI `agy` (Installazione su Linux, macOS, Windows)
* 12.2 Tassonomia dei Comandi Principali (`prompt`, `run`, `init`, `doctor`, `mcp`, `remote`, `config`, `auth`)
* 12.3 Matrice Esaustiva delle Opzioni e Flag Globali
* 12.4 Variabili d'Ambiente e Gerarchia di Risoluzione
* 12.5 Contratti dei Codici di Uscita Deterministici (Exit Codes)
* 12.6 Formati di Output Strutturati per l'Automazione (JSON Envelope, NDJSON Stream, SARIF)
* 12.7 Workflow Terminali Completi Dual-Language (Bash & PowerShell: CI/CD Gate, Batch, Init, Doctor)
* 12.8 Best Practice Operative per la CLI

---

*Nota: Questo manuale è inteso come riferimento dettagliato. Per un'introduzione rapida, consulta le Guide di Base.*

[⬅️ Torna all'inizio](README.md)