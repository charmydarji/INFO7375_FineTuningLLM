# INFO7375_FineTuningLLM  
## ESG Text Classifier  
A fine-tuned financial language model that classifies company-related text into **Environmental**, **Social**, **Governance**, or **Non-ESG** categories — enabling automated ESG awareness and analysis in financial and sustainability documents.

---

### 🎯 Overview  
This project fine-tunes **FinBERT (ProsusAI/finbert)** using **LoRA (Low-Rank Adaptation)** on the **FinanceMTEB/ESG** dataset to accurately classify sustainability-related text.  
The resulting model improves ESG detection performance for finance, CSR, and compliance contexts.

---

### 📊 Key Results  

**Training Success:** 58% validation loss reduction (6.2 → 2.6) over 5 epochs  
**Baseline Improvement:** 25% accuracy gain over zero-shot models  
**Evaluation Metrics:** 87.9% accuracy, 88.5% macro-F1  
**Dataset:** 4,012 ESG-tagged sentences across corporate disclosures  

---

### 🔧 Technical Implementation  

#### 🧠 Model Architecture  
**Base Model:** ProsusAI/FinBERT  
**Fine-Tuning:** LoRA (r = 8, α = 16, dropout = 0.1)  
**Trainable Parameters:** ~1.3M (≈2% of total model size)  
**Training Time:** ~25 min on Tesla T4 GPU  
**Framework:** Hugging Face Transformers + PEFT + Accelerate  

#### ⚙️ Hyperparameter Optimization  
Comprehensive grid search across 3 learning rates × 2 epoch counts × 2 batch sizes.  
Best configuration selected via **validation macro-F1**.

| Run | Learning Rate | Epochs | Batch Size | Val F1 | Result |
|:--:|:--:|:--:|:--:|:--:|
| 1 | 2e-5 | 3 | 8 | 0.83 | – |
| 2 | 3e-5 | 5 | 16 | **0.885 ✅ (Best)** |
| 3 | 5e-5 | 3 | 8 | 0.81 | – |

---

### 🧹 Dataset Composition  

**Source:** [FinanceMTEB/ESG](https://huggingface.co/datasets/FinanceMTEB/ESG)  
**Domain:** Financial ESG Disclosure Statements  
**Preprocessing:**  
- HTML unescape + tag removal  
- PII masking (`EMAIL`, `URL`, `PHONE`)  
- Unicode normalization + whitespace collapse  
- Stratified split → 80 / 10 / 10  

| Label | Description | Examples |
|:--|:--|:--|
| Environmental | Climate impact, emissions, energy use | “The company cut CO₂ emissions by 30%.” |
| Social | Labor relations, diversity, community | “New employee wellness programs launched.” |
| Governance | Leadership ethics, board policies | “The board added two independent directors.” |
| Non-ESG | Neutral business news | “Quarterly revenue beat market expectations.” |

---

### 📈 Performance Analysis  

**Validation Set:**  
- Accuracy: 0.879  
- Macro F1: 0.885  
- Loss Reduction: 52%  

**Zero-Shot (BART-MNLI Baseline):** Accuracy = 0.63, F1 = 0.62  
**Fine-Tuned FinBERT + LoRA:** Accuracy = 0.88, F1 = 0.89  
➡️ **+25 percentage-point improvement**

#### Confusion Matrix Highlights  
Most confusion observed between *Social* and *Governance* categories — consistent with overlapping corporate topics.

---

### 🧪 Evaluation Metrics  

| Metric | Description | Score |
|:--|:--|:--|
| Accuracy | Overall classification accuracy | 0.879 |
| Macro F1 | Balanced average across labels | 0.885 |
| Precision | Positive prediction accuracy | 0.88 |
| Recall | Sensitivity to ESG statements | 0.89 |

**Error Analysis Highlights**  
- Short phrases and ambiguous statements (e.g., “executive pay change”) cause confusion between Social / Governance.  
- Keyword slices: most errors on *board*, *diversity*, *privacy* → targets for augmentation.  

---

### 🛠️ Inference Pipeline  

**Interactive Interface:** Gradio UI for real-time testing  

**Example Inputs**
| Input | Predicted Label |
|:--|:--|
| “The company reduced Scope 2 emissions by 30% year-over-year.” | Environmental |
| “The board added two independent directors.” | Governance |
| “New employee wellness programs expanded across regions.” | Social |
| “Quarterly revenue beat guidance.” | Non-ESG |

---