Sulla base del codice che hai condiviso e dei risultati ottenuti, ecco un README completo e professionale:

---

# VizDoom — TakeCover

Questo repository contiene il codice per la sperimentazione di architetture ibride tra **Deep Reinforcement Learning (DRL)** e **Large Language Models (LLM)** nello scenario *TakeCover* di [ViZDoom]. Il progetto confronta cinque varianti di agente, sviluppando, oltre ad un agente di RL puro, 2 varianti, rispettivamente reward e policy diretta.

---

## Scenario

**TakeCover** è uno scenario di ViZDoom in cui l'agente deve sopravvivere il più a lungo possibile evitando i proiettili lanciati dai nemici. Lo spazio delle azioni è binario (movimento a sinistra / destra) e l'osservazione è visiva. La difficoltà del task risiede nella reattività richiesta: le decisioni devono essere prese a ogni frame, rendendo la latenza di inferenza un fattore critico.
---

## Agenti

### Baseline
PPO standard addestrato con il reward nativo di ViZDoom (+1 per ogni tic sopravvissuto). Nessun componente LLM. Funge da riferimento per tutte le comparazioni.

### LLM-Driven Reward (Llama / Qwen)
Il reward di training nativo viene sostituito da un segnale prodotto da un LLM locale. A ogni step, lo stato dell'ambiente viene descritto in linguaggio naturale, mediante il parsing di un perception layer, e sottoposto al modello, che restituisce un valore di reward. La policy è comunque appresa tramite PPO.

- **Modello Llama**: `llama3.2:3b` via Ollama
- **Modello Qwen**: `qwen3:4b` via Ollama

### LLM-Driven Policy (Llama / Qwen)
L'LLM sostituisce direttamente la policy di PPO: a ogni step riceve una descrizione testuale dello stato e seleziona l'azione da eseguire. 

---

## Setup

### Requisiti
Nella repository è riportato il file `requirements.txt` contenente tutte le dipendenze necessarie per la run del repository.

```bash
pip install -r requirements.txt
```

L'inferenza LLM locale richiede [Ollama](https://ollama.com) con i modelli:

```bash
ollama pull llama3.2:3b
ollama pull qwen3:4b
```

### Configurazione percorsi

Nel file di valutazione, verificare che `CONFIG_PATH` punti correttamente al file `.cfg` dello scenario:

```python
CONFIG_PATH = './github/ViZDoom/scenarios/take_cover.cfg'
```

---

## Valutazione

Per riprodurre i risultati runnare il notebook `Evaluate_RL_Variants.ipynb`. Il notebook:
1. Carica i cinque modelli da `models/`
2. Calibra la soglia di sopravvivenza sul Baseline (100 episodi)
3. Valuta ciascun agente su 1.000 episodi indipendenti
4. Stampa la tabella comparativa e salva i grafici in `logs/`

---

## Citazione

Se utilizzi questo codice in una pubblicazione, cita il repository e lo scenario originale:

```
Kempka et al., "ViZDoom: A Doom-based AI Research Platform for Visual Reinforcement Learning", IEEE CIG 2016.
```