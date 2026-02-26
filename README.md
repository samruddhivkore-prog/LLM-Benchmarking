# LLM Benchmarking: Gemini vs Cohere on Mathematical Reasoning

A structured evaluation of two large language models — Google Gemini and Cohere Command — on 387 mathematical problems across 12 domains and 3 difficulty levels, analysing accuracy, domain-specific strengths, and failure modes.

---

## Overview

As LLMs are increasingly deployed in high-stakes environments, rigorous evaluation of their capabilities and limitations is essential. This project benchmarks two production-grade LLMs against a structured mathematical dataset, using strict prompt engineering to enforce consistent, comparable outputs.

The goal was not just to measure accuracy — but to understand *where* each model fails, *why* it fails, and what that means for responsible AI deployment.

---

## Dataset

- **Source:** Odyssey Math Dataset (`final-odyssey-math-with-levels.jsonl`)
- **Total Problems:** 387
- **Domains (12):**

| Domain | Problems |
|---|---|
| Algebra | 151 |
| PreCalculus | 47 |
| Geometry | 39 |
| Combinatorics | 37 |
| Linear Algebra and Abstract Algebra | 25 |
| Calculus and Analysis | 24 |
| Probability | 21 |
| Statistics | 17 |
| Differential Equations | 14 |
| Calculus | 5 |
| Number Theory | 4 |
| Trigonometry / Series | 3 |

- **Difficulty Levels (3):**

| Level | Problems |
|---|---|
| High School Competition | 148 |
| High School Math | 138 |
| College Math | 101 |

---

## Models Evaluated

| Model | Provider | API Used |
|---|---|---|
| Gemini 2.5 Flash Lite | Google | Generative Language API |
| Command A (command-a-03-2025) | Cohere | Cohere ClientV2 |

---

## Methodology

### Prompt Engineering
Both models were given identical system prompts designed for strict output control:

```
"Give only the exact numeric answer in less than 5 words. No words. No steps."
```

This enforced:
- Numeric-only responses
- No chain-of-thought contamination
- Consistent parsing across both models

### Pipeline
1. Load 387 problems from `.jsonl` dataset
2. Call each model API with retry logic (handles rate limits, 429, 502 errors)
3. Save raw predictions to JSON
4. Convert to CSV for analysis
5. Merge with ground truth and clean answers for comparison
6. Compute accuracy by overall, domain, and difficulty level

### Answer Cleaning
Predictions were normalised before comparison — stripping whitespace, removing LaTeX formatting, handling edge cases where models returned partial answers or refused to respond.

---

## Results

### Overall Accuracy

| Model | Problems Compared | Skipped (NA) | Accuracy |
|---|---|---|---|
| **Cohere** | 387 | 0 | **28.42%** |
| **Gemini** | 234 | 153 | **32.91%** |

Note: Gemini skipped 153 problems (39.5% non-response rate), meaning its effective accuracy on attempted problems is higher — but its real-world reliability is lower due to refusals.

---

### Accuracy by Domain

| Domain | Cohere | Gemini |
|---|---|---|
| Algebra | 25.83% | 26.97% |
| Calculus | 40.00% | 66.67% |
| Calculus and Analysis | 29.17% | 23.08% |
| Combinatorics | 5.41% | 0.00% |
| Differential Equations | 64.29% | 33.33% |
| Geometry | 33.33% | 53.57% |
| Linear Algebra | 20.00% | 25.00% |
| Number Theory | 0.00% | — |
| PreCalculus | 59.57% | 72.41% |
| Probability | 4.76% | 12.50% |
| Series | 0.00% | 100.00% |
| Statistics | 23.53% | 40.00% |
| Trigonometry | 0.00% | — |

---

### Accuracy by Difficulty Level

| Level | Cohere | Gemini |
|---|---|---|
| High School Math | 52.90% | 69.88% |
| College Math | 25.74% | 24.14% |
| High School Competition | 7.43% | 5.38% |

---

## Key Findings

- **Gemini outperforms Cohere overall** (32.91% vs 28.42%) but has a significant non-response problem — 153 skipped problems vs 0 for Cohere
- **Both models struggle with competition-level problems** — under 8% accuracy, suggesting LLMs are not yet reliable for complex mathematical reasoning
- **Cohere is more consistent** — it attempted every problem, making it more reliable for production deployment where a response is always required
- **PreCalculus and Differential Equations** were strongest domains for both models
- **Combinatorics, Probability, Number Theory, and Trigonometry** were weakest — near 0% for both
- **High school math** is the sweet spot for both models; performance degrades sharply at competition and college level

---

## Tech Stack

| Category | Tools |
|---|---|
| Language | Python 3 |
| APIs | Gemini Generative Language API, Cohere ClientV2 |
| Data Processing | Pandas, JSON, CSV |
| Error Handling | Retry logic for 429, 502, rate limit errors |
| Notebook | Jupyter Notebook |

---

## Project Structure

```
LLM-Benchmarking/
│
├── Final_Code.ipynb                      # Full pipeline notebook
├── original.csv                          # Ground truth answers (387 problems)
├── cohere.csv                            # Cohere predictions
├── gemini.csv                            # Gemini predictions
├── Comparison_table.PNG                  # Visual results summary
├── GAIC_All_question_answers_Final.pdf   # Full question and answer set
└── README.md                             # This file
```

---

## How to Run

```bash
# Clone the repo
git clone https://github.com/samruddhivkore-prog/LLM-Benchmarking.git
cd LLM-Benchmarking

# Install dependencies
pip install pandas cohere requests jupyter

# Add your own API keys in Final_Code.ipynb before running
# Replace the API_KEY values in the Cohere and Gemini sections

# Launch notebook
jupyter notebook Final_Code.ipynb
```

> **Note:** You will need your own Cohere and Gemini API keys to re-run the API calls. The results CSVs are included so you can reproduce the analysis without re-calling the APIs.

---

## What I Learned

- How to design structured prompt engineering for consistent, parseable LLM outputs
- The importance of non-response rate as a reliability metric — accuracy alone does not capture real-world deployment risk
- Domain-specific LLM weaknesses: both models fail on combinatorics, probability, and competition-level reasoning
- How to build robust API pipelines with retry logic and error handling at scale
- That rigorous AI evaluation methodology is as important as model development itself

---

## Author

**Samruddhi Kore**
MSc Artificial Intelligence, Dublin City University
[LinkedIn](https://www.linkedin.com/in/samruddhi-kore25082003/) | [GitHub](https://github.com/samruddhivkore-prog)# LLM-Benchmarking
