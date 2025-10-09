# 🔄 Prompt Adaptation Across AI Models

## Overview
This document explains how to adapt structured prompts for different LLMs:
- **ChatGPT / GPT-4 / GPT-5 (OpenAI)**
- **Claude (Anthropic)**
- **Copilot (GitHub/OpenAI)**

All models interpret intent similarly, but they vary in:
- Input style (chat vs. inline)
- Context handling
- Sensitivity to formatting (Markdown, JSON, comments)

---

## 🧠 1. General Guidance

| Model | Ideal Input Style | Best For | Context Length | Markdown Support | JSON Fidelity |
|--------|------------------|-----------|----------------|------------------|----------------|
| **ChatGPT / GPT-5** | Full Markdown + JSON hybrid | Structured reasoning, coding, analysis | ~128k tokens | ✅ Excellent | ✅ Excellent |
| **Claude** | Concise Markdown with clearly scoped tasks | Explanations, documentation, summarization | 200k+ tokens | ✅ Excellent | ✅ Good |
| **Copilot** | Inline comments and short instructions | Code completion, refactoring | ~2–4k tokens | ⚠️ Limited | ⚠️ Limited |

---

## 🧩 2. Example: Code Generation Prompt

### 🟦 ChatGPT / GPT-5
```markdown
## Context
You are a Python engineer.

## Task
Write a function that:
- Loads a CSV
- Filters by a date range
- Returns summary statistics for numeric columns

## Constraints
- Use pandas only
- Assume column "timestamp" exists
- Return a pandas DataFrame

## Output Format
Provide only code inside a ```python``` block.

```
## 🟧 Claude

```markdown
(You are a Python engineer.)

Please write a concise, readable function that:
- Loads a CSV and filters rows by date range
- Calculates summary stats for numeric columns

Constraints:
- Use pandas only
- Column name: "timestamp"
- Return a DataFrame, no printed output

Output: Python code only.
```
## 🟩 Copilot

```python
# Task: Load a CSV file and filter it by a date range.
# Requirements:
# - Use pandas only
# - Assume a 'timestamp' column
# - Return a DataFrame with summary statistics for numeric columns.

def summarize_csv(filepath, start_date, end_date)
    # your code here


```
## 3. Adaptation Rules Summary
| Principle                | ChatGPT                  | Claude                       | Copilot                        |
| ------------------------ | ------------------------ | ---------------------------- | ------------------------------ |
| **Structure**            | Multi-layer Markdown     | Compact Markdown             | Inline comments                |
| **Tone**                 | Instructional            | Conversational               | Directive                      |
| **Output Formatting**    | Use ``` blocks           | Use concise formatting       | Use code comments              |
| **Constraints Handling** | Very reliable            | Reliable with short phrasing | Limited, inferred from context |
| **Context Length**       | Large (multi-section OK) | Very large (long prompts OK) | Small (short context)          |
| **Best For**             | Deep reasoning & coding  | Documentation & design       | Inline coding help             |

## ⚙️ 4. TL;DR — Adaptation Summary

| Layer | Universal | ChatGPT/GPT | Claude | Copilot |
|--------|------------|--------------|----------|----------|
| **Context/Role** | ✅ | ✅ | ✅ | ⚠️ (comment only) |
| **Markdown Headings** | ✅ | ✅ | ✅ | ⚠️ |
| **JSON Blocks** | ✅ | ✅ | ✅ | ⚠️ |
| **Long Prompts** | ⚠️ | ✅ | ✅✅ | ❌ |
| **Inline Tasks** | ⚠️ | ⚠️ | ⚠️ | ✅✅ |

---

## 🧭 Final Note
> The *logic* of a prompt is universal — the *syntax* must adapt to the model’s input channel.

- Chat models → favor **Markdown & structure**
- Code assistants → favor **concise comments**
- Documentation generators → favor **clarity & brevity**
