# Abilità OpenCode: Prevenzione delle allucinazioni dell'IA

> Un'abilità per agenti OpenCode che previene le allucinazioni dell'IA imponendo verifica, riconoscimento dell'incertezza e risposte basate su prove.

[English](./README.md) | [中文](./README_CN.md) | [中文 (繁體)](./README_TW.md) | [Русский](./README_RU.md) | [Français](./README_FR.md) | [Deutsch](./README_DE.md) | Italiano | [Español](./README_ES.md) | [Español (Cuba)](./README_CU.md) | [Português](./README_PT.md) | [Português (Brasil)](./README_BR.md) | [العربية](./README_AR.md) | [हिन्दी](./README_HI.md)

## Caratteristiche

- **Verifica prima di affermare** — leggi i file, esegui il codice, consulta la documentazione prima di trarre conclusioni
- **Riconosci l'incertezza** — distingui chiaramente tra speculazione e fatti verificati
- **Non inventare** — non fabbricare mai errori, API, risultati di test o correzioni
- **Basati sulle prove** — cita percorsi di file, numeri di riga e output reali
- **Autoverifica** — lista di controllo in 10 punti prima di ogni risposta
- **Protezioni per la generazione di codice** — doppia verifica prima e dopo la scrittura del codice
- **Regole per strumento** — regole anti-allucinazione specifiche per read, write, edit, bash, grep, glob, ecc.
- **8 tipi di allucinazioni** — ogni tipo ha la propria strategia di prevenzione

## Installazione rapida

### Metodo 1: Clonare il repository (consigliato)

```bash
git clone https://github.com/magiccloudflash/opencode-skill-hallucination-prevention.git
```

Copia l'abilità nella tua configurazione globale di OpenCode:

```bash
# macOS / Linux
cp -r hallucination-prevention ~/.config/opencode/skills/

# Windows PowerShell
Copy-Item -Recurse hallucination-prevention "$env:USERPROFILE\.config\opencode\skills\"
```

### Metodo 2: Installazione manuale

```bash
# macOS / Linux
mkdir -p ~/.config/opencode/skills/hallucination-prevention
cp hallucination-prevention/SKILL.md ~/.config/opencode/skills/hallucination-prevention/

# Windows PowerShell
mkdir -p "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention"
Copy-Item hallucination-prevention/SKILL.md "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention\"
```

### Metodo 3: Come regola globale (caricamento automatico in ogni sessione — consigliato)

```bash
# macOS / Linux
cp AGENTS.md ~/.config/opencode/AGENTS.md

# Windows PowerShell
Copy-Item AGENTS.md "$env:USERPROFILE\.config\opencode\AGENTS.md"
```

> Il file globale `AGENTS.md` viene caricato automaticamente in ogni sessione, senza bisogno di invocare manualmente l'abilità.

## Utilizzo

### Su richiesta (Metodo 1, 2)

OpenCode scopre automaticamente l'abilità. L'agente la carica quando necessario:

```javascript
skill({ name: "hallucination-prevention" })
```

### Caricamento automatico (Metodo 3)

Le regole di `AGENTS.md` vengono iniettate automaticamente nel contesto di ogni sessione. **Nessuna azione manuale richiesta.**

## Configurazione dei permessi

In `opencode.json`:

```json
{
  "permission": {
    "skill": {
      "hallucination-prevention": "allow"
    }
  }
}
```

Per agenti specifici:

```json
{
  "agent": {
    "plan": {
      "permission": {
        "skill": {
          "hallucination-prevention": "allow"
        }
      }
    }
  }
}
```

I permessi supportano pattern con caratteri jolly:

| Permesso | Comportamento |
|----------|---------------|
| `allow` | L'abilità viene caricata immediatamente |
| `deny` | L'abilità è nascosta all'agente, accesso negato |
| `ask` | Richiede conferma prima del caricamento |

## Struttura del repository

```
opencode-skill-hallucination-prevention/
├── README.md                                 # Documentazione in inglese
├── README_CN.md                              # Documentazione in cinese semplificato
├── README_TW.md                              # Documentazione in cinese tradizionale
├── README_RU.md                              # Documentazione in russo
├── README_FR.md                              # Documentazione in francese
├── README_DE.md                              # Documentazione in tedesco
├── README_IT.md                              # Documentazione in italiano (questo file)
├── README_ES.md                              # Documentazione in spagnolo
├── README_CU.md                              # Documentazione in spagnolo cubano
├── README_PT.md                              # Documentazione in portoghese
├── README_BR.md                              # Documentazione in portoghese brasiliano
├── README_AR.md                              # Documentazione in arabo
├── README_HI.md                              # Documentazione in hindi
├── LICENSE                                   # Licenza MIT
├── opencode.json                             # Esempio di configurazione
├── AGENTS.md                                 # Regole globali (caricamento automatico)
└── hallucination-prevention/
    └── SKILL.md                              # File di definizione dell'abilità
```

## Contenuto dell'abilità v2.0

### Classificazione degli 8 tipi di allucinazioni

| Tipo | Descrizione | Esempio | Prevenzione |
|------|-------------|---------|-------------|
| **A: Fattuale** | Affermare fatti non verificati | "Questa funzione restituisce una Promise" (senza leggere il codice) | Leggi le fonti, esegui il codice |
| **B: Strutturale** | Inventare file/percorsi | "Il gestore è in src/routes/auth.ts" (il file non esiste) | Usa glob/ls per verificare |
| **C: Comportamentale** | Presumere il comportamento del codice | "Il bug è stato risolto" (senza eseguire il progetto) | Esegui il codice e mostra il risultato |
| **D: Esterna** | Inventare API/funzioni | "Lodash ha una funzione groupByAndSort()" (non esiste) | Verifica la documentazione o le fonti |
| **E: Risultato** | Immaginare risultati di test | "Tutti i test passano" (senza eseguirli) | Esegui i test, mostra il risultato reale |
| **F: Temporale** | Versioni/dati errati | "Node v24 ha aggiunto questa funzionalità" (v24 non esiste) | Verifica versione e changelog |
| **G: Autorità** | Fingere di aver verificato | "Ho confermato che la modifica è corretta" (senza rileggere) | Esegui la verifica e mostra le prove |
| **H: Riferimento** | Inventare URL/ID/commit | "Vedi https://github.com/foo/issues/99999" (link falso) | Verifica l'URL o indica "non verificato" |

### Regole per strumento

| Strumento | Regola principale |
|-----------|-------------------|
| **read** | Non indovinare il contenuto; verifica l'esistenza del file; rileggi dopo la modifica |
| **write** | Rileggi immediatamente dopo aver scritto; non creare documentazione senza richiesta |
| **edit** | Leggi sempre prima di modificare; rileggi dopo; rispetta l'indentazione |
| **bash** | Non inventare output di comandi; riesegui per verificare le correzioni; chiedi prima di azioni distruttive |
| **grep** | Conferma l'esistenza di funzioni/classi tramite grep |
| **glob** | Usa glob per verificare la struttura prima di riferire percorsi |
| **question** | Se le istruzioni sono ambigue, chiedi prima, agisci dopo |

### Lista di controllo in 10 punti

Prima di ogni risposta, verifica:

| # | Domanda | Azione se "No" |
|---|--------|----------------|
| 1 | Ho davvero LETTO i file di cui sto parlando? | Leggili ora |
| 2 | Ho davvero ESEGUITO i comandi di cui riporto l'output? | Eseguili ora |
| 3 | Ho davvero VERIFICATO il pacchetto/API che sto riferendo? | Verifica o segna come incerto |
| 4 | Sono sicuro che questo messaggio di errore sia reale? | Mostra l'output reale |
| 5 | Ho riletto i file dopo averli modificati? | Rileggili ora |
| 6 | Sto indovinando la struttura delle directory? | Elenca la directory |
| 7 | Sto indovinando una firma di funzione? | Leggi il codice sorgente |
| 8 | Ho affermato che una correzione funziona senza testarla? | Esegui il codice |
| 9 | Sto inventando opzioni di comando? | Controlla --help |
| 10 | La mia risposta potrebbe contenere dati inventati? | Mantieni solo l'output reale |

### Protocollo di ancoraggio contestuale

1. **Prima di operazioni importanti sui file**: rileggi i file sorgente pertinenti
2. **Dopo 5+ chiamate di strumenti**: rileggi i file modificati, verifica lo stato accumulato
3. **Quando torni a un'attività**: rileggi TUTTI i file pertinenti — non fidarti della memoria

### Regole specifiche per linguaggio/framework

- **JS/TS**: Verifica package.json prima di importare; verifica tsconfig.json
- **Python**: Verifica requirements.txt/pyproject.toml; verifica python --version
- **Rust**: Verifica Cargo.toml; verifica rust-toolchain.toml
- **Go**: Verifica go.mod; verifica go version
- **Shell**: Verifica la disponibilità dei comandi prima di scrivere script; testa i comandi prima di suggerirli

### Regole specifiche di Git

- Non inventare mai hash di commit — usa `git log`
- Non presumere mai messaggi di commit — usa `git log --oneline`
- Non inventare mai numeri di PR o ID di Issue
- Dopo un commit, esegui `git status` e `git log -1`
- Prima di affermare l'esistenza di un branch, esegui `git branch -a`

### Livelli di confidenza

- **Verificato**: Ho letto/eseguito/verificato direttamente
- **Probabile**: Prove solide ma senza verifica diretta
- **Speculativo**: Inferenza basata su schemi simili, necessita verifica
- **Sconosciuto**: Non determinabile dalle informazioni disponibili

Per impostazione predefinita, usa il livello **Verificato**.

### Protocollo di escalation (quando fermarsi e chiedere all'utente)

Fermati immediatamente e chiedi all'utente quando:
1. Le istruzioni sono ambigue, con molteplici interpretazioni possibili
2. Lo strumento/la libreria richiesto non è disponibile
3. L'operazione richiede privilegi elevati
4. Ciò che osservi contraddice la descrizione dell'utente
5. L'attività richiede conoscenze che non puoi verificare
6. L'operazione potrebbe causare perdita di dati o problemi di sicurezza
7. Il contesto è cambiato troppo per essere seguito con precisione

## Licenza

MIT — consulta il file [LICENSE](LICENSE)

## Contributi

Issue e Pull Requests sono benvenuti:

https://github.com/magiccloudflash/opencode-skill-hallucination-prevention

---

**Navigazione** : [English](./README.md) | [中文](./README_CN.md) | [中文 (繁體)](./README_TW.md) | [Русский](./README_RU.md) | [Français](./README_FR.md) | [Deutsch](./README_DE.md) | **Italiano** | [Español](./README_ES.md) | [Español (Cuba)](./README_CU.md) | [Português](./README_PT.md) | [Português (Brasil)](./README_BR.md) | [العربية](./README_AR.md) | [हिन्दी](./README_HI.md)
