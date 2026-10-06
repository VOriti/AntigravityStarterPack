# Workflow: /variant-analysis — Analisi e Annotazione Varianti Genetiche

**Descrizione**: Analizza la significatività clinica e l'impatto funzionale di una variante genetica interrogando i database dbSNP, ClinVar e gnomAD.

**Esempio di Utilizzo**:
> `/variant-analysis controlla la variante rs1234567 in ClinVar e dimmi se è patogenetica.`

## Istruzioni Operative per l'Agente

1. **Input della Variante**: Richiedi all'utente l'identificativo rsID (es. `rs123456`) o le coordinate genomiche (formato `chr:pos:ref>alt` su GRCh38).
2. **Interrogazione Database Genomici**:
   - Usa `dbsnp-database` per mappare la variante e recuperare le frequenze alleliche.
   - Usa `clinvar-database` per verificare la classificazione clinica e la patogenicità.
   - Usa `gnomad-database` per determinare la rarità nella popolazione generale.
3. **Generazione Report**: Compila i risultati in un documento Markdown strutturato che dettagli l'impatto funzionale della variante sulla salute o sul fenotipo clinico.
