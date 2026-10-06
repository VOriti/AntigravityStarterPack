# 10. Uso in Locale: Modelli Offline, Hardware Setup e Privacy Assoluta

L'esecuzione di Antigravity in modalità **completamente locale e disconnessa** rappresenta una necessità strategica fondamentale per organizzazioni e sviluppatori che operano sotto vincoli normativi stringenti, gestiscono proprietà intellettuale critica o lavorano in ambienti privi di connettività internet.

Questo capitolo illustra l'architettura on-premise di Antigravity, le modalità di configurazione dei principali motori di inferenza open source (**Ollama**, **vLLM**, **LM Studio**), le formule matematiche rigorose per il dimensionamento della memoria grafica (**VRAM**) e della **KV Cache**, le policy di isolamento air-gapped con regole firewall a livello di sistema operativo, e un flusso di lavoro completo di coding e test eseguito al 100% offline.

---

## 10.1 Filosofia, Sovranità dei Dati e Conformità Normativa

### 10.1.1 Il Paradigma Zero-Cloud e Sovranità Assoluta del Dato
Nello sviluppo software contemporaneo, il codice sorgente racchiude la totalità del know-how, della logica di business e dei segreti commerciali di un'organizzazione. L'invio di porzioni di repository, stringhe di configurazione, schemi di database o frammenti diagnostici verso endpoint cloud gestiti da terze parti introduce vettori di rischio non trascurabili:
- **Esposizione di Proprietà Intellettuale**: Rischio di data breach presso provider terzi o incorporamento accidentale di logiche proprietarie nei dataset di riaddestramento.
- **Dipendenza da Servizi Esterni (Vendor Lock-in)**: Interruzioni di servizio dell'infrastruttura cloud, fluttuazioni di latenza, rate limit e aumenti unilaterali dei costi di fatturazione per milione di token.
- **Violazione della Segregazione dei Dati**: In molti contesti industriali, il codice non deve mai lasciare il perimetro fisico della workstation o del datacenter aziendale.

La modalità locale di Antigravity opera secondo il principio di **sovranità assoluta del dato**: l'agente esegue il parsing sintattico, l'analisi statica (AST), l'indicizzazione vettoriale del workspace e la generazione di codice interfacciandosi unicamente con un demone LLM in esecuzione sulla stessa macchina (`127.0.0.1`) o su un server di calcolo interno alla rete aziendale privata (LAN). Nessun singolo byte viene trasmesso all'esterno.

### 10.1.2 Conformità Normativa: GDPR, HIPAA e Settori Critici
L'architettura 100% offline di Antigravity consente di soddisfare i requisiti più severi delle normative internazionali:

1. **GDPR (Regolamento UE 2016/679)**:
   - *Sovranità Territoriale e Trasferimento Dati (Art. 44 e ss.)*: Impedisce qualsiasi trasferimento transfrontaliero di dati personali verso server extra-UE non conformi.
   - *Privacy by Design & by Default (Art. 25)*: Il runtime è configurato per azzerare la telemetria e trattenere ogni manufatto esclusivamente nello storage locale.
   - *Diritto all'Oblio e Tracciabilità*: I log delle interazioni dell'agente risiedono unicamente nel file system locale dello sviluppatore.
2. **HIPAA (Health Insurance Portability and Accountability Act)**:
   - *Protezione dei Protected Health Information (PHI)*: Nello sviluppo di software medicale o nell'analisi di dataset clinici, l'elaborazione dei dati tramite API cloud pubbliche costituisce una violazione federale in assenza di un Business Associate Agreement (BAA) esplicito. Il funzionamento offline garantisce che nessun record clinico venga esfiltrato.
3. **Settori Difesa, Finanza e Sistemi SCADA**:
   - In ambienti industriali critici (infrastrutture energetiche, sistemi bancari di core transazionale, ambienti militari classificati), l'accesso a internet è fisicamente interdetto da barriere hardware (**air-gap**). Antigravity permette di sfruttare l'assistenza avanzata dell'AI anche all'interno di tali perimetri ermetici.

### 10.1.3 Matrice Comparativa: Modelli Cloud vs Modelli Locali
La scelta tra inferenza cloud e inferenza locale comporta un trade-off ingegneristico tra ampiezza del contesto e controllo operativo:

| Parametro Operativo | Modelli Cloud (es. Gemini 2.5 Pro / Flash) | Modelli Locali (es. Qwen 2.5 Coder / DeepSeek) |
| :--- | :--- | :--- |
| **Finestra di Contesto** | Massiva: fino a 1.000.000 - 2.000.000+ token | Ottimizzata: tipicamente 16.384 - 32.768 token (fino a 65k) |
| **Latenza al Primo Token (TTFT)** | Variabile (200 - 1500 ms in base al carico di rete) | Ultra-bassa e deterministica (30 - 150 ms su GPU locale) |
| **Throughput di Generazione** | Elevato (60 - 120 token/s tramite infrastruttura Google) | Dipendente dall'hardware (20 - 90 token/s su GPU consumer/pro) |
| **Costi Operativi** | Opex: fatturazione variabile a consumo (token-based) | Capex: costo fisso hardware (ammortizzabile nel tempo) |
| **Privacy & Riservatezza** | Dipendente dai termini di servizio del fornitore | **Assoluta**: zero telemetria, dati non replicati |
| **Funzionamento Offline** | Impossibile (richiede connessione costante) | **Nativo**: funziona in assenza totale di connettività |
| **Personalizzazione Pesi** | Limitata a system prompt e context window | Completa: fine-tuning LoRA, quantizzazioni su misura |

---

## 10.2 Configurazione dei Backend LLM Locali

Antigravity adotta lo standard de facto dell'industria per la comunicazione con i modelli di linguaggio: l'interfaccia **OpenAI-Compatible REST API**. Qualsiasi motore di inferenza che esponga gli endpoint `/v1/chat/completions`, `/v1/models` ed `/v1/embeddings` può essere utilizzato come motore di calcolo dell'agente.

```
┌────────────────────────────────────────────────────────────────────────┐
│                        ANTIGRAVITY RUNTIME                             │
│                      (Macchina Sviluppatore)                           │
│                                                                        │
│   ┌────────────────────┐                   ┌───────────────────────┐   │
│   │   Language Server  │ ── OpenAI REST ──►│   Local LLM Backend   │   │
│   │ (language_server)  │ ◄── Streaming ────│ (Ollama / vLLM / LMS) │   │
│   └─────────┬──────────┘                   └───────────┬───────────┘   │
│             │                                          │               │
│   ┌─────────┴──────────┐                               │ (Inference)   │
│   │ File System / AST  │                               ▼               │
│   │ Workspace Indexing │                   ┌───────────────────────┐   │
│   └────────────────────┘                   │ GPU VRAM (CUDA/Metal) │   │
│                                            │ Q4/Q8 Model Weights   │   │
│                                            └───────────────────────┘   │
└────────────────────────────────────────────────────────────────────────┘
                       ❌ ZERO TRAFFICO VERSO IL CLOUD
```

### 10.2.1 Ollama (Porta 11434) - Per Sviluppatori e Workstation Singole
Ollama rappresenta la soluzione più immediata ed efficiente per workstation individuali (Linux, macOS, Windows). Basato sul runtime `llama.cpp`, gestisce automaticamente l'allocazione dei layer tra VRAM e memoria di sistema.

#### 1. Download dei Modelli Ottimizzati per il Coding
Per il supporto al vibecoding e all'editing multi-file in Antigravity, le famiglie raccomandate sono **Qwen 2.5 Coder** e **DeepSeek-R1** (per reasoning logico):

```bash
# Modello bilanciato per GPU con 12-16 GB VRAM
ollama pull qwen2.5-coder:14b

# Modello ad altissima precisione per GPU con 24 GB VRAM (RTX 3090/4090)
ollama pull qwen2.5-coder:32b

# Modello per compiti di reasoning architetturale profondo
ollama pull deepseek-r1:14b
```

#### 2. Creazione di un Modelfile Ottimizzato per Antigravity
Per garantire che Ollama allochi una finestra di contesto sufficientemente ampia e risponda con parametri idonei alla generazione di codice, crea un file personalizzato denominato `Modelfile`:

```dockerfile
FROM qwen2.5-coder:14b

# Imposta la context window a 32k token (default Ollama è 2048 o 4096)
PARAMETER num_ctx 32768

# Temperatura bassa per ridurre allucinazioni sintattiche nel codice
PARAMETER temperature 0.2
PARAMETER top_p 0.95
PARAMETER repeat_penalty 1.1

# System prompt che impone precisione ingegneristica e assenza di testo superfluo
SYSTEM """Sei l'assistente ingegneristico di Antigravity. Rispondi con codice pulito, modulare, rigorosamente tipizzato ed esente da refusi. Non produrre testo conversazionale non richiesto."""
```

Compila e registra il modello locale con il comando:
```bash
ollama create antigravity-coder:14b -f ./Modelfile
```

#### 3. Avvio del Demone e Verifica dell'Endpoint
Avvia il server Ollama (ascolto predefinito su porta `11434`):
```bash
ollama serve
```

Verifica la disponibilità dell'API compatibile OpenAI tramite terminale:
```bash
curl http://localhost:11434/v1/models
```

### 10.2.2 vLLM (Porta 8000) - Per Server Enterprise, Cluster e Alto Throughput
**vLLM** è il motore di inferenza open source ad altissime prestazioni ideale per server dedicati, postazioni multi-GPU o laboratori aziendali con schede NVIDIA (CUDA). La sua caratteristica cardine è l'algoritmo **PagedAttention**, che memorizza le chiavi e i valori della KV Cache in blocchi di memoria non contigui, azzerando la frammentazione interna ed eliminando gli sprechi di VRAM tipici dei runtime tradizionali.

#### 1. Script di Avvio per Linux (Bash)
Esegui il server compatibile OpenAI configurando la dimensione massima del contesto e la percentuale di memoria riservata:

```bash
#!/usr/bin/env bash
python3 -m vllm.entrypoints.openai.api_server \
  --model Qwen/Qwen2.5-Coder-32B-Instruct-AWQ \
  --port 8000 \
  --host 0.0.0.0 \
  --served-model-name qwen2.5-coder-32b \
  --max-model-len 32768 \
  --gpu-memory-utilization 0.92 \
  --quantization awq \
  --tensor-parallel-size 1 \
  --enforce-eager \
  --disable-log-requests
```

#### 2. Script di Avvio per Windows (PowerShell)
Su workstation Windows dotate di ambiente Python con PyTorch CUDA:

```powershell
python -m vllm.entrypoints.openai.api_server `
  --model "Qwen/Qwen2.5-Coder-14B-Instruct-AWQ" `
  --port 8000 `
  --host 127.0.0.1 `
  --served-model-name "qwen2.5-coder-14b" `
  --max-model-len 32768 `
  --gpu-memory-utilization 0.90 `
  --quantization awq `
  --enforce-eager
```

#### Spiegazione dei Parametri Critici:
- `--max-model-len 32768`: Alloca dinamicamente i blocchi di attenzione per una finestra operativa di 32k token.
- `--gpu-memory-utilization 0.92`: Indica a vLLM di occupare il 92% della VRAM complessiva della scheda; la quota residua dopo il caricamento dei pesi viene integralmente adibita a pool di blocchi KV Cache per il continuous batching.
- `--quantization awq`: Abilita l'accelerazione dei tensori quantizzati Activation-aware Weight Quantization (4-bit) con conservazione quasi totale delle prestazioni logico-sintattiche.
- `--tensor-parallel-size <N>`: Distribuisce il calcolo su $N$ GPU parallele mediante sharding tensoriale (richiede interconnessione PCIe veloce o NVLink).

### 10.2.3 LM Studio (Porta 1234) - Per Utenti Desktop e Test Visivi
Per sviluppatori che preferiscono un'interfaccia grafica per il download e il benchmarking di pesi in formato GGUF:
1. Scarica il modello desiderato (es. `Qwen2.5-Coder-14B-Instruct-GGUF` o `Qwen2.5-Coder-32B-Instruct-GGUF`) da Hugging Face tramite la barra di ricerca interna di LM Studio.
2. Nel pannello di destra, configura **GPU Offload**:
   - Sposta lo slider su `Max` oppure imposta `n_gpu_layers` su un valore sufficiente a caricare l'intero modello in memoria grafica (es. 48 o 64 layer).
   - Imposta **Context Length** su `16384` o `32768`.
3. Clicca sulla scheda **Local Server** (icona con due frecce contrapposte) e premi **Start Server**.
4. L'endpoint OpenAI-compatibile è immediatamente accessibile su `http://localhost:1234/v1`.

---

## 10.3 Hardware Mapping & Calcolo Matematico della VRAM

Il funzionamento fluido di un modello locale per task complessi di vibecoding dipende dalla corretta allocazione della memoria grafica. Se la memoria richiesta supera la capacità fisica della GPU, il sistema incorre in un crash fatale per esaurimento di memoria (**CUDA Out of Memory - OOM**) oppure retrocede all'offloading su RAM di sistema, con un crollo del throughput di generazione da 30 token/s a meno di 1-2 token/s.

L'equazione fondamentale del consumo di VRAM è:

$$\text{VRAM}_{\text{totale}} = \text{VRAM}_{\text{pesi}} + \text{VRAM}_{\text{KV\_Cache}} + \text{VRAM}_{\text{attivazioni\_overhead}}$$

### 10.3.1 Calcolo della Memoria dei Pesi ($\text{VRAM}_{\text{pesi}}$)
La memoria occupata dai soli pesi del modello statico è calcolata dalla formula:

$$\text{VRAM}_{\text{pesi}} \approx P \times \frac{B}{8} \times 1.2$$

dove:
- $P$ è il numero totale di parametri del modello espresso in miliardi ($7 \times 10^9$, $14 \times 10^9$, $32 \times 10^9$).
- $B$ è la precisione di quantizzazione espressa in bit per parametro:
  - **FP16 / BF16**: $B = 16$ bit (2.0 Byte per parametro).
  - **Q8_0**: $B = 8$ bit (1.0 Byte per parametro).
  - **Q5_K_M**: $B \approx 5.5$ bit (~0.69 Byte per parametro).
  - **Q4_K_M / AWQ-4bit**: $B \approx 4.5$ bit (~0.56 Byte per parametro).
- Il coefficiente **$1.2$** è un moltiplicatore empirico di sicurezza (+20%) che tiene conto delle matrici di embedding iniziali e finali (spesso mantenute a 16-bit non quantizzati), dei pesi di normalizzazione dei layer (RMSNorm) e delle strutture dati di dequantizzazione a runtime.

### 10.3.2 Calcolo della Memoria della KV Cache con GQA ($\text{VRAM}_{\text{KV\_Cache}}$)
La **KV Cache** memorizza i tensori delle chiavi (Key) e dei valori (Value) per ogni token presente nella finestra di contesto. Nei modelli moderni dotati di **Grouped-Query Attention (GQA)**, il numero di teste di attenzione per Key e Value ($H_{\text{kv}}$) è drasticamente inferiore rispetto alle teste delle Query ($H_q$), riducendo l'impatto di memoria.

La formula esatta è:

$$\text{VRAM}_{\text{KV\_Cache}} = 2 \times L \times H_{\text{kv}} \times D_{\text{head}} \times C \times \frac{B_{\text{kv}}}{8}$$

dove:
- Il fattore **$2$** tiene conto dei due tensori distinti per ogni strato (Key e Value).
- $L$ è il numero di strati Transformer (layers) della rete.
- $H_{\text{kv}}$ è il numero di teste di attenzione per Key e Value (es. 8 teste per Qwen 2.5 32B, a fronte di 40 teste Query).
- $D_{\text{head}}$ è la dimensione del vettore di ciascuna testa (standard industriale: 128 byte).
- $C$ è la lunghezza della finestra di contesto attiva espressa in token (es. 16.384 o 32.768).
- $B_{\text{kv}}$ è la precisione dei token di cache (16 bit per FP16 standard, oppure 8 bit per cache FP8).

#### Esempio Numerico Applicativo (Qwen 2.5 Coder 32B):
Parametri strutturali: $P = 32.5\text{B}$, $L = 64$, $H_{\text{kv}} = 8$, $D_{\text{head}} = 128$, quantizzazione pesi a 4-bit ($B = 4.5$), precisione cache a 16-bit ($B_{\text{kv}} = 16$).
1. **Memoria Pesi**:
   $$\text{VRAM}_{\text{pesi}} = 32.5 \times \frac{4.5}{8} \times 1.2 \approx 21.9\text{ GB}$$
2. **Memoria KV Cache con contesto a 8.192 token**:
   $$\text{VRAM}_{\text{KV}} = 2 \times 64 \times 8 \times 128 \times 8.192 \times \frac{16}{8} \text{ Byte} \approx 2.15\text{ GB}$$
3. **Memoria KV Cache con contesto a 32.768 token**:
   $$\text{VRAM}_{\text{KV}} = 2 \times 64 \times 8 \times 128 \times 32.768 \times \frac{16}{8} \text{ Byte} \approx 8.59\text{ GB}$$

> ⚠️ **Avvertenza sull'Impatto del Contesto**: Portare la context window da 8k a 32k token su un modello da 32B richiede **6.44 GB di VRAM aggiuntiva** unicamente per la KV Cache! Su una GPU da 24 GB (come la RTX 4090), il modello 32B a 4-bit richiede l'attivazione della quantizzazione della cache a 8-bit (`--kv-cache-dtype fp8` in vLLM) per non incorrere in OOM a 32k token.

### 10.3.3 Matrice di Dimensionamento Hardware (I Tre Livelli)

| Livello Hardware | Specifiche Macchina | Modelli Raccomandati | Quantizzazione | Contesto Max Consigliato | Throughput Medio |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Tier 1: Entry / Laptop** | 16 GB RAM + GPU 6-8 GB VRAM (RTX 3060/4060 o Apple M1/M2/M3 16GB) | Qwen2.5-Coder-7B<br>Llama-3.1-8B | Q4_K_M (~4.8 GB pesi + ~1.2 GB KV) | 8.192 - 16.384 token | 35 - 55 tok/s |
| **Tier 2: Pro Workstation** | 32-64 GB RAM + GPU 16-24 GB VRAM (RTX 3090/4090 o Apple M2/M3 Max 36-64GB) | Qwen2.5-Coder-14B (Q8/Q5)<br>Qwen2.5-Coder-32B (Q4/AWQ) | Q4_K_M / AWQ (~19-21 GB totali) | 16.384 - 32.768 token | 22 - 38 tok/s |
| **Tier 3: Enterprise / Multi-GPU** | 64-128+ GB RAM + 48-96 GB VRAM (2x RTX 4090, A6000, Apple M2/M3 Ultra 128GB) | Qwen2.5-Coder-32B (FP16/Q8)<br>DeepSeek-Coder-V2 (Lite / 70B) | Q8_0 o FP16 nativo (~35-65 GB) | 32.768 - 65.536 token | 40 - 75 tok/s |

### 10.3.4 Quantizzazioni a Confronto: GGUF vs AWQ vs EXL2
- **GGUF (GPT-Generated Unified Format)**: Formato standard di `llama.cpp` e Ollama. Supporta quantizzazioni a blocchi (K-quants come `Q4_K_M`, `Q5_K_M`). Eccellente flessibilità per offload parziale tra CPU e GPU. Nel coding, non scendere mai sotto `Q4_K_M` per evitare corruzione delle parentesi e delle indentazioni sintattiche.
- **AWQ (Activation-aware Weight Quantization)**: Preserva con precisione a 16-bit l'1% dei pesi più critici (quelli che subiscono le attivazioni maggiori), quantizzando il restante 99% a 4-bit. È il formato ideale per vLLM su GPU NVIDIA, garantendo throughput 2x-3x superiore rispetto a GGUF a parità di VRAM.
- **EXL2 (ExLlamaV2)**: Ottimizzato per inferenza ultra-veloce su singola GPU con quantizzazione a bit-rate frazionario (es. 4.25 bpw). Estremamente reattivo per l'autocompletamento inline.

---

## 10.4 Configurazione di Antigravity per Provider Locali

Per istruire Antigravity all'utilizzo del motore locale al posto delle API cloud predefinite, occorre modificare la configurazione globale in `~/.gemini/config/config.json` oppure creare un file di configurazione con ambito limitato al workspace in `.agents/config.json`.

### 10.4.1 Configurazione Completa (`config.json`)
La seguente configurazione JSON disattiva le chiamate cloud, imposta l'endpoint REST OpenAI-compatibile, definisce la context window hardware e attiva la sandbox di sicurezza:

```json
{
  "activeProvider": "local_openai_compatible",
  "providers": {
    "local_openai_compatible": {
      "type": "openai-compatible",
      "baseUrl": "http://127.0.0.1:11434/v1",
      "apiKey": "local-dummy-key",
      "defaultModel": "qwen2.5-coder:14b",
      "contextWindow": 32768,
      "maxOutputTokens": 4096,
      "temperature": 0.2,
      "topP": 0.95
    }
  },
  "offlineMode": true,
  "userSettings": {
    "enableTelemetry": false,
    "autoExecutionPolicy": "ask_for_approval",
    "enableTerminalSandbox": true,
    "nonWorkspaceFileAccessPolicy": "deny",
    "networkAccessPolicy": "local_only"
  },
  "embeddings": {
    "provider": "local_openai_compatible",
    "baseUrl": "http://127.0.0.1:11434/v1",
    "model": "bge-m3:latest",
    "dimensions": 1024,
    "batchSize": 32
  }
}
```

### 10.4.2 Gestione degli Embeddings Locali per il Codebase Indexing
Antigravity include un motore di indicizzazione vettoriale che indicizza funzioni, classi e documentazione del repository per rispondere a prompt architetturali. In modalità standard, gli embedding vengono calcolati via cloud. 

In modalità 100% offline, è necessario specificare un modello di embedding locale ospitato su Ollama:
```bash
# Scarica il modello multilingue ad alta densità per codice e testo
ollama pull bge-m3
```
Grazie al blocco `"embeddings"` presente nel file di configurazione, la vettorizzazione dei file del progetto avviene interamente in locale sulla scheda grafica, popolando il database vettoriale interno (`.agents/index/`) senza emettere pacchetti verso l'esterno.

---

## 10.5 Air-Gapped, Switch Offline & Isolamento di Rete

La privacy e la conformità normativa non devono basarsi unicamente sulla fiducia nell'applicazione: devono essere garantite da barriere architetturali a livello di sistema operativo.

### 10.5.1 La Variabile d'Ambiente `ANTIGRAVITY_OFFLINE=1`
L'impostazione della variabile d'ambiente `ANTIGRAVITY_OFFLINE=1` istruisce il runtime di Antigravity a disabilitare in modo non aggirabile:
1. I controlli periodici di aggiornamento software e download di estensioni remote.
2. L'invio di metriche analitiche e report di crash.
3. Il caricamento di risorse web esterne nelle interfacce grafiche.
4. Qualsiasi fallback su provider di intelligenza artificiale cloud in caso di timeout del backend locale.

Per impostarla permanentemente:
- **Su Windows (PowerShell)**:
  ```powershell
  [System.Environment]::SetEnvironmentVariable('ANTIGRAVITY_OFFLINE', '1', [System.EnvironmentVariableTarget]::User)
  ```
- **Su Linux / macOS (Bash / Zsh)**:
  ```bash
  echo 'export ANTIGRAVITY_OFFLINE=1' >> ~/.bashrc
  source ~/.bashrc
  ```

### 10.5.2 Regole Firewall a Livello di Sistema Operativo
Per garantire la conformità air-gapped anche in presenza di una scheda di rete attiva sulla macchina, configura le regole del firewall del sistema operativo per bloccare qualsiasi traffico in uscita generato dal binario di Antigravity, autorizzando esclusivamente il traffico di loopback locale verso `127.0.0.1`.

#### Regole per Windows Defender Firewall (PowerShell con privilegi di Amministratore):
```powershell
# 1. Individua il percorso dell'eseguibile di Antigravity
$AntigravityBin = "$env:LOCALAPPDATA\Programs\antigravity\Antigravity.exe"

# 2. Rimuovi eventuali regole preesistenti
Remove-NetFirewallRule -DisplayName "Antigravity-Block-Outbound" -ErrorAction SilentlyContinue
Remove-NetFirewallRule -DisplayName "Antigravity-Allow-Localhost" -ErrorAction SilentlyContinue

# 3. Blocca tutte le connessioni in uscita verso internet
New-NetFirewallRule -DisplayName "Antigravity-Block-Outbound" `
  -Direction Outbound `
  -Program $AntigravityBin `
  -Action Block `
  -Profile Any `
  -Description "Blocca qualsiasi connessione internet outbound per Antigravity"

# 4. Autorizza unicamente il loopback locale per inferenza (Ollama porta 11434, vLLM 8000)
New-NetFirewallRule -DisplayName "Antigravity-Allow-Localhost" `
  -Direction Outbound `
  -Program $AntigravityBin `
  -RemoteAddress 127.0.0.1 `
  -Action Allow `
  -Profile Any `
  -Description "Consenti esclusivamente la connessione verso i motori di inferenza locali"
```

#### Regole per Linux (iptables):
```bash
#!/usr/bin/env bash
# Assicurarsi che Antigravity sia eseguito con un gruppo utente dedicato (es. antigravity-local)
sudo iptables -A OUTPUT -m owner --gid-owner antigravity-local -d 127.0.0.1 -j ACCEPT
sudo iptables -A OUTPUT -m owner --gid-owner antigravity-local -j DROP
```

#### Verifica e Monitoraggio dei Socket di Rete:
Per verificare in qualunque momento che nessun socket sia connesso a server esterni:
- **Su Windows (PowerShell)**:
  ```powershell
  Get-NetTCPConnection -OwningProcess (Get-Process Antigravity).Id | Select-Object LocalAddress, LocalPort, RemoteAddress, RemotePort, State
  ```
- **Su Linux**:
  ```bash
  ss -tunap | grep Antigravity
  ```
Tutti i record devono mostrare `127.0.0.1` o `localhost` come `RemoteAddress`.

### 10.5.3 PreToolUse Hook di Data Loss Prevention (DLP)
Antigravity supporta gli **Hook del Ciclo di Vita** (approfonditi nel Capitolo 5). Per evitare che l'agente o lo sviluppatore passino inavvertitamente token segreti o dati sensibili a tool locali (es. log di compilazione o script di commit), è possibile configurare un hook di scansione preventiva DLP.

Crea il file `.agents/hooks.json` nel workspace:
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "name": "dlp-secret-scanner",
        "description": "Scansiona parametri dei tool per intercettare credenziali prima dell'esecuzione",
        "command": "python",
        "args": [".agents/hooks/dlp_scanner.py"],
        "timeoutMs": 3000
      }
    ]
  }
}
```

Implementa lo script di controllo in `.agents/hooks/dlp_scanner.py`:
```python
#!/usr/bin/env python3
import sys
import json
import re

PATTERNS = [
    (r"(?i)bearer\s+[a-zA-Z0-9_\-\.]{20,}", "Token di autorizzazione Bearer"),
    (r"(?i)(?:api_key|apikey|secret)[\s:=]+[\"']?([a-zA-Z0-9_\-]{16,})[\"']?", "API Key / Segreto"),
    (r"(?i)-----BEGIN\s+(?:RSA\s+)?PRIVATE\s+KEY-----", "Chiave privata crittografica"),
    (r"[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+", "Indirizzo Email personale")
]

def audit_payload():
    try:
        raw_input = sys.stdin.read()
        if not raw_input.strip():
            sys.exit(0)
        
        data = json.loads(raw_input)
        args_str = json.dumps(data.get("toolArguments", {}))
        
        for regex, desc in PATTERNS:
            if re.search(regex, args_str):
                sys.stderr.write(f"🛑 [BLOCCO DLP AIR-GAPPED]: Rilevato dato sensibile vietato ({desc}). Operazione annullata.\n")
                sys.exit(1) # Exit code != 0 blocca l'esecuzione del tool
        
        sys.exit(0)
    except Exception as e:
        sys.stderr.write(f"Errore durante l'audit DLP: {str(e)}\n")
        sys.exit(1)

if __name__ == "__main__":
    audit_payload()
```

---

## 10.6 Tutorial Passo-Passo: Workflow di Coding e Test 100% Offline

> **Ambiente Operativo:** [Usa in Antigravity IDE]

In questo tutorial realizzeremo un modulo Python per la gestione sicura di token a tempo con validazione HMAC, accompagnato da una suite di test unitari con `pytest`. L'intero processo — dalla generazione del codice all'esecuzione della suite di test — avverrà rigorosamente offline su un backend locale Ollama.

### Step 1: Verifica dello Stato del Backend Locale
Prima di aprire il workspace in Antigravity, accertati che il modello locale sia operativo e in ascolto:

```powershell
Invoke-RestMethod -Uri "http://localhost:11434/v1/models" -Method Get | Select-Object -ExpandProperty data
```
L'output confermerà la disponibilità del modello `qwen2.5-coder:14b`.

### Step 2: Configurazione del Workspace
Nella cartella principale del progetto, crea il file `.agents/config.json`:
```json
{
  "activeProvider": "local_openai_compatible",
  "providers": {
    "local_openai_compatible": {
      "type": "openai-compatible",
      "baseUrl": "http://127.0.0.1:11434/v1",
      "apiKey": "local-offline",
      "defaultModel": "qwen2.5-coder:14b",
      "contextWindow": 32768
    }
  },
  "offlineMode": true
}
```

### Step 3: Interazione e Generazione di Codice e Test Offline

Apri la chat dell'agente (`Ctrl+L` nell'IDE o pannello chat dell'App 2.0) e invia il prompt:

**🗣️ Tu (Prompt):**
> [Usa in Antigravity IDE]
> Lavoriamo in ambiente 100% offline. Crea un modulo Python `security/vault.py` che implementi una classe `ExpiringTokenVault`.
> Requisiti:
> 1. Metodo `generate_token(payload: dict, ttl_seconds: int) -> str` che firma il payload JSON con HMAC-SHA256 usando una chiave segreta passata al costruttore e vi allega una scadenza UNIX UTC.
> 2. Metodo `verify_token(token: str) -> dict` che verifica la firma crittografica e solleva `ValueError` se il token è manomesso o scaduto.
> 3. Scrivi i test unitari esaustivi in `tests/test_vault.py` usando `pytest`, coprendo: generazione valida, verifica corretta, rilevamento manomissione e token scaduto.
> 4. Esegui i test nel terminale locale e conferma che la suite passi al 100%. Non tentare chiamate di rete.

**🤖 Agente (Risposta):**
> Ricevuto. Procedo in conformità con la policy offline attiva (`local_openai_compatible` su `http://127.0.0.1:11434/v1`).
> 
> Creo la directory `security/` e il file `security/vault.py`:

```python
# security/vault.py
import hmac
import hashlib
import json
import base64
import time
from typing import Dict, Any

class ExpiringTokenVault:
    def __init__(self, secret_key: bytes):
        if len(secret_key) < 32:
            raise ValueError("La chiave segreta deve contenere almeno 32 byte.")
        self._secret = secret_key

    def generate_token(self, payload: Dict[str, Any], ttl_seconds: int) -> str:
        data = {
            "exp": int(time.time()) + ttl_seconds,
            "data": payload
        }
        raw_json = json.dumps(data, separators=(',', ':'), sort_keys=True).encode('utf-8')
        signature = hmac.new(self._secret, raw_json, hashlib.sha256).digest()
        
        encoded_data = base64.urlsafe_b64encode(raw_json).decode('utf-8')
        encoded_sig = base64.urlsafe_b64encode(signature).decode('utf-8')
        return f"{encoded_data}.{encoded_sig}"

    def verify_token(self, token: str) -> Dict[str, Any]:
        parts = token.split('.')
        if len(parts) != 2:
            raise ValueError("Struttura token non valida.")
        
        encoded_data, encoded_sig = parts
        try:
            raw_json = base64.urlsafe_b64decode(encoded_data.encode('utf-8'))
            signature = base64.urlsafe_b64decode(encoded_sig.encode('utf-8'))
        except Exception as e:
            raise ValueError("Decodifica Base64 fallita.") from e
        
        expected_sig = hmac.new(self._secret, raw_json, hashlib.sha256).digest()
        if not hmac.compare_digest(signature, expected_sig):
            raise ValueError("Firma crittografica non valida: rilevata manomissione.")
        
        data = json.loads(raw_json.decode('utf-8'))
        if time.time() > data.get("exp", 0):
            raise ValueError("Token scaduto.")
        
        return data.get("data", {})
```

> Ora creo la suite di collaudo in `tests/test_vault.py`:

```python
# tests/test_vault.py
import pytest
import time
import os
from security.vault import ExpiringTokenVault

@pytest.fixture
def vault():
    secret = os.urandom(32)
    return ExpiringTokenVault(secret)

def test_token_generation_and_validation(vault):
    payload = {"user_id": 42, "role": "admin"}
    token = vault.generate_token(payload, ttl_seconds=60)
    assert isinstance(token, str)
    
    recovered = vault.verify_token(token)
    assert recovered == payload

def test_tampered_token_raises_error(vault):
    token = vault.generate_token({"user": "alice"}, ttl_seconds=60)
    parts = token.split('.')
    tampered = parts[0][:-1] + ('A' if parts[0][-1] != 'A' else 'B') + '.' + parts[1]
    
    with pytest.raises(ValueError, match="Firma crittografica non valida"):
        vault.verify_token(tampered)

def test_expired_token_raises_error(vault):
    token = vault.generate_token({"user": "bob"}, ttl_seconds=-5)
    with pytest.raises(ValueError, match="Token scaduto"):
        vault.verify_token(token)

def test_short_secret_key():
    with pytest.raises(ValueError, match="almeno 32 byte"):
        ExpiringTokenVault(b"chiave_troppo_corta")
```

> Eseguo ora il comando nel terminale locale di Antigravity: `pytest tests/test_vault.py -v`.
> 
> ```text
> ============================= test session starts =============================
> platform win32 -- Python 3.11.9, pytest-8.3.2, pluggy-1.5.0
> rootdir: C:\Users\VincenzoOriti\OneDrive - Università di Pavia\Developing\Antigravity IDE starter pack
> collected 4 items
> 
> tests/test_vault.py::test_token_generation_and_validation PASSED         [ 25%]
> tests/test_vault.py::test_tampered_token_raises_error PASSED             [ 50%]
> tests/test_vault.py::test_expired_token_raises_error PASSED              [ 75%]
> tests/test_vault.py::test_short_secret_key PASSED                        [100%]
> 
> ============================== 4 passed in 0.08s ==============================
> ```
> 
> Tutti i 4 test sono stati superati con successo in 80 millisecondi. Il codice è pronto e nessun pacchetto di rete ha lasciato l'ambiente locale.

---

## 10.7 Best Practice di Prompt Engineering per Modelli Locali

I modelli open source eseguiti in locale (da 7B a 32B parametri) possiedono capacità logiche eccellenti nella programmazione, ma dispongono di un budget di attenzione e di contesto più contenuto rispetto ai giganti cloud da trilioni di parametri. Per ottenere il massimo rendimento, adotta le seguenti linee guida:

1. **Massimizza la Precisione del Riferimento Contestuale (`@file`)**:
   Non lasciare che l'agente ricerchi file a caso nell'intero albero del repository. Indica con esattezza i percorsi rilevanti:
   - ❌ *Prompt debole:* *"Refattorizza il database per supportare gli utenti."*
   - ✅ *Prompt ottimizzato:* *"Aggiorna `@models/user.py` e adegua `@repositories/user_repo.py` aggiungendo il campo `is_verified`."*

2. **Comprimi le Regole di Progetto (`.agents/rules/`)**:
   Mentre i modelli cloud possono gestire file di regole da decine di kilobyte senza degradare le prestazioni, un modello locale con 16k o 32k di contesto dedica preziosa memoria a ogni riga di system prompt. Mantieni i file `.md` delle regole al di sotto delle 100-150 righe, focalizzandoli su vincoli inderogabili di stile, librerie ammesse e convenzioni di testing.

3. **Temperatura Bassa per Codice Deterministico**:
   Mantieni il parametro `temperature` tra `0.1` e `0.2`. Nel codice sorgente, l'inventiva e la variazione stilistica sono deleterie: una temperatura bassa garantisce risposte stabili, corretto accoppiamento delle parentesi e coerenza con i tipi statici dichiarati.

4. **Scomposizione Atomica dei Compiti**:
   Se devi implementare una funzionalità complessa, non formulare un unico mega-prompt. Usa il comando `/plan` per scomporre il lavoro in step atomici da 1-2 file per volta, validando l'output ad ogni passaggio.

5. **Evita la Verbose Chat**:
   Nel Modelfile o nel prompt iniziale, inserisci sempre la clausola: *"Genera unicamente il blocco di codice richiesto e i comandi di test. Evita spiegazioni introduttive o riassunti discorsivi."* Ciò riduce il consumo di token di output e velocizza notevolmente l'esecuzione.

---

## 10.8 Riferimenti Incrociati & Risorse Correlate per Ambienti Locali

Per la consultazione dell'indice completo, torna all'[Indice Generale](../index.md). Di seguito i capitoli con collegamenti tecnici diretti al funzionamento offline:
- **Regole e Budget Token per Modelli Piccoli (Capitolo 4)**: Consulta [04_rules.md](./04_rules.md) per redigere regole dense sotto i 20k token.
- **Meccanica della Finestra di Contesto e Attenzione (Capitolo 6)**: Consulta [06_advanced_architecture.md](./06_advanced_architecture.md) per i dettagli teorici sull'equazione di attenzione e scaling dei token.
- **Server MCP su Database Locali (Capitolo 9)**: Consulta [09_mcp_servers.md](./09_mcp_servers.md) per collegare server SQLite e filesystem isolati senza connessione internet.
- **Esecuzione Headless da Terminale (Capitolo 12)**: Consulta [12_cli_reference.md](./12_cli_reference.md) per lanciare la CLI `agy` su workstation offline e server SCADA isolati.

---
[⬅️ Torna all'Indice](../index.md) | [Capitolo Precedente: 9. Server MCP](./09_mcp_servers.md) | [Prossimo Capitolo: 11. Controllo Remoto](./11_remote_control.md)
