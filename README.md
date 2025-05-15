# Arabic Text-to-SQL with DuckDB-NSQL-7B

## 🧠 Introduction
The Arabic text-to-SQL problem involves developing a system that interprets Arabic natural-language queries and generates accurate SQL statements. This task is complicated by Arabic morphology, orthographic variations, and the lack of specialized datasets. In this project, we use the **Spider dataset** and its associated schemas to train and evaluate Arabic text-to-SQL models.

After testing multiple sequence-to-sequence models, our best-performing model was [`motherduckdb/DuckDB-NSQL-7B-v0.1`](https://huggingface.co/motherduckdb/DuckDB-NSQL-7B-v0.1), fine-tuned on Arabic-translated Spider data. It achieved:

- **Final Execution Accuracy**: `69.65%`  
- **Final BLEU Score**: `0.4698`

---

## 📚 Literature Review

### Ar-Spider: Text-to-SQL in Arabic
- **Authors**: Saleh Almohaimeed, Saad Almohaimeed, Mansour Al Ghanim, Liqiang Wang  
- **Published**: February 22, 2024  
- **Summary**: Introduced Ar-Spider, the first Arabic cross-domain text-to-SQL dataset. Addressed schema-language and SQL-structure issues. LGESQL achieved 65.57%, S2SQL reached 62.48%.  
- [Read the paper](https://arxiv.org/abs/2402.12879)

### AraSpider: Democratizing Arabic-to-SQL  
- **Authors**: Ahmed Heakl, Youssef Mohamed, Ahmed B. Zaky  
- **Published**: February 12, 2024  
- **Summary**: Released AraSpider, evaluated multiple translation strategies and fine-tuned multilingual models. Back-translation and contextual schema awareness significantly improved performance.  
- [Read the paper](https://arxiv.org/abs/2402.10187)

---

## 📦 Dataset

**Spider Dataset**  
Originally designed for English text-to-SQL tasks, we translated it into Arabic:

- 10,000+ total questions  
- 6,000+ questions translated into Arabic  
- 200 different databases across 138 domains  
- Complex queries involving joins, aggregations, nested SQL  

---

## 🧰 Model: DuckDB-NSQL-7B

### 1. Overview
- 7B parameters, 32 transformer layers  
- Based on **Meta's Llama-2 7B**  
- Pretrained on general SQL + fine-tuned for **DuckDB SQL syntax**  
- Generates full SQL statements, not limited to `SELECT`

### 2. Training Data
- 200K DuckDB text-to-SQL pairs  
- Generated using `Mixtral-8x7B-Instruct` and `DuckDB v0.9.2` docs  
- Includes `NSText2SQL` pairs transpiled to DuckDB SQL via `sqlglot`

### 3. Evaluation
- Evaluated on a DuckDB-specific benchmark (75 examples)  
- Dataset publicly available

### 4. Training Procedure
- **Cross-entropy loss** (focused on SQL portion)  
- Trained on **80GB A100 GPUs**  
- Utilized data/model parallelism  
- Fine-tuned for **500 epochs**

### 5. Intended Use & Limitations
- Designed for **natural language to SQL**  
- Performs best with **table schema + structured prompts**  
- Supports full DuckDB SQL, including extension features  
- Prompt formatting impacts accuracy

---

## 🧪 Fine-Tuned Model

- **Base**: [`motherduckdb/DuckDB-NSQL-7B-v0.1`](https://huggingface.co/motherduckdb/DuckDB-NSQL-7B-v0.1)  
- **Fine-tuning dataset**: Arabic-translated Spider subset  
- **Final Metrics**:
  - Execution Accuracy: `69.65%`
  - BLEU Score: `0.4698`

---

## ⚙️ Hyperparameters

| Parameter            | Value       |
|----------------------|-------------|
| Epochs               | 500         |
| Learning Rate        | *TBD*       |
| Batch Size           | *TBD*       |
| Optimizer            | *TBD*       |
| Max Seq Length       | *TBD*       |
| Gradient Accumulation| *TBD*       |

(*Fill in with actual values used.*)

---

## 📝 Prompt Format

Example Prompt:
```plaintext
-- Table schema:
CREATE TABLE students (id INT, name TEXT, major TEXT, gpa FLOAT);

-- Question:
ما أسماء الطلاب الذين يدرسون علوم الحاسوب؟

-- SQL:
SELECT name FROM students WHERE major = 'علوم الحاسوب';


├── data/               # Translated Arabic Spider dataset
├── model/              # Fine-tuning scripts and checkpoints
├── evaluation/         # Evaluation scripts
├── prompts/            # Prompt format examples
├── results/            # Output logs and metrics
└── README.md
