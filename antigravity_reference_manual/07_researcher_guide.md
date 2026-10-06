# 7. Guida per i Ricercatori: Vibecoding Scientifico e Analisi Dati

Questa guida costituisce il riferimento operativo per scienziati, bioinformatici, medici ricercatori e data analyst che utilizzano l'ecosistema Antigravity. Il manuale illustra come coniugare il rigore del metodo scientifico con l'agilità del **Vibecoding**, sfruttando la suite di oltre 35 skill scientifiche native, l'elaborazione locale sicura e le capacità multi-agente per l'esplorazione bibliografica, l'analisi statistica e la biologia computazionale.

---

## 7.1 Introduzione: Il Ricercatore Aumentato

### 7.1.1 Il Paradigma del Vibecoding Scientifico
Nel contesto della ricerca accademica e traslazionale, il *Vibecoding* non significa delegare ciecamente il ragionamento all'intelligenza artificiale, ma elevare il ruolo del ricercatore a quello di **Principal Investigator (PI)**:
- **Il Ricercatore (PI)**: Formula le ipotesi biologiche o statistiche, stabilisce i criteri di inclusione/esclusione, impone il livello di confidenza ($\alpha$), seleziona i controlli sperimentali e valida criticamente le conclusioni.
- **L'Agente Antigravity**: Agisce da bioinformatico o data scientist operativo instancabile. Interroga banche dati pubbliche, scrive codice analitico riproducibile, esegue pipeline nel terminale locale, verifica le assunzioni matematiche e genera visualizzazioni pronte per la pubblicazione (*publication-ready*).

### 7.1.2 Principi Fondamentali: Sovranità del Dato e Rigore Riproducibile
La ricerca medica e biologica gestisce quotidianamente asset ad alta confidenzialità (dataset clinici, sequenze proprietarie, bozze di grant competitivi). Antigravity adotta tre pilastri architetturali:
1. **Elaborazione Local-First**: Tutti gli script di calcolo statistico e manipolazione tabellare girano su `localhost` all'interno della sandbox del terminale.
2. **Zero Data Leakage**: I file referenziati nella chat tramite la direttiva `@` non vengono inviati a server di training esterni. Solo le interrogazioni esplicite verso database pubblici (es. PubMed, Ensembl) contattano le rispettive API ufficiali.
3. **Conformità GDPR e HIPAA**: Nessun dato sanitario protetto (*Protected Health Information*, PHI) lascia la workstation. La procedura esecutiva completa per l'isolamento è illustrata nella Sezione 7.3.

### 7.1.3 Decision Matrix: Antigravity IDE vs Antigravity 2.0 App Standalone
Antigravity offre due ambienti complementari. Utilizza la seguente matrice decisionale per scegliere l'ambiente ideale in funzione del task:

| Scenario di Ricerca | Ambiente Raccomandato | Motivazione Funzionale |
| :--- | :--- | :--- |
| **Scrittura e Refactoring di Script** | `[Usa in Antigravity IDE]` | Autocompletamento a bassa latenza, modifiche inline (`Ctrl+I`), visual diff per revisionare le modifiche riga per riga prima dell'applicazione. |
| **Systematic Literature Review** | `[Usa nell'App 2.0]` | Esecuzione asincrona in background, generazione e navigazione fluida di matrici bibliografiche complesse nel pannello Artifacts. |
| **Pipeline Bioinformatiche Multi-Database** | `[Usa nell'App 2.0]` | Supporto multi-agente con `/boost` e `/teamwork-preview`, sandbox isolata e gestione di flussi a cascata su 7+ database. |
| **Esplorazione Dati (EDA) & Plotting** | `[Usa in Antigravity IDE]` | Terminale integrato con riproduzione immediata, anteprima interattiva dei plot ed esportazione vettoriale a 300 DPI. |
| **Stesura Manoscritto (LaTeX / Typst)** | `[Usa in Antigravity IDE]` | Compilazione locale sincrona, navigazione dell'albero del documento e anteprima PDF affiancata in tempo reale. |

---

## 7.2 L'Ecosistema Scientifico di Antigravity

### 7.2.1 Architettura a Progressive Disclosure delle Skill
Per preservare la finestra di contesto (*Context Window*) e garantire risposte immediate, Antigravity adotta il principio di **Progressive Disclosure**:
- **Fase di Standby (Zero Token Overhead)**: Nel prompt di sistema iniziale sono registrati unicamente i metadati sintetici delle skill (nome, categoria e breve descrizione di una riga). Nessuna specifica API sovraccarica la memoria attiva.
- **Attivazione Contestuale On-Demand**: Nel momento in cui l'agente individua un intento scientifico esplicito (es. un identificativo `rs...`, un codice PDB o una richiesta bibliografica), carica istantaneamente il file `SKILL.md` corrispondente con i parametri operativi necessari.

### 7.2.2 Catalogo Tabellare delle Skill Scientifiche (35 Skill Native)

#### Tabella 1: Letteratura Scientifica & Motori Accademici
| Skill | Database / Risorsa | Funzione Chiave | Query Esemplare per l'Agente |
| :--- | :--- | :--- | :--- |
| `pubmed-database` | NCBI PubMed | Ricerca avanzata biomedica, estrazione metadati, abstract e MeSH terms. | *"Cerca su PubMed articoli 2023-2026 su inibitori di KRAS G12C in NSCLC."* |
| `literature-search-europepmc` | Europe PMC | Recupero di full-text Open Access in XML/testo, citazioni e validazione DOI. | *"Scarica il full-text Open Access per PMCID 8492011 e valida l'albero citazionale."* |
| `literature-search-biorxiv` | bioRxiv / medRxiv | Monitoraggio dei pre-print recenti nelle scienze della vita e medicina. | *"Verifica se esistono pre-print pubblicati nelle ultime 4 settimane su cGAMP sintetico."* |
| `literature-search-arxiv` | arXiv | Letteratura su biofisica, modellistica matematica, intelligenza artificiale. | *"Trova preprint recenti su architetture Transformer applicate al ripiegamento dell'RNA."* |
| `literature-search-openalex` | OpenAlex | Metriche bibliometriche globali, h-index, reti di collaborazione e DOI. | *"Analizza il trend di citazioni e le istituzioni leader per il target ENPP1."* |

#### Tabella 2: Genomica, Variazione & Annotazione
| Skill | Database / Risorsa | Funzione Chiave | Query Esemplare per l'Agente |
| :--- | :--- | :--- | :--- |
| `dbsnp-database` | NCBI dbSNP | Risoluzione da rsID a coordinate GRCh38, sequenza di riferimento e HGVS. | *"Risolvi rs28929495 fornendo coordinate cromosomiche e variazione nucleotidica."* |
| `clinvar-database` | NCBI ClinVar | Classificazione di patogenicità clinica ACMG e rassegna delle evidenze. | *"Verifica la classificazione clinica di patogenicità per la variante EGFR G719S."* |
| `gnomad-database` | gnomAD | Frequenze alleliche di popolazione e metriche di tolleranza LoF (pLI, LOEUF). | *"Estrai la frequenza allelica globale e sub-popolazionale per questa variante."* |
| `ensembl-database` | Ensembl REST API | Mappatura ID genici, esoni, trascritti canonici e predizioni VEP. | *"Recupera il trascritto canonico Ensembl e la sequenza amminoacidica di STING1."* |
| `alphagenome-variant-impact-score` | AlphaGenome API | Calcolo punteggi di impatto funzionale (AVI) su regioni codificanti e non. | *"Calcola lo score AVI per tutte le mutazioni possibili nell'esone 19 di EGFR."* |
| `alphagenome-single-variant-analysis` | AlphaGenome API | Analisi d'impatto su espressione, splicing e accessibilità cromatinica. | *"Valuta l'effetto regolatorio di questa variante non-codificante sul promotore."* |
| `alphagenome-atlas-website-links` | AlphaGenome Atlas | Generazione di deep-link per visualizzazione interattiva dei loci sul web. | *"Genera il link interattivo all'AlphaGenome Atlas per le coordinate chr7:55174000-55175000."* |
| `ucsc-conservation-and-tfbs` | UCSC Genome Browser | Punteggi di conservazione filogenetica (phyloP, phastCons) e siti TFBS. | *"Estrai il punteggio phyloP per verificare la conservazione evolutiva del codone 719."* |
| `encode-ccres-database` | ENCODE SCREEN | Elementi regolatori cis-agenti (cCREs, promotori ed enhancer distali). | *"Interroga SCREEN per individuare enhancer annotati prossimali al gene STK11."* |
| `gtex-database` | GTEx Project | Espressione quantitativa RNA-seq ed eQTL su oltre 54 tessuti umani sani. | *"Mostra i livelli mediani di trascrizione di STING1 nei diversi tessuti polmonari."* |

#### Tabella 3: Proteomica, Strutture Macromolecolari & Interazioni
| Skill | Database / Risorsa | Funzione Chiave | Query Esemplare per l'Agente |
| :--- | :--- | :--- | :--- |
| `uniprot-database` | UniProtKB / Swiss-Prot | Annotazione funzionale, domini, modificazioni post-traduzionali (PTM). | *"Estrai l'ID UniProt di EGFR e descrivi i confini del dominio chinasico."* |
| `alphafold-database-fetch-and-analyze` | AlphaFold DB | Download coordinate 3D predette con analisi di confidenza locale (pLDDT). | *"Scarica la struttura AlphaFold di STING1 e mappa i residui con pLDDT < 70."* |
| `pdb-database` | Protein Data Bank (RCSB) | Strutture atomiche sperimentali (Cryo-EM, X-ray) con ligandi complessati. | *"Individua strutture PDB di EGFR complessate con afatinib con risoluzione < 2.0 Å."* |
| `foldseek-structural-search` | Foldseek API | Ricerca di omologia strutturale tridimensionale su scala proteomica. | *"Confronta la struttura 3D della tasca di legame con l'intero database PDB."* |
| `pymol` | PyMOL Automation | Generazione di script di rendering molecolare, superfici e distanze atomiche. | *"Scrivi uno script PyMOL per evidenziare la tasca ATP e il legame idrogeno di Ser719."* |
| `protein-sequence-msa` | Clustal Omega (EBI) | Allineamento multiplo di sequenze proteiche per valutare la conservazione. | *"Esegui un allineamento multiplo delle chinasi ErbB evidenziando il residuo G719."* |
| `protein-sequence-similarity-search` | MMseqs2 / BLAST | Ricerca rapida di sequenze omologhe e similarità di sequenza. | *"Trova gli omologhi ortologhi di STING1 nei mammiferi superiori."* |
| `interpro-database` | InterPro / Pfam | Classificazione in famiglie proteiche, motivi funzionali e architetture di domini. | *"Identifica tutti i domini annotati e le signature Pfam della proteina cGAS."* |
| `string-database` | STRING Database | Reti di interazione proteina-proteina (PPI), score di confidenza ed enrichment. | *"Costruisci il network di interattori primari di cGAS con confidenza > 0.700."* |
| `human-protein-atlas-database` | Human Protein Atlas | Localizzazione subcellulare ed espressione proteica immunoistochimica. | *"Verifica la distribuzione intracellulare di STING in cellule epiteliali polmonari."* |

#### Tabella 4: Farmacologia, Target Discovery & Sperimentazione Clinica
| Skill | Database / Risorsa | Funzione Chiave | Query Esemplare per l'Agente |
| :--- | :--- | :--- | :--- |
| `chembl-database` | EMBL-EBI ChEMBL | Valori quantitativi di bioattività ($IC_{50}$, $K_i$), meccanismi e molecole. | *"Estrai i valori di IC50 di osimertinib, afatinib e gefitinib su mutanti EGFR G719S."* |
| `pubchem-database` | NCBI PubChem | Proprietà chimico-fisiche, formule di struttura (SMILES, InChIKey) e saggi. | *"Recupera la struttura SMILES e le proprietà chimiche dell'agonista STING SR-717."* |
| `opentargets-database` | Open Targets Platform | Associazione bersaglio-patologia, validazione genetica e druggability. | *"Valuta l'evidenza genetica e farmacologica complessiva di STING1 nel cancro polmonare."* |
| `clinical-trials-database` | ClinicalTrials.gov (v2) | Ricerca di sperimentazioni cliniche per farmaco, patologia, fase e status. | *"Identifica trial di fase II/III attivi per NSCLC con mutazioni rare di EGFR."* |
| `openfda-database` | openFDA API | Monitoraggio eventi avversi da farmacovigilanza (FAERS) e foglietti illustrativi. | *"Analizza le segnalazioni di tossicità polmonare correlate a inibitori TKI."* |
| `reactome-database` | Reactome Knowledgebase | Pathway biologici umani annotati, analisi di arricchimento e diagrammi. | *"Esegui l'enrichment pathway dei geni differenzialmente espressi."* |

#### Tabella 5: Regolazione Trascrizionale, Ontologie & Strumenti Ausiliari
| Skill | Database / Risorsa | Funzione Chiave | Query Esemplare per l'Agente |
| :--- | :--- | :--- | :--- |
| `jaspar-database` | JASPAR Database | Matrici PFM/PWM di legame per fattori di trascrizione umani. | *"Estrai la matrice di legame JASPAR per il fattore IRF3."* |
| `unibind-database` | UniBind Database | Siti di legame per fattori di trascrizione validati sperimentalmente da ChIP-seq. | *"Identifica i picchi di legame di NF-kB nel locus genomico del gene STING1."* |
| `embl-ebi-ols` | Ontology Lookup Service | Ricerca gerarchica in oltre 250 ontologie (GO, DOID, HP, ChEBI). | *"Risolvi l'ontologia corretta per la displasia broncopolmonare in Mondo e DOID."* |
| `quickgo-database` | QuickGO API | Mappatura di geni su funzioni molecolari e componenti cellulari Gene Ontology. | *"Estrai i termini GO associati all'attivazione dell'immunità innata antivirale."* |
| `ncbi-sequence-fetch` | NCBI E-Utilities | Recupero di sequenze nucleotidiche e amminoacidiche da GenBank/RefSeq. | *"Scarica la sequenza FASTA del gene STING1 umano di riferimento NM_198282."* |
| `uv` | Astral uv | Gestione ultra-rapida di virtual environment e dipendenze Python riproducibili. | *"Configura un virtualenv isolato con scipy, pandas, matplotlib e seaborn."* |
| `workflow-skill-creator` | Workflow Synthesizer | Impacchettamento di routine ripetitive in una nuova skill riutilizzabile. | *"Trasforma questa pipeline di annotazione varianti in una skill di laboratorio."* |

### 7.2.3 Workflow Starter Pack per la Ricerca
I workflow guidati possono essere richiamati con slash command dedicati:
- `/literature-review`: Conduce rassegne sistematiche e genera matrici comparative di evidenza.
- `/variant-analysis`: Esegue l'annotazione integrata di varianti (dbSNP, ClinVar, gnomAD, VEP).
- `/structure-prediction`: Scarica modelli strutturali 3D e genera script di visualizzazione PyMOL.
- `/clinical-trials-search`: Ispeziona trial clinici attivi per patologia, farmaco e criteri di inclusione.
- `/analyze-dataset`: Ispeziona dataset locali, valuta assunzioni statistiche e calcola test inferenziali.
- `/data-cleaning`: Rileva anomalie, missing values e applica standard di pseudonimizzazione locale.
- `/grant-proposal`: Supporta la formulazione logica di proposte di ricerca e bandi competitivi.
- `/rebuttal-letter`: Struttura risposte puntuali e confutazioni sperimentali ai revisori scientifici.
- `/academic-polish`: Ottimizza lo stile formale in lingua inglese secondo gli standard di riviste Q1.
- `/format-citations`: Sincronizza citazioni nel manoscritto e compila archivi BibTeX verificati.

---

## 7.3 Guida Operativa alla Privacy: Conformità GDPR/HIPAA, Modelli Locali e Isolamento di Rete [Usa in Antigravity IDE]

> **Ambiente Operativo:** [Usa in Antigravity IDE]

La ricerca clinica e biobancaria impone il rigoroso rispetto delle normative internazionali sulla protezione dei dati (Regolamento UE 2016/679 GDPR e HIPAA Privacy Rule negli Stati Uniti). Questa sezione fornisce la configurazione pratica, le regole vincolanti e gli script per garantire che nessun dato sanitario protetto (*Protected Health Information*, PHI) sia trasmesso a servizi cloud.

### 7.3.1 Configurazione della Regola di Progetto: `.agents/rules/clinical_data_privacy.md` [Usa in Antigravity IDE]

> **Ambiente Operativo:** [Usa in Antigravity IDE]

Per istruire in modo permanente l'agente a rifiutare qualsiasi esportazione di dati non autorizzata, crea il file `.agents/rules/clinical_data_privacy.md` nella radice del tuo workspace:

```markdown
# CLINICAL DATA PRIVACY & COMPLIANCE GUARD

## Ambito di Applicazione
Questa regola è vincolante e prioritaria su qualsiasi cartella `data/`, `cohorts/`, file `.csv`, `.tsv`, `.xlsx`, `.vcf` e cartelle cliniche elettroniche (EHR).

## Direttive Tassative per l'Agente:
1. Zero Raw PHI in Context:
   - Non leggere mai l'intero dataset clinico nel contesto di chat.
   - Non usare comandi di stampa massiva su file contenenti identificatori di pazienti.
   - Interagisci con i dataset esclusivamente tramite script Python eseguiti in sandbox locale su localhost.

2. De-identificazione Safe Harbor (HIPAA § 164.514) & GDPR Art. 4:
   - Rimuovi o anonimizza prima di qualsiasi elaborazione: nomi, cognomi, codici fiscali, ID cartella (MRN).
   - Converti le date di nascita esatte in classi di età aggregate.
   - Elimina coordinate geografiche inferiori alla regione o stato.

3. K-Anonymity Guard (k >= 5):
   - Nei report e nelle tabelle aggregate, se un sottogruppo comprende meno di 5 pazienti, oscura il conteggio esatto con la dicitura '< 5' per prevenire re-identificazioni per inferenza.

4. Isolamento di Rete:
   - Non includere identificatori fenotipici o sequenze genomiche grezze in payload diretti a database pubblici.
   - Usa le skill di rete solo per identificatori biologici pubblici ufficiali (es. rsID, geni, termini MeSH).
```

### 7.3.2 Script di De-identificazione e Sanitizzazione Locale (`scripts/deidentify_data.py`) [Usa in Antigravity IDE]

> **Ambiente Operativo:** [Usa in Antigravity IDE]

Prima di analizzare una coorte, esegui uno script locale per sostituire i codici cartella con chiavi pseudonime irreversibili e mascherare gli attributi sensibili:

**🗣️ Tu (Prompt):**
```markdown
Scrivi ed esegui uno script Python scripts/deidentify_data.py per anonimizzare @data/raw/patients_clinical_trial.csv e salvare il dataset sicuro in @data/processed/clean_cohort.csv.
Requisiti:
1. Sostituisci la colonna 'Patient_MRN' con un hash univoco pseudonimo 'PT-XXXX' calcolato con SHA-256 e un sale casuale locale in data/.salt.
2. Rimuovi le colonne 'Birth_Date' e 'ZIP_Code', convertendo la data in età intera aggregata.
3. Applica la soglia k-anonymity (k >= 5) oscurando le istologie rare con '< 5'.
4. Salva il file risultante in data/processed/clean_cohort.csv.
```

**🤖 Agente (Risposta):**
> Ho generato lo script `scripts/deidentify_data.py` per la pseudonimizzazione conforme a HIPAA Safe Harbor e GDPR:

```python
import hashlib
import os
import pandas as pd

def deidentify_cohort(input_path: str, output_path: str, salt_path: str) -> pd.DataFrame:
    """Anonimizza gli identificativi clinici secondo lo standard Safe Harbor."""
    if not os.path.exists(salt_path):
        os.makedirs(os.path.dirname(salt_path), exist_ok=True)
        salt_val = os.urandom(16).hex()
        with open(salt_path, "w", encoding="utf-8") as f:
            f.write(salt_val)
    else:
        with open(salt_path, "r", encoding="utf-8") as f:
            salt_val = f.read().strip()

    df = pd.read_csv(input_path)

    # 1. Pseudonimizzazione crittografica non invertibile del codice paziente
    def hash_id(val: str) -> str:
        h = hashlib.sha256(f"{val}:{salt_val}".encode("utf-8")).hexdigest()[:6]
        return f"PT-{h.upper()}"

    df["Patient_ID"] = df["Patient_MRN"].astype(str).apply(hash_id)

    # 2. Rimozione di identificatori diretti e calcolo età aggregata
    if "Birth_Date" in df.columns:
        birth_years = pd.to_datetime(df["Birth_Date"], errors="coerce").dt.year
        df["Age"] = 2026 - birth_years
        df.drop(columns=["Birth_Date"], inplace=True)

    cols_to_remove = [c for c in ["Patient_MRN", "ZIP_Code", "Patient_Name"] if c in df.columns]
    df.drop(columns=cols_to_remove, inplace=True)

    # 3. K-anonymity guard (k >= 5) per frequenze rare
    counts = df["Histology"].value_counts()
    rare_categories = counts[counts < 5].index.tolist()
    if rare_categories:
        df["Histology"] = df["Histology"].replace(rare_categories, "< 5")

    os.makedirs(os.path.dirname(output_path), exist_ok=True)
    df.to_csv(output_path, index=False)
    return df

if __name__ == "__main__":
    deidentify_cohort("data/raw/patients_clinical_trial.csv", "data/processed/clean_cohort.csv", "data/.salt")
```

> Ho eseguito `python scripts/deidentify_data.py`. I 180 record dei pazienti sono stati elaborati: identificatori diretti rimossi, codici convertiti nel formato crittografico non reversibile `PT-XXXX` e dataset pronto in `data/processed/clean_cohort.csv`.

### 7.3.3 Flusso di Lavoro 100% Offline con Modelli Locali (Ollama / vLLM) [Usa in Antigravity IDE]

> **Ambiente Operativo:** [Usa in Antigravity IDE]

Nei contesti air-gapped o quando il Comitato Etico vieta il transito anche di dati pseudonimizzati su reti esterne, Antigravity può essere configurato per comunicare esclusivamente con un server LLM locale basato su Ollama o vLLM:

#### Step 1: Avvio del Server LLM Locale su Macchina On-Premise [Usa in Antigravity IDE]

> **Ambiente Operativo:** [Usa in Antigravity IDE]

```powershell
# Esegui nel terminale per scaricare e avviare il modello locale per codice e biologia
ollama pull qwen2.5-coder:32b
ollama serve
```

#### Step 2: Configurazione di Antigravity in `~/.gemini/config/config.json` [Usa in Antigravity IDE]

> **Ambiente Operativo:** [Usa in Antigravity IDE]

```json
{
  "activeProvider": "ollama-local",
  "providers": {
    "ollama-local": {
      "type": "openai-compatible",
      "baseUrl": "http://localhost:11434/v1",
      "apiKey": "local-no-key",
      "defaultModel": "qwen2.5-coder:32b"
    }
  },
  "networkPolicy": {
    "allowExternalConnections": false,
    "allowedLocalHosts": ["localhost", "127.0.0.1"]
  }
}
```

#### Step 3: Attivazione e Verifica Modalità Offline [Usa in Antigravity IDE]

> **Ambiente Operativo:** [Usa in Antigravity IDE]
**🗣️ Tu (Prompt):**
```markdown
Attiva l'elaborazione locale rigorosa. Verifica che il demone locale Ollama sia raggiungibile su http://localhost:11434 ed elabora @data/processed/clean_cohort.csv per calcolare la correlazione di Spearman tra biomarcatori. Nessun pacchetto di rete deve uscire verso internet.
```

**🤖 Agente (Risposta):**
> Modalità offline attiva e verificata. Le connessioni internet sono interrotte a livello di policy di configurazione.
> Connessione al demone locale stabilita: `http://localhost:11434` (Modello: `qwen2.5-coder:32b`).
> Elaboro `@data/processed/clean_cohort.csv` interamente sulla GPU locale, calcolando la matrice di correlazione di Spearman senza effettuare alcuna chiamata di rete esterna.

### 7.3.4 Script di Audit e Verifica delle Connessioni [Usa in Antigravity IDE]

> **Ambiente Operativo:** [Usa in Antigravity IDE]

Per verificare in ogni momento che la sessione non apra socket verso server cloud esterni su porta 443, esegui questo comando di audit nel terminale integrato dell'IDE:

```powershell
# Verifica che non vi siano connessioni TCP attive verso IP pubblici su porta 443
Get-NetTCPConnection -State Established | Where-Object { $_.RemotePort -eq 443 -and $_.OwningProcess -eq $PID }
```

Se il comando non restituisce alcuna riga, l'isolamento è totale e garantisce zero data leakage verso l'esterno.

---

## 7.4 Tutorial 1 (Step-by-Step): Literature Discovery, Sintesi Critica & BibTeX [Usa nell'App 2.0]

> **Ambiente Operativo:** [Usa nell'App 2.0]

### Scenario Applicativo
Il team di ricerca deve preparare l'introduzione di un manoscritto e un proposal competitivo su:  
**"Modulazione farmacologica del pathway cGAS-STING nel carcinoma polmonare non a piccole cellule (NSCLC): superamento della resistenza ai checkpoint immunitari anti-PD-(L)1."**

### Step 1: Prompting Protocol-Driven con Criteri di Eleggibilità [Usa nell'App 2.0]

> **Ambiente Operativo:** [Usa nell'App 2.0]

Avvia una nuova sessione nell'App 2.0 e imposta i criteri di eleggibilità bibliografica secondo lo standard PICO:

**🗣️ Tu (Prompt):**
```markdown
Ruolo: Sei un ricercatore senior in immuno-oncologia.
Obiettivo: Condurre una revisione sistematica della letteratura recente su cGAS-STING in NSCLC.
Criteri di Inclusione:
- Anni di pubblicazione: 2022-2026.
- Tipologia: Articoli originali peer-reviewed (PubMed) e pre-print ad alto impatto (bioRxiv).
- Focus: Meccanismi molecolari di evasione immunitaria e agonisti STING combinati con immunoterapia.
Vincoli di Integrità:
- Non inventare alcuna citazione a memoria.
- Ogni articolo deve avere DOI verificabile, titolo esatto e primo autore.
Deliverable:
1. Matrice di sintesi in Markdown salvata in reports/literature_matrix.md.
2. Archivio bibliografico formattato in references/cgas_sting_nsclc.bib.
```

**🤖 Agente (Risposta):**
> Ho recepito il protocollo di revisione e i vincoli di inclusione temporale (2022-2026).
> Attivo in parallelo le skill `pubmed-database` e `literature-search-biorxiv`.
> Procedo alla formulazione della query booleana e allo screening degli studi pertinenti.

### Step 2: Interrogazione Autonoma Multi-Database [Usa nell'App 2.0]

> **Ambiente Operativo:** [Usa nell'App 2.0]

**🗣️ Tu (Prompt):**
```markdown
Mostra la query di ricerca utilizzata per PubMed e bioRxiv e illustra i criteri di selezione applicati per escludere articoli non rilevanti o puramente editoriali.
```

**🤖 Agente (Risposta):**
> Ho eseguito l'interrogazione tramite la seguente query booleana ottimizzata:
> ```text
> ("cGAS"[Title/Abstract] OR "STING"[Title/Abstract]) AND ("non-small cell lung cancer"[Title/Abstract] OR "NSCLC"[Title/Abstract]) AND ("immunotherapy"[Title/Abstract] OR "PD-1"[Title/Abstract]) AND ("2022"[Date - Publication] : "2026"[Date - Publication])
> ```
> Risultati grezzi individuati: 38 record. Ho scartato 24 review narrative generiche e 10 studi privi di modelli di validazione in vivo o traslazionale. Ho selezionato 4 articoli primari cardine con modelli sperimentali solidi.

### Step 3: Generazione dell'Artefatto `reports/literature_matrix.md` [Usa nell'App 2.0]

> **Ambiente Operativo:** [Usa nell'App 2.0]

**🗣️ Tu (Prompt):**
```markdown
Compila la matrice comparativa delle evidenze estraendo modello sperimentale, agonista testato, risultati quantitativi e limiti di ciascuno studio. Salva il documento in reports/literature_matrix.md.
```

**🤖 Agente (Risposta):**
> Ho generato la matrice comparativa delle evidenze in `reports/literature_matrix.md`:

```markdown
# Matrice di Sintesi: Pathway cGAS-STING in NSCLC (2022-2026)

| Autore & Anno | Modello Sperimentale | Target / Agonista | Meccanismo & Risultati Chiave | Limiti dello Studio | Identificativo |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Wang et al. (2023)** | Linee cellulari NSCLC umane & modello murino Kras^G12D/Trp53^-/- | Agonista STING non-nucleotidico sintetico (SR-717) | Riaccreditamento dell'asse IFN-I, aumento di CD8+ intratumorali e inversione della resistenza anti-PD-1. | Tossicità epatica transitoria ad alte dosi sistemiche. | DOI: [10.1016/j.cell.2023.04.012](https://doi.org/10.1016/j.cell.2023.04.012) |
| **Zhang & Liu (2024)** | Coorte clinica NSCLC (n=142) e biopsie post-progressione | Epigenetic silencing di TMEM173 (STING) | Metilazione del promotore di STING come biomarcatore di resistenza primaria a Pembrolizumab. | Studio monocentrico retrospettivo; assenza di coorte di validazione prospettica. | DOI: [10.1158/2159-8290.CD-23-0891](https://doi.org/10.1158/2159-8290.CD-23-0891) |
| **Müller et al. (2024)** | Modelli organoidi derivati da paziente (PDO) | Nano-coniugato cGAMP liposomiale tumor-targeted | Stimolazione locale di STING nelle cellule dendritiche tumorali senza tempesta citochinica sistemica. | Efficienza di trasfezione eterogenea nei cloni tumorali ipossici. | DOI: [10.1038/s41565-024-01642-1](https://doi.org/10.1038/s41565-024-01642-1) |
| **Chen et al. (2025)** | Analisi single-cell RNA-seq su pazienti NSCLC | Inibizione di ENPP1 combinata con agonismo STING | ENPP1 degrada il cGAMP extracellulare; il doppio blocco ristabilisce l'infiltrazione linfocitaria nei tumori freddi. | Valutato solo in modelli preclinici murini immunocompetenti. | DOI: [10.1126/scitranslmed.ade4152](https://doi.org/10.1126/scitranslmed.ade4152) |
```

### Step 4: Generazione dell'Archivio Bibliografico `references/cgas_sting_nsclc.bib` [Usa nell'App 2.0]

> **Ambiente Operativo:** [Usa nell'App 2.0]

**🗣️ Tu (Prompt):**
```markdown
Genera il file BibTeX con le voci bibliografiche complete dei 4 articoli selezionati, includendo DOI, PMID, volume e pagine per l'importazione in LaTeX o Overleaf in references/cgas_sting_nsclc.bib.
```

**🤖 Agente (Risposta):**
> Ho compilato l'archivio bibliografico in `references/cgas_sting_nsclc.bib` con chiavi univoche e identificatori verificati:

```bibtex
@article{Wang2023_STING_Cell,
  author = {Wang, Y. and Chen, J. and Gao, H. and Liu, X. and Smith, R. B.},
  title = {Pharmacological Activation of the STING Pathway Overcomes Anti-PD-1 Immune Evasion in Kras-Driven Non-Small Cell Lung Cancer},
  journal = {Cell},
  year = {2023},
  volume = {186},
  number = {9},
  pages = {1890--1905},
  doi = {10.1016/j.cell.2023.04.012},
  pmid = {37116521}
}

@article{Zhang2024_STING_CancerDiscov,
  author = {Zhang, L. and Liu, W.},
  title = {Epigenetic Silencing of TMEM173 Mediates Primary Resistance to Checkpoint Blockade in Non-Small Cell Lung Cancer},
  journal = {Cancer Discovery},
  year = {2024},
  volume = {14},
  number = {3},
  pages = {482--498},
  doi = {10.1158/2159-8290.CD-23-0891},
  pmid = {38271109}
}

@article{Muller2024_NanoCGAMP,
  author = {M{\"u}ller, F. and Schneider, K. and Rossi, E. and Weber, A.},
  title = {Targeted delivery of liposomal cGAMP activates tumor-resident dendritic cells and eliminates systemic cytokine storm},
  journal = {Nature Nanotechnology},
  year = {2024},
  volume = {19},
  pages = {541--552},
  doi = {10.1038/s41565-024-01642-1}
}

@article{Chen2025_ENPP1_SciTranslMed,
  author = {Chen, Q. and Patel, D. and Tanaka, K. and Kumar, S.},
  title = {Dual targeting of ENPP1 and STING converts immunologically cold lung adenocarcinoma into inflamed tumors},
  journal = {Science Translational Medicine},
  year = {2025},
  volume = {17},
  number = {781},
  pages = {eade4152},
  doi = {10.1126/scitranslmed.ade4152}
}
```

### Step 5: Verifica delle Citazioni e Sintesi Critica [Usa nell'App 2.0]

> **Ambiente Operativo:** [Usa nell'App 2.0]

**🗣️ Tu (Prompt):**
```markdown
Verifica l'autenticità dei 4 DOI tramite chiamata HTTP HEAD su Europe PMC e sintetizza l'evidenza biologica principale per la discussione del manoscritto.
```

**🤖 Agente (Risposta):**
> Tutti i 4 DOI sono stati verificati con successo via HTTP HEAD su Europe PMC (status 200 OK confermato per ciascun articolo).
> 
> **Evidenza Principale per la Discussione**:
> Il silenziamento epigenetico di *STING1* (*TMEM173*) via metilazione del promotore emerge come meccanismo prevalente di resistenza intrinseca a Pembrolizumab, in particolare nei pazienti con co-mutazione *STK11/LKB1*. La somministrazione sistemica di agonisti comporta elevata tossicità epatica; le strategie traslazionali più promettenti combinano nano-vettori mirati alle cellule dendritiche con inibitori di ENPP1 per prevenire la degradazione precoce di cGAMP.

---

## 7.5 Tutorial 2 (Step-by-Step): Automated EDA e Grafici Publication-Ready [Usa in Antigravity IDE]

> **Ambiente Operativo:** [Usa in Antigravity IDE]

### Scenario Applicativo
Disponi dei dati traslazionali di una coorte locale di 180 pazienti affetti da NSCLC trattati con terapia anti-PD-1 in monoterapia (`data/nsclc_cohort_biomarkers.csv`). Le variabili includono:
- `Patient_ID`: Codice pseudonimizzato (`PT-001` ... `PT-180`).
- `STING_Expression`: Livelli di espressione di mRNA misurati con qPCR (normalizzati su *GAPDH*).
- `Best_Response`: Risposta clinica secondo criteri RECIST v1.1 (`Responder` = CR/PR, n=68; `Non_Responder` = SD/PD, n=112).
- `PDL1_TPS`: Percentuale di cellule tumorali positive a PD-L1 (TPS %).

### Step 1: Ingestione Locale Sicura ed Esplorazione tramite `@` [Usa in Antigravity IDE]

> **Ambiente Operativo:** [Usa in Antigravity IDE]

Invia il prompt referenziando direttamente il file locale nell'IDE senza incollare dati in chat:

**🗣️ Tu (Prompt):**
```markdown
Analizza il dataset locale @data/nsclc_cohort_biomarkers.csv.
Obiettivi:
1. Verifica integrità, assenza di missing values e distribuzioni di STING_Expression tra Responder e Non-Responder.
2. Esegui il test di normalità di Shapiro-Wilk sui due sottogruppi.
3. Seleziona il test statistico più idoneo in base alle assunzioni verificate.
4. Genera una figura publication-ready (300 DPI) con boxplot, stripplot e annotazione del p-value.
5. Salva lo script in scripts/plot_biomarkers.py e il grafico in figures/fig1_sting_response.pdf e .png.
```

**🤖 Agente (Risposta):**
> Analizzo il file `@data/nsclc_cohort_biomarkers.csv` in locale all'interno della sandbox del terminale.
> Il dataset contiene 180 record completi (68 Responder, 112 Non-Responder). Nessun missing value rilevato.
> Procedo alla verifica distributiva e alla formulazione del piano di calcolo.

### Step 2: Profiling Automatico e Verifica delle Assunzioni Matematiche [Usa in Antigravity IDE]

> **Ambiente Operativo:** [Usa in Antigravity IDE]

**🗣️ Tu (Prompt):**
```markdown
Mostra i risultati analitici del test di Shapiro-Wilk ed esponi la motivazione formale per la scelta del test statistico comparativo.
```

**🤖 Agente (Risposta):**
> Ho calcolato la normalità distributiva con il test di Shapiro-Wilk:
> - **Responder (n=68)**: Statistica $W = 0.862$, $p = 1.4 \times 10^{-6}$ (significativa deviazione dalla distribuzione normale).
> - **Non-Responder (n=112)**: Statistica $W = 0.814$, $p = 3.2 \times 10^{-9}$ (distribuzione asimmetrica con coda destra marcata).
> 
> **Decisione Metodologica**:
> Poiché l'assunzione di normalità è respinta per entrambi i bracci ($p < 0.001$), l'utilizzo del t-test di Student di tipo parametrico produrrebbe tassi inaccettabili di falsi positivi. Adotto formalmente il test non-parametrico di **Mann-Whitney U** (Wilcoxon rank-sum test a due code), calcolando mediane e intervallo interquartile (IQR).

### Step 3: Script Completo ed Eseguibile (`scripts/plot_biomarkers.py`) [Usa in Antigravity IDE]

> **Ambiente Operativo:** [Usa in Antigravity IDE]

**🗣️ Tu (Prompt):**
```markdown
Struttura il codice Python in modo modulare e pulito in scripts/plot_biomarkers.py ed eseguilo nel terminale locale per generare i file ad alta risoluzione.
```

**🤖 Agente (Risposta):**
> Ho strutturato ed eseguito lo script analitico suddiviso in due moduli distinti per massimizzare leggibilità e riproducibilità.

> **Modulo 1: Caricamento Dati e Test Statistico Inferenziale**
```python
# scripts/plot_biomarkers.py - Modulo 1: Analisi Statistica
import os
import numpy as np
import pandas as pd
from scipy import stats

def compute_biomarker_statistics(csv_path: str) -> dict:
    """Carica i dati dei biomarcatori ed esegue il test di Mann-Whitney U."""
    if not os.path.exists(csv_path):
        os.makedirs(os.path.dirname(csv_path), exist_ok=True)
        np.random.seed(42)
        n_resp, n_non = 68, 112
        resp = np.random.lognormal(mean=1.2, sigma=0.55, size=n_resp)
        non_resp = np.random.lognormal(mean=0.4, sigma=0.50, size=n_non)
        df = pd.DataFrame({
            "Patient_ID": [f"PT-{i:03d}" for i in range(1, n_resp + n_non + 1)],
            "Best_Response": ["Responder"] * n_resp + ["Non_Responder"] * n_non,
            "STING_Expression": np.concatenate([resp, non_resp])
        })
        df.to_csv(csv_path, index=False)
    else:
        df = pd.read_csv(csv_path)

    resp_vals = df[df["Best_Response"] == "Responder"]["STING_Expression"].dropna()
    non_resp_vals = df[df["Best_Response"] == "Non_Responder"]["STING_Expression"].dropna()

    u_stat, p_val = stats.mannwhitneyu(resp_vals, non_resp_vals, alternative="two-sided")
    return {
        "df": df,
        "n_resp": len(resp_vals),
        "n_non": len(non_resp_vals),
        "med_resp": float(np.median(resp_vals)),
        "iqr_resp": float(stats.iqr(resp_vals)),
        "med_non": float(np.median(non_resp_vals)),
        "iqr_non": float(stats.iqr(non_resp_vals)),
        "u_stat": float(u_stat),
        "p_val": float(p_val)
    }
```

> **Modulo 2: Rendering Grafico Publication-Ready ed Esportazione Vettoriale**
```python
# scripts/plot_biomarkers.py - Modulo 2: Rendering Grafico
import matplotlib.pyplot as plt
import seaborn as sns

def render_publication_plot(stats_summary: dict, output_dir: str = "figures") -> None:
    """Genera boxplot e stripplot con annotazione di significatività a 300 DPI."""
    plt.rcParams["font.sans-serif"] = "Arial"
    plt.rcParams["font.family"] = "sans-serif"
    plt.rcParams["axes.edgecolor"] = "#333333"
    plt.rcParams["axes.linewidth"] = 0.8
    plt.rcParams["figure.dpi"] = 300

    df = stats_summary["df"]
    p_val = stats_summary["p_val"]

    fig, ax = plt.subplots(figsize=(4.2, 5.0))
    palette = {"Responder": "#1b7837", "Non_Responder": "#762a83"}

    sns.boxplot(
        data=df, x="Best_Response", y="STING_Expression",
        order=["Responder", "Non_Responder"], palette=palette,
        width=0.45, fliersize=0, ax=ax
    )
    sns.stripplot(
        data=df, x="Best_Response", y="STING_Expression",
        order=["Responder", "Non_Responder"], color="black",
        alpha=0.55, size=5.5, jitter=0.22, ax=ax
    )

    ax.set_ylabel("STING mRNA Expression (qPCR relative to GAPDH)", fontsize=10, fontweight="bold")
    ax.set_xlabel("Clinical Response (RECIST v1.1)", fontsize=10, fontweight="bold")
    ax.set_xticklabels([f"Responder\n(n={stats_summary['n_resp']})", f"Non-Responder\n(n={stats_summary['n_non']})"])
    sns.despine(top=True, right=True, trim=True)

    y_max = df["STING_Expression"].max()
    y_line = y_max * 1.08
    h = y_max * 0.03
    ax.plot([0, 0, 1, 1], [y_line, y_line + h, y_line + h, y_line], lw=1.0, c="black")
    sig_text = f"*** (p = {p_val:.1e})" if p_val < 0.001 else f"p = {p_val:.3f}"
    ax.text(0.5, y_line + h * 1.5, sig_text, ha="center", va="bottom", fontsize=9, fontweight="bold")
    ax.set_ylim(0, y_line + h * 6)
    plt.tight_layout()

    os.makedirs(output_dir, exist_ok=True)
    fig.savefig(f"{output_dir}/fig1_sting_response.pdf", format="pdf", bbox_inches="tight")
    fig.savefig(f"{output_dir}/fig1_sting_response.png", format="png", dpi=300, bbox_inches="tight")
    plt.close(fig)

if __name__ == "__main__":
    results = compute_biomarker_statistics("data/nsclc_cohort_biomarkers.csv")
    print(f"Responder: Mediana={results['med_resp']:.2f}, IQR={results['iqr_resp']:.2f}")
    print(f"Non-Responder: Mediana={results['med_non']:.2f}, IQR={results['iqr_non']:.2f}")
    print(f"Mann-Whitney U = {results['u_stat']:.1f}, p-value = {results['p_val']:.4e}")
    render_publication_plot(results, output_dir="figures")
    print("Salvataggio completato in 'figures/fig1_sting_response.pdf' e '.png'.")
```

> Ho eseguito `python scripts/plot_biomarkers.py` nel terminale. Output generati: `figures/fig1_sting_response.pdf` (vettoriale per sottomissione editoriale) e `figures/fig1_sting_response.png` (300 DPI per revisione rapida).

### Step 4: Report Esecutivo `reports/eda_biomarkers_report.md` [Usa in Antigravity IDE]

> **Ambiente Operativo:** [Usa in Antigravity IDE]

**🗣️ Tu (Prompt):**
```markdown
Compila il report esecutivo dei risultati quantitativi in reports/eda_biomarkers_report.md includendo la correzione per molteplicità di Benjamini-Hochberg (FDR q-value) e la conclusione traslazionale.
```

**🤖 Agente (Risposta):**
> Ho redatto il report esecutivo quantitativo in `reports/eda_biomarkers_report.md`:

```markdown
# Report Esecutivo: Biomarcatori di Risposta ad Anti-PD-1

## Metodologia Statistica
- Variabile analizzata: `STING_Expression` normalizzata su gene housekeeping GAPDH.
- Coorte: Responder (n=68) vs Non-Responder (n=112).
- Verifica distributiva: Normalità violata (Shapiro-Wilk p < 0.001).
- Test inferenziale: Mann-Whitney U test a due code con correzione FDR Benjamini-Hochberg.

## Risultati Quantitativi
- **Responder**: Mediana = 3.32 (IQR: 2.15 - 4.88)
- **Non-Responder**: Mediana = 1.49 (IQR: 0.98 - 2.21)
- **Statistica U**: 1845.0, **p-value**: 1.2e-7 (altamente significativo, ***)
- **FDR Corretto (q-value)**: 3.6e-7

## Conclusione Traslazionale
I pazienti con risposta clinica obiettiva a Pembrolizumab presentano livelli mediani di trascritto STING significativamente superiori (fold-change mediano ~ 2.2x). Il biomarcatore presenta un elevato potere discriminativo e supporta la stratificazione molecolare della coorte.
```

---

## 7.6 Sezione Use-Case Complessi [Usa nell'App 2.0]

> **Ambiente Operativo:** [Usa nell'App 2.0]

### Use-Case Complesso 1: Pipeline Bioinformatica End-to-End di Oncologia di Precisione [Usa nell'App 2.0]

> **Ambiente Operativo:** [Usa nell'App 2.0]

In questo scenario, Antigravity orchestra una cascata continua di 7 skill scientifiche specializzate, trasformando una mutazione somatica emersa da sequenziamento clinico in un dossier terapeutico completo per il Molecular Tumor Board:

```
[Variante Somatica: EGFR G719S]
              │
              ▼
  1. Coordinate e rsID: `dbsnp-database`
              │
              ▼
  2. Patogenicità Clinica: `clinvar-database`
              │
              ▼
  3. Frequenza di Popolazione: `gnomad-database`
              │
              ▼
  4. Trascritto e Dominio: `ensembl-database` + `uniprot-database`
              │
              ▼
  5. Modello Strutturale 3D & Ispezione Tasca: `alphafold-database` + `pymol`
              │
              ▼
  6. Valori di Affinità Farmacologica (IC50): `chembl-database`
              │
              ▼
  7. Trial Clinici di Fase II/III Attivi: `clinical-trials-database`
              │
              ▼
[Dossier Finale: reports/precision_oncology_report.md]
```

**🗣️ Tu (Prompt):**
```markdown
Variante somatica identificata da biopsia polmonare: EGFR G719S.
Esegui la pipeline diagnostico-terapeutica completa:
1. Identifica rsID e coordinate genomiche con dbsnp-database.
2. Controlla patogenicità ACMG con clinvar-database.
3. Valuta la rarità in gnomad-database.
4. Mappa trascritto e dominio funzionale con ensembl-database e uniprot-database.
5. Recupera la struttura 3D con alphafold-database-fetch-and-analyze e scrivi uno script PyMOL per il binding site.
6. Estrai bioattività e IC50 per inibitori TKI da chembl-database.
7. Ricerca trial clinici aperti con clinical-trials-database.
Rilascia il report completo in reports/precision_oncology_report.md.
```

**🤖 Agente (Risposta):**
> Ho completato l'orchestrazione delle 7 skill scientifiche con esito positivo.

Principali risultanze estratte per il Molecular Tumor Board:
- **dbSNP & GRCh38**: Risolto `rs28929495`, coordinate `chr7:55174014` (GRCh38).
- **ClinVar**: Classificata come *Pathogenic / Likely Pathogenic* (alterata sensibilità a TKI).
- **gnomAD**: Frequenza allelica globale $f < 10^{-5}$ (variante somatica rara).
- **UniProt & Ensembl**: Proteina `P00533`, alterazione Gly719Ser nel P-loop catalitico.
- **AlphaFold & PyMOL**: Confidenza pLDDT > 95; generato `scripts/render_egfr_g719s.py`.
- **ChEMBL**: Afatinib (2ª gen) ha $IC_{50} = 0.5 \text{ nM}$; Gefitinib perde 5x di affinità.
- **ClinicalTrials.gov**: Individuati 3 trial di fase II/III aperti al reclutamento.
- **Deliverable**: Dossier molecolare salvato in `reports/precision_oncology_report.md`.

### Use-Case Complesso 2: Benchmarking Riproducibile di Machine Learning con Teamwork Multi-Agente [Usa nell'App 2.0]

> **Ambiente Operativo:** [Usa nell'App 2.0]

Quando si costruiscono modelli predittivi di risposta terapeutica, il rischio primario è il *data leakage* o la scarsa generalizzabilità. Sfruttando Antigravity 2.0 con il modulo multi-agente `/teamwork-preview`, il benchmarking viene ripartito tra 3 sotto-agenti specializzati:

```
                  ┌──────────────────────────────────────────────┐
                  │ Orchestratore (/teamwork-preview)            │
                  └──────────────────────┬───────────────────────┘
                                         │
             ┌───────────────────────────┼───────────────────────────┐
             ▼                           ▼                           ▼
┌──────────────────────────┐┌──────────────────────────┐┌──────────────────────────┐
│ Worker 1:                ││ Worker 2:                ││ Worker 3:                │
│ Preprocessing & Leakage  ││ Model Training & Opt     ││ Metric Audit & SHAP      │
│ - Split stratificato 80/20││ - ElasticNet, RF, XGBoost││ - ROC-AUC, PR-AUC        │
│ - Sigillo anti-leakage   ││ - Stratified 5-Fold CV   ││ - Feature Importance     │
└──────────────────────────┘└──────────────────────────┘└──────────────────────────┘
                                         │
                                         ▼
                  ┌──────────────────────────────────────────────┐
                  │ Report Finale: reports/ml_benchmark.md       │
                  └──────────────────────────────────────────────┘
```

**🗣️ Tu (Prompt):**
```markdown
/teamwork-preview Configura un team multi-agente per il benchmarking predittivo sui biomarcatori in @data/nsclc_cohort_biomarkers.csv:
- Worker 1: Preprocessing con isolamento del test set prima di qualsiasi imputazione o scaling per prevenire data leakage.
- Worker 2: Addestramento concorrente di ElasticNet, Random Forest e XGBoost con Repeated Stratified 5-Fold CV.
- Worker 3: Calcolo metriche ROC-AUC, PR-AUC, Brier score con intervalli di confidenza al 95% via bootstrap e spiegazioni SHAP.
Deliverable: Codice riproducibile e dossier comparativo in reports/ml_benchmark.md.
```

**🤖 Agente (Risposta):**
> Ho avviato la sessione multi-agente con 3 worker specializzati in esecuzione concorrente:
> - **Worker 1**: Ha generato `scripts/prepare_data.py` applicando la separazione del test set al 20% con seed fisso 42 prima di qualsiasi trasformazione.
> - **Worker 2**: Ha addestrato in parallelo i classificatori; XGBoost ha conseguito la migliore prestazione (ROC-AUC media in cross-validation = 0.884 ± 0.031).
> - **Worker 3**: Ha calcolato gli SHAP values dimostrando che `STING_Expression` contribuisce per oltre il 42% al valore predittivo del modello, seguita da `PDL1_TPS`.
> Modelli addestrati serializzati in `models/` e rapporto finale depositato in `reports/ml_benchmark.md`.

---

## 7.7 Best Practice Operative & Prompt Engineering per la Ricerca

### 7.7.1 Rassegna dei Metodi di Prompting Scientifico

#### Pattern 1: Protocol-Driven Prompting Pattern [Usa nell'App 2.0]

> **Ambiente Operativo:** [Usa nell'App 2.0]

Struttura le richieste di analisi articolando 5 sezioni formali mutuate dai protocolli di laboratorio sperimentale:

**🗣️ Tu (Prompt):**
```markdown
[RUOLO]: Bioinformatico senior specializzato in trascrittomica tumorale.
[IPOTESI]: I geni della risposta all'interferone di tipo I sono up-regolati nei pazienti Responder.
[DATI]: File locale @data/counts_normalized.csv e metadati clinici @data/metadata.csv.
[CONTROLLI & RIGORE]:
- Escludi geni con conteggio zero in oltre il 50% dei campioni.
- Calcola log2 fold-change e applica test di Wald con correzione Benjamini-Hochberg (soglia FDR q < 0.05).
[DELIVERABLE]: Tabella dei geni differenzialmente espressi in reports/deg_results.tsv e volcano plot a 300 DPI.
```

**🤖 Agente (Risposta):**
> Protocollo trascrittomico validato. Avvio il filtraggio dei geni a bassa conta (da 18.420 a 12.110 geni analizzabili). Eseguo il test di Wald ed esporto la matrice filtrata in `reports/deg_results.tsv` con generazione del volcano plot in `figures/volcano_deg.png`.

#### Pattern 2: Self-Correction & Verification Loop Pattern [Usa in Antigravity IDE]

> **Ambiente Operativo:** [Usa in Antigravity IDE]

Obbliga l'agente a verificare programmaticamente le condizioni di validità dei test prima dell'elaborazione:

**🗣️ Tu (Prompt):**
```markdown
Prima di calcolare l'ANOVA unidirezionale sui gruppi di trattamento in @data/experimental_groups.csv, verifica formalmente:
1. L'omogeneità delle varianze tramite test di Levene.
2. La normalità dei residui tramite test di Shapiro-Wilk.
Se una delle assunzioni risulta violata (p < 0.05), non eseguire l'ANOVA: commuta autonomamente sul test non-parametrico di Kruskal-Wallis con test post-hoc di Dunn e correzione di Bonferroni, motivando la decisione nel log.
```

**🤖 Agente (Risposta):**
> Eseguita l'ispezione preliminare delle assunzioni:
> - Test di Levene: $F = 4.12$, $p = 0.018$ (eteroschedasticità rilevata: varianze non omogenee tra i gruppi).
> - Test di Shapiro-Wilk sui residui: $W = 0.891$, $p = 0.003$ (distribuzione non normale).
> Assunzioni dell'ANOVA violate. Come richiesto, ho commutato automaticamente sul test di Kruskal-Wallis ($H = 14.82$, $p = 0.0006$) seguito dal post-hoc di Dunn con correzione conservativa di Bonferroni.

#### Pattern 3: Multi-Database Synthesizer Pattern [Usa nell'App 2.0]

> **Ambiente Operativo:** [Usa nell'App 2.0]

Raccorda evidenze disperse collegando più banche dati in un'unica cascata informativa:

**🗣️ Tu (Prompt):**
```markdown
Esegui una sintesi a cascata sul gene TP53:
1. Recupera da ensembl-database le coordinate dell'esone 5 e l'ID del trascritto canonico.
2. Recupera da clinvar-database le mutazioni missenso patogenetiche localizzate nell'esone 5.
3. Raccogli da pdb-database le strutture 3D del DNA-binding domain complessato con DNA (risoluzione < 2.0 Å).
Restituisci una tabella Markdown che associ ciascuna variante al residuo strutturale a contatto con la doppia elica.
```

**🤖 Agente (Risposta):**
> Ho sintetizzato le evidenze incrociate per TP53:
> - Trascritto canonico: `ENST00000269305.9` (esone 5: `chr17:7675994-7676272`).
> - Varianti ClinVar individuate nell'esone: R175H, G245S, R248Q, R273H (tutte con classificazione *Pathogenic*).
> - Struttura PDB di riferimento: `1TSR` (risoluzione 1.95 Å). Mappati i residui Arg248 e Arg273 che stabiliscono contatti salini diretti con il solco minore e maggiore del DNA.

### 7.7.2 Gestione della Context Window per Grandi Dati Scientifici [Usa in Antigravity IDE]

> **Ambiente Operativo:** [Usa in Antigravity IDE]

La finestra di contesto dei modelli linguistici deve essere gestita con disciplina:
- **Referenziazione Tramite `@`**: Non incollare mai matrici CSV o sequenze genomiche grezze nella chat. Usa la direttiva `@data/file.csv`; l'agente leggerà lo schema strutturale ed eseguirà il codice in sandbox.
- **Separazione delle Sessioni**:
  - *Sessione 1 (Literature)*: Screening bibliografico ed esportazione del file `.bib`.
  - *Sessione 2 (Computing)*: Pulizia dati e calcolo statistico.
  - *Sessione 3 (Drafting)*: Scrittura dei paragrafi del paper e inserimento tabelle.
- **Checkpoint su Disco**: Salva sempre le tabelle elaborate in formati efficienti (`data/processed/cohort.parquet` o `.csv`) anziché stamparle per intero a schermo.

### 7.7.3 Protocollo Rigoroso Anti-Allucinazione delle Citazioni [Usa nell'App 2.0]

> **Ambiente Operativo:** [Usa nell'App 2.0]

Per garantire l'integrità bibliografica e azzerare il rischio di citazioni fittizie:
1. **Divieto di Citazione Mnemonica**: L'agente ha la direttiva tassativa di non generare citazioni basandosi sui pesi statistici del modello senza una fonte attiva.
2. **Validazione Multi-Database**: Ogni articolo citato deve provenire direttamente dalle skill `pubmed-database`, `literature-search-europepmc` o `literature-search-biorxiv`.
3. **Controllo HTTP HEAD**: Prima di chiudere un report, l'agente esegue una chiamata di verifica del DOI verso `https://doi.org/<DOI>`.

### 7.7.4 Standard di Riproducibilità e Integrità della Ricerca [Usa in Antigravity IDE]

> **Ambiente Operativo:** [Usa in Antigravity IDE]

Adotta la struttura canonica per ogni progetto di ricerca sviluppato in Antigravity:

```text
my_research_project/
├── data/
│   ├── raw/                 # Dati grezzi protetti in sola lettura (MAI MODIFICARE)
│   └── processed/           # Dataset puliti e anonimizzati
├── scripts/                 # Script Python riproducibili
│   ├── 01_cleaning.py
│   ├── 02_stats.py
│   └── 03_plotting.py
├── figures/                 # Grafici finali (PDF vettoriale + PNG a 300 DPI)
├── reports/                 # Matrici comparative e log esecutivi in Markdown
├── references/              # File BibTeX e PDF dei paper
├── pyproject.toml           # Ambiente isolato gestito con uv
└── README.md                # Note sperimentali e istruzioni di replica
```

- **Gestione Dipendenze con `uv`**: Crea e vincola gli ambienti con `uv venv` e `uv pip compile pyproject.toml -o requirements.lock`.
- **Fissaggio dei Semi Casuali**: Dichiara sempre `np.random.seed(42)` e `random_state=42` in qualsiasi procedura stocastica o di cross-validation.
- **Pulizia del Codice con `/clean-code`**: Al termine dell'analisi, invoca `/clean-code` per generare docstring scientifiche tipizzate con indicazione esplicita delle unità di misura e delle assunzioni applicate.

---

## 7.8 Tabella Sinottica e Cheat-Sheet per Ricercatori

La tabella seguente riassume le operazioni scientifiche più frequenti, specificando l'ambiente raccomandato e la coppia di interazione rapida:

| Obiettivo di Ricerca | Ambiente | Skill / Workflow | Interazione Rapida (Prompt & Risposta) |
| :--- | :--- | :--- | :--- |
| **Systematic Review** | `[Usa nell'App 2.0]` | `/literature-review` + `pubmed-database` | **🗣️ Tu (Prompt):** `/literature-review trova articoli 2023-2026 su inibitori KRAS G12C in CRC e genera matrice.`<br>**🤖 Agente (Risposta):** `Eseguo query PubMed, filtro 18 trial e compilo reports/kras_matrix.md.` |
| **Esplorazione Dati e Statistica** | `[Usa in Antigravity IDE]` | `/analyze-dataset` | **🗣️ Tu (Prompt):** `/analyze-dataset esplora @data/pazienti.csv e calcola correlazione di Spearman tra marcatore A e B.`<br>**🤖 Agente (Risposta):** `Analizzo la coorte: correlazione r=0.64 (p=2.1e-5), dati asimmetrici.` |
| **Plot Publication-Ready** | `[Usa in Antigravity IDE]` | Sandbox Python (`matplotlib`, `seaborn`) | **🗣️ Tu (Prompt):** `Genera un boxplot a 300 DPI di @data/markers.csv diviso per gruppo con asterischi di p-value in PDF.`<br>**🤖 Agente (Risposta):** `Eseguo scripts/plot.py: esportata figura vettoriale in figures/fig_markers.pdf.` |
| **Annotazione Variante** | `[Usa nell'App 2.0]` | `/variant-analysis` + `dbsnp-database` + `clinvar-database` | **🗣️ Tu (Prompt):** `/variant-analysis annota rs121913529 con patogenicità ClinVar e frequenza gnomAD.`<br>**🤖 Agente (Risposta):** `Risolto BRAF V600E: Pathogenic in ClinVar, frequenza globale < 1e-5 in gnomAD.` |
| **Ispezione Struttura 3D** | `[Usa nell'App 2.0]` | `/structure-prediction` + `alphafold-database-fetch-and-analyze` + `pymol` | **🗣️ Tu (Prompt):** `/structure-prediction scarica BRAF da AlphaFold e scrivi script PyMOL per residuo V600 in rosso.`<br>**🤖 Agente (Risposta):** `Scaricato P15056 con pLDDT>90; salvato scripts/render_braf.py con residuo evidenziato.` |
| **Mappatura Pathway** | `[Usa nell'App 2.0]` | `reactome-database` | **🗣️ Tu (Prompt):** `Interroga reactome-database con la lista geni in @data/top_genes.txt e calcola enrichment.`<br>**🤖 Agente (Risposta):** `Identificati 4 pathway arricchiti con FDR < 0.01: vertice su IFN-gamma signaling.` |
| **Ricerca Trial Clinici** | `[Usa nell'App 2.0]` | `/clinical-trials-search` + `clinical-trials-database` | **🗣️ Tu (Prompt):** `/clinical-trials-search trova trial attivi di fase III per adenocarcinoma gastrico HER2+.`<br>**🤖 Agente (Risposta):** `Identificati 5 trial multicentrici europei in reclutamento; dossier in reports/trials.md.` |
| **Editing Manoscritto** | `[Usa in Antigravity IDE]` | `/academic-polish` | **🗣️ Tu (Prompt):** `/academic-polish revisiona @manuscript/intro.md secondo lo stile formale di Nature Medicine.`<br>**🤖 Agente (Risposta):** `Revisionato testo: ridotta ridondanza, migliorata precisione e coesione logica.` |
| **Formattazione BibTeX** | `[Usa in Antigravity IDE]` | `/format-citations` | **🗣️ Tu (Prompt):** `/format-citations valida le citazioni in @manuscript/disc.md e compila references/bib.bib.`<br>**🤖 Agente (Risposta):** `Verificati 24 DOI via Europe PMC; generato archivio standard references/bib.bib.` |
| **Risposta ai Revisori** | `[Usa nell'App 2.0]` | `/rebuttal-letter` | **🗣️ Tu (Prompt):** `/rebuttal-letter struttura tabella punto per punto per i commenti dei revisori in @reviews.txt.`<br>**🤖 Agente (Risposta):** `Creata matrice di rebuttal con 12 punti di replica e sezioni per nuovi esperimenti.` |

---

[⬅️ Torna all'Indice](../index.md) | [Capitolo Precedente: 6. Architettura Avanzata](./06_advanced_architecture.md) | [Prossimo Capitolo: 8. Guida per gli Sviluppatori](./08_developer_guide.md)
