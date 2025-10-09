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
7. [VS Code Version Check](#8-vs-code-version-check-prompt)
8. [📖 Documentation Resources](#documentation-resources)
9. [✅ Prompting Guidelines Summary](#-prompting-guidelines-summary)

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

## 8. VS Code version check prompt
```markdown
Role: VS Code development assistant
Task: Help me check my development environment versions
Context: Working on a Node.js/TypeScript project in VS Code
Constraints: Provide commands I can run in the integrated terminal
Format: Step-by-step instructions with terminal commands

What are the current versions of:
- VS Code
- Node.js 
- npm/yarn
- TypeScript
- Any active extensions

Please provide terminal commands to check each.
```
## Documentation Resources

| Category | Resource | URL | Description |
|----------|----------|-----|-------------|
| **VS Code Core** | Command API | [VS Code Command API](https://code.visualstudio.com/api/references/commands) | Built-in command reference for VS Code |
| **VS Code Core** | Integrated Terminal | [Terminal Documentation](https://code.visualstudio.com/docs/terminal/basics) | How to use VS Code's integrated terminal |
| **VS Code Core** | Settings Reference | [VS Code Settings](https://code.visualstudio.com/docs/getstarted/settings) | Complete settings documentation |
| **VS Code Core** | Extension API | [VS Code Extension API](https://code.visualstudio.com/api) | For extension development |
| **Languages** | TypeScript in VS Code | [TypeScript Integration](https://code.visualstudio.com/docs/languages/typescript) | TypeScript-specific features and setup |
| **Languages** | Node.js Documentation | [Node.js Docs](https://nodejs.org/en/docs/) | Official Node.js documentation |
| **Languages** | npm Documentation | [npm Docs](https://docs.npmjs.com/) | Package manager documentation |
| **Development** | Git in VS Code | [Source Control](https://code.visualstudio.com/docs/sourcecontrol/overview) | Git integration and version control |
| **Development** | Debugging Guide | [VS Code Debugging](https://code.visualstudio.com/docs/editor/debugging) | Debugging setup and configuration |
| **AI Tools** | GitHub Copilot | [Copilot Documentation](https://docs.github.com/en/copilot) | AI pair programming tool setup |
| **AI Tools** | VS Code Chat | [AI Chat Features](https://code.visualstudio.com/docs/copilot/overview) | Built-in AI chat capabilities |
| **Architecture** | Mermaid Diagrams | [Mermaid Documentation](https://mermaid.js.org/) | For system architecture diagrams |
| **Data Analysis** | Pandas Documentation | [Pandas Docs](https://pandas.pydata.org/docs/) | Python data analysis library |
| **API Development** | REST API Guidelines | [Microsoft REST API Guidelines](https://github.com/microsoft/api-guidelines) | Best practices for REST APIs |
| **API Development** | OpenAPI Specification | [OpenAPI Docs](https://swagger.io/specification/) | API documentation standard |
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

