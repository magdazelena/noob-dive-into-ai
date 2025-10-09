# 🧩 Engineering Prompt Library

A curated library of reusable prompt templates for software engineers.  
Each follows a consistent structure to maximize accuracy, reproducibility, and clarity.
## 📚 Table of Contents

1. [⚙️ Code Generation](#️-1-code-generation)
2. [🧹 Code Review and Refactoring](#-2-code-review-and-refactoring)
3. [🧮 Data Analysis](#-4-data-analysis)
4. [🧱 System Architecture Design](#-5-system-architecture-design)
5. [🐞 Debugging Assistance](#-6-debugging-assistance)
6. [🧰 API Specification](#-7-api-specification)
7. [✅ Prompting Guidelines Summary](#-prompting-guidelines-summary)

---

## ⚙️ 1. Code Generation

```markdown
## Context
You are a senior [language] developer.

## Task
Write a function/module/class that does the following:
- [Describe the functionality]

## Inputs

{
    input_parameters: [],
    expected_behavior: ...,
    edge_cases: []
}

## Constraints

Follow [language/framework] conventions

Include type hints and comments

Avoid extra dependencies

## Output Format

Provide only the code in a [language] block.

```

---

## 🧹 2. Code Review and Refactoring

```markdown
## Context
You are a senior software engineer reviewing/refactoring code.

## Task
Identify issues, suggest improvements, and refactor while preserving behavior.

## Inputs
> Paste code here

## Constraints

 - Use concise technical language
 - Include examples
 - Explain parameters and return values

## Output Format

Return Markdown documentation with sections:
 - Overview
 - Parameters
 - Returns
 - Example
```

---

## 🧮 4. Data Analysis

```markdown
## Context
You are a data analyst using pandas.

## Task
Analyze the given dataset for summary statistics and patterns.

## Inputs
Dataset path: `data/[filename].csv`

## Constraints
- Use pandas and matplotlib
- Include commented code
- Highlight key insights

## Output Format
1. Python code block
2. Textual summary of insights
```
---
## 🧱 5. System Architecture Design
```markdown
## Context
You are a software architect designing a scalable system.

## Task
Design an architecture for [describe the system].

## Constraints
- Must be modular and maintainable
- Include data flow and key components

## Output Format
1. Description (text)
2. Mermaid diagram
3. Tech stack summary (table)

```
---

## 🐞 6. Debugging Assistance
```markdown
## Context
You are a debugging assistant.

## Task
Identify the root cause of the error and fix it.

## Inputs
Error:
> Paste traceback
Code: 
> paste code

## Constraints

 - Keep fix minimal
 - Explain reasoning

## Output Format

 - Diagnosis summary
 - Fixed code
 - Explanation
```

---

## 🧰 7. API Specification

```markdown
## Context
You are a backend engineer.

## Task
Define a REST or GraphQL API that supports:
- [List endpoints]

## Constraints
- Follow standard conventions
- Include request/response schemas

## Output Format
Markdown sections:
- Overview
- Endpoints
- Example request/response (JSON)

```
---

## ✅ Prompting Guidelines Summary

| Principle | Why It Matters |
|------------|----------------|
| Use Markdown headings | Separates logic layers |
| One task per prompt | Avoids confusion |
| Define constraints early | Prevents assumption errors |
| Specify output format | Keeps output clean |
| Include examples | Reduces ambiguity |
| Ask for clarifications | Improves alignment |
| Iterate and refine | Ensures reliability |

---

