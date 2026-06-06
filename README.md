# AISHE College Explorer — Natural Language Query Interface

A natural language analytics tool for the **All India Survey on Higher Education (AISHE)** dataset (~52,000 colleges). Ask plain-English questions about Indian higher education institutions and get back structured answers, auto-generated charts, and full data provenance — all through a Gradio web UI.

---

## How It Works

The pipeline has four stages:

```
User Question
     │
     ▼
[1] Intent Parsing      — LLM (Llama 3.3 70B via Groq) reads the question and
                          returns a structured JSON operation object
     │
     ▼
[2] Execution           — The JSON op is dispatched to a pandas executor
                          (count / top_n / groupby / compare / filter / trend)
     │
     ▼
[3] Answer Synthesis    — A second LLM call produces a concise natural-language
                          answer grounded in the actual query result
     │
     ▼
[4] Chart Rendering     — Matplotlib generates a context-appropriate chart
                          (horizontal bar, pie, or line trend) from the result
```

---

## Project Structure

```
.
├── work.ipynb                          # Main notebook (all pipeline code + Gradio UI)
├── All Colleges AISHE Dashboard.csv    # Primary dataset  (~52,292 rows, 12 cols)
├── All Colleges AISHE Dashboard.xlsx   # Same dataset in Excel format
├── eval_ques                           # Evaluation question bank (plain text)
├── .env                                # API keys (not committed)
├── .gitignore
├── .gradio/                            # Gradio cache / temp files
└── README.md
```

---

## Dataset Schema

| Column | Type | Description |
|---|---|---|
| `aishe_code` | string | Unique college identifier (e.g. `C-60180`) |
| `name` | string | Institution name |
| `state` | string | Indian state / UT |
| `district` | string | District name |
| `website` | string | Institution website |
| `year_of_establishment` | float | Founding year (1800–2024) |
| `location` | string | `Urban` or `Rural` |
| `college_type` | string | e.g. Affiliated, Autonomous, Constituent |
| `management` | string | e.g. Central Government, Private-Unaided |
| `university_aishe_code` | string | Parent university code |
| `university_name` | string | Parent university name |
| `university_type` | string | e.g. Central University, State Public University |

---

## Supported Query Operations

| Operation | Example Question |
|---|---|
| `count` | "How many colleges are there in Bihar?" |
| `top_n` | "Which 5 states have the most colleges?" |
| `groupby` | "How are colleges distributed by management type?" |
| `compare` | "Compare college counts by university type" |
| `filter` | "Show private unaided colleges in Maharashtra" |
| `trend` | "How has college establishment grown decade by decade?" |
| `out_of_scope` | "What is the average fee?" *(not in dataset)* |

Questions about fees, NAAC/NIRF rankings, student/faculty counts, and course offerings are gracefully flagged as out-of-scope.

---

## Setup

### 1. Clone & install dependencies

```bash
pip install openai pandas gradio matplotlib pillow python-dotenv
```

### 2. Configure API key

Create a `.env` file in the project root:

```
GROQ_API_KEY=your_groq_api_key_here
```

Get a free key at [console.groq.com](https://console.groq.com).

### 3. Place the dataset

Ensure `All Colleges AISHE Dashboard.csv` is in the same directory as the notebook.

### 4. Run the notebook

Open `work.ipynb` in Jupyter and run all cells. The Gradio UI will launch at `http://127.0.0.1:7861` and optionally generate a public share link.

---

## Dependencies

| Package | Purpose |
|---|---|
| `openai` | Groq-compatible OpenAI client |
| `pandas` | Data loading, cleaning, query execution |
| `gradio` | Web UI |
| `matplotlib` | Chart rendering |
| `pillow` | Matplotlib → PIL image conversion for Gradio |
| `python-dotenv` | `.env` key loading |

**Model:** `llama-3.3-70b-versatile` via [Groq](https://groq.com) (OpenAI-compatible endpoint)  
**Python:** 3.10+

---

## Example Queries

- *"Which 5 states have the most colleges?"*
- *"How are colleges distributed by management type?"*
- *"How has college establishment grown decade by decade?"*
- *"How many private unaided colleges are in Maharashtra?"*
- *"Which district in Tamil Nadu has the most affiliated colleges?"*
