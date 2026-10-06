# Workflow: /structure-prediction — Predizione e Visualizzazione Strutture Proteiche 3D

**Descrizione**: Recupera e visualizza le strutture proteiche tridimensionali (AlphaFold o PDB) dato un gene symbol o UniProt ID, generando script di rendering con PyMOL.

**Esempio di Utilizzo**:
> `/structure-prediction scarica la struttura di P53 e mostrami i domini di legame principali.`

## Istruzioni Operative per l'Agente

1. **Risoluzione Identificativo**: Se viene fornito un simbolo genetico (*gene symbol*), usa la skill `uniprot-database` per individuare l'UniProt Accession ID canonico.
2. **Recupero Struttura 3D**: Usa la skill `alphafold-database-fetch-and-analyze` per scaricare la struttura predetta (formato PDB/mmCIF) ed estrarre le metriche di confidenza pLDDT.
3. **Verifica Omologia (Opzionale)**: Usa la skill `foldseek-structural-search` per individuare proteine con omologia strutturale tridimensionale.
4. **Visualizzazione Molecolare**: Genera uno script PyMOL (tramite la skill `pymol`) per colorare la struttura in base al punteggio pLDDT ed evidenziare i domini e i siti di legame chiave.
