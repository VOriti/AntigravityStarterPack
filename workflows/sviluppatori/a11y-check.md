# Workflow: /a11y-check — Verifica Accessibilità Web WCAG

**Descrizione**: Verifica la conformità dell'interfaccia web agli standard di accessibilità WCAG (Web Content Accessibility Guidelines), rilevando problemi di contrasto, semantica e attributi ARIA.

**Esempio di Utilizzo**:
> `/a11y-check analizza i componenti in src/components/ e verifica la conformità WCAG 2.1 AA.`

## Istruzioni Operative per l'Agente

1. **Scansione Componenti**: Analizza i file dei componenti dell'interfaccia (es. JSX/TSX/Vue/HTML).
2. **Validazione WCAG**:
   - Controlla attributi ARIA mancanti o errati (`aria-label`, `aria-hidden`).
   - Verifica i ruoli semantici e la navigabilità tramite tastiera (`tabindex`).
   - Segnala contrasto colore insufficiente nei CSS.
3. **Action Plan**: Mostra le violazioni individuate tramite un Artifact e chiedi all'utente se desidera l'applicazione della correzione automatica.
