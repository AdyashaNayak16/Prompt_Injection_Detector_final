#  SHIELD — Smart Heuristic Injection Evasion & Labeling Detector

> An adversarial prompt injection detection system built with a two-model pipeline: a DistilBERT classifier and an LLM-powered red-team attack generator that continuously hardens the detector through iterative adversarial training.

[

![Live Demo](https://img.shields.io/badge/Live%20Demo-Streamlit-ff4b4b?style=for-the-badge&logo=streamlit)

](https://promptinjectiondetectorfinal-5w4i7tru87uhkrushppzlo.streamlit.app/)




![Code](https://img.shields.io/badge/Code-GitHub-black?style=for-the-badge&logo=github)

(https://github.com/AdyashaNayak16/Prompt_Injection_Detector_final)

---

## What is Prompt Injection?

Prompt injection is an attack where malicious user input overrides an LLM's original instructions — making it leak system prompts, bypass safety guidelines, or perform unintended actions. As LLMs get deployed in production systems, detecting these attacks in real-time becomes critical.

---

## What Makes SHIELD Different

Most injection detectors are static — they train once and stop. SHIELD uses an **adversarial loop**:

1. A detector classifies prompts as injection or clean
2. A red-team LLM generates evasion variants of known attacks
3. The attacks that fool the detector get added to training data
4. The detector retrains — now harder to fool
5. Repeat

This mirrors how real attackers behave: iteratively probing and adapting.


## Architecture

```
Dataset (1,639 samples)
    ↓
TF-IDF Baseline (RF/SVM/LR)
    ↓
DistilBERT Fine-tune (Colab T4 GPU)
    ↓
Red-Team Generator (Groq LLaMA 3.3 70B)
    ↓
Adversarial Loop (3 iterations)
    ↓
Hardened Model → Streamlit Demo
```

---

## Results

| Model | Recall | ROC-AUC |
|---|---|---|
| TF-IDF + Random Forest (baseline) | 0.88 | 0.993 |
| DistilBERT (fine-tuned) | 0.98 | 0.998 |

**Adversarial Loop Results:**

| Iteration | Attacks Evaded | Attack Success Rate | Recall |
|---|---|---|---|
| 1 | 101 | 33.67% | 0.938 |
| 2 | 15 | 5.00% | 0.946 |
| 3 | 7 | 2.33% | 0.946 |

**Per-Category Robustness:**

| Category | Recall |
|---|---|
| Role Override | 100% |
| Instruction Smuggling | 100% |
| Indirect Injection | 100% |
| Jailbreak | 94.44% |
| Unknown | 90.48% |
| Data Extraction | 85.71% |

---

## Dataset

1,639 labeled samples across 6 categories:
- **Role Override** — forcing the model to assume a different identity
- **Instruction Smuggling** — hiding malicious instructions in innocent content
- **Data Extraction** — attempting to leak system prompts or context
- **Jailbreak** — using fiction/hypotheticals to bypass safety guidelines
- **Indirect Injection** — attacks embedded in documents the LLM reads
- **Clean** — benign user inputs

Sources: `deepset/prompt-injections` (HuggingFace) + LLM-generated examples (Groq LLaMA 3.3 70B)

---

## Evasion Strategies (Red-Team)

| Strategy | Description |
|---|---|
| Paraphrase | Same malicious intent, completely different wording |
| Obfuscation | Leetspeak, character substitutions, unusual spacing |
| Roleplay | Wrapping attacks inside stories or hypotheticals |
| Encoding | Hiding instructions inside decode requests |
| Nested | Embedding attacks inside innocent-looking requests |

---

## Project Structure

```

SHIELD/
├── NOTEBOOKS/
│   ├── 01_fetch_data.ipynb       # Pull public dataset
│   ├── 02_generate_llm.ipynb     # LLM-based data generation
│   ├── 05_finetune.ipynb         # DistilBERT training (run on Colab T4 GPU)
│   ├── main.ipynb                # Baseline + adversarial loop
│   └── full_dataset.csv          # Final labeled dataset (1,639 samples)
├── app.py                        # Streamlit demo (SHIELD)
├── models/                       # Saved baseline models
├── requirements.txt
└── README.md
```

---

## Run Locally

```bash
git clone https://github.com/AdyashaNayak16/Prompt_Injection_Detector_final
cd Prompt_Injection_Detector_final
pip install -r requirements.txt
```

Create a `.env` file:
```
GROQ_API_KEY=your_groq_key_here
```

```bash
streamlit run app.py
```

---

## Tech Stack

| Component | Technology |
|---|---|
| Detector Model | DistilBERT (fine-tuned) |
| Baseline | TF-IDF + Random Forest / SVM / LR |
| Red-Team LLM | LLaMA 3.3 70B via Groq API |
| Training | Google Colab T4 GPU |
| App | Streamlit |
| Model Hosting | HuggingFace Hub |

---

## Built By

**Adyasha Nayak** — 2nd year B.Tech, NIT Rourkela