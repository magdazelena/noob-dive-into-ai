# 🧭 Prompting Rules and Principles for Engineers

## Overview
This guide summarizes the best practices for prompting large language models (LLMs) effectively in technical and engineering contexts.  
It focuses on how to achieve *scoped, accurate, and reproducible* results through structured prompt design.

---

## 🧩 1. Core Philosophy
Think of a prompt as a **specification**, not a casual question.  
The LLM is an intelligent system that interprets instructions probabilistically, so your clarity and structure directly determine accuracy.

A good prompt defines:
- **Goal** – What outcome do you want?
- **Inputs** – What information or data is provided?
- **Constraints** – What rules must be followed?
- **Output Format** – How should the result look?
- **Success Criteria** – How will you know it’s correct?

---

## 🧱 2. Prompt Structure Layers

| Layer | Purpose | Example |
|-------|----------|---------|
| **1. Context** | Define the role or perspective | “You are a senior Python engineer.” |
| **2. Task** | State the objective clearly | “Refactor the following function for readability.” |
| **3. Constraints** | Control scope or style | “Do not change the public interface.” |
| **4. Input** | Supply relevant data or code | “Here is the function: …” |
| **5. Output Format** | Define structure and deliverable | “Return code only, inside a Python block.” |

---

## 🧮 3. Syntax vs. Semantics

> [!NOTE]
> Sensitivity to formatting depends on the LLM! [Read more here](./prompt_adaptation_across_models.md)

| Format | Strengths | Weaknesses | Best Use |
|---------|------------|-------------|-----------|
| **Natural Language** | Best for reasoning, nuanced intent, brainstorming | Ambiguous, may assume details | Conceptual or open tasks |
| **Markdown** | Clear segmentation of context, task, and code | Slightly more verbose | Coding, explanations, reports |
| **JSON** | Machine-parsable, unambiguous | Poor for narrative or reasoning | Automation, API specs, structured pipelines |

### 🧠 Key Insight
> Use **natural language for intent**, and **structure (Markdown or JSON)** to constrain output.

---

## 💡 4. Hybrid Prompt Design (Recommended)

```markdown
## Context
You are an expert in [domain]. Provide technically correct and precise answers.

## Resources
Use ONLY the following:
[Insert text of official documentation, version notes, or snippets]

## Task
Answer the following question:
[Your question]

## Constraints
- Assume version [X.Y.Z] if applicable
- Cite or reference any sources used
- Indicate confidence in each answer (high/medium/low)
- If information is missing in the resources, respond: "Not available in the provided documentation."

## Output Format
- Step-by-step reasoning
- Cited sources
- Final answer highlighted
```

This hybrid approach gives both semantic clarity and syntactic precision.

---

## ⚙️ 5. Scoping Techniques

| Problem | Fix / Example |
|----------|----------------|
| Too vague | “Be explicit and use technical terminology.” |
| Too verbose | “Limit to 2 paragraphs.” |
| Output drift | “If unsure, ask clarifying questions instead of assuming.” |
| Large task | “Step 1: summarize inputs. Step 2: propose approach.” |
| Need consistency | “Always output in JSON with these keys…” |

---

## 🔁 6. Iterative Prompting

Treat each interaction like a control loop:

1. **Prompt**  
2. **Observe Output**  
3. **Adjust for Ambiguity**  
4. **Re-prompt**

You can even ask:
> “Summarize your understanding of my request before proceeding.”

This confirms mutual understanding.

---

## 🧩 7. Output Control and Formatting

### To improve precision:
- Explicitly specify output boundaries:  
  “Return only code inside a ```python``` block.”
- Use schemas or examples for structured data.
- Include positive and negative constraints:
  - ✅ “Use pandas.”
  - ❌ “Do not use external APIs.”
- Include small examples of correct output to set expectations.


---

## 8. Fact checking

Got it — you want to understand **how to maximize factual accuracy and reliability when using LLMs**, especially for technical topics where outdated or incorrect answers are common. Let’s go step by step, with practical prompting strategies.

---

### 1️⃣ Why LLMs Can Be Wrong

LLMs generate text based on **patterns and probabilities in their training data**, not live verification. This means:

* They can confidently output **outdated information** (old docs, APIs, libraries).
* They may produce **plausible-sounding but incorrect answers** (hallucinations).
* Even if you provide resources, the model may **default to training knowledge** unless prompted specifically to prioritize the resource.

---

### 2️⃣ How to Prompt for the Most Correct Answers

**Key principle:** Treat the LLM as a reasoning assistant that can **verify against sources if instructed**, not just a free-flow answer generator.

**Prompting patterns:**

#### ✅ Ask explicitly for factual verification

```markdown
You are a senior software engineer. Answer using **only authoritative sources** and clearly indicate if something is uncertain. Do not guess.
```

#### ✅ Request step-by-step reasoning

```markdown
Explain your reasoning step by step and show how you reach the answer. Indicate any assumptions.
```

#### ✅ Emphasize sources

```markdown
Cite sources, links, or documentation where possible.
```

#### ✅ Ask for confidence levels

```markdown
Rate the confidence of your answer from 0% to 100% and explain why.
```

---

### 3️⃣ How to Prompt for Specific Resources

If you have a reference (documentation, snippet, URL, PDF):

```markdown
Use ONLY the following documentation to answer:
[Insert documentation text or link]

Answer the question strictly based on this resource. If the information is missing, say "not available in the provided resources."
```

**Notes:**

* LLMs **cannot fetch URLs live**, unless connected to a retrieval plugin (like ChatGPT with Browsing or a Retrieval-Augmented Generation system).
* Providing the content of documents in the prompt is the **most reliable way** to ensure accuracy.

---

### 4️⃣ How to Prompt with Version-Specific Context

For APIs, libraries, or specs:

```markdown
Assume the code/library version is X.Y.Z. Provide examples and references **only for this version**. Do not include behavior from other versions.
```

**Tip:** Include tables of breaking changes or relevant version-specific notes in your input.

---

### 5️⃣ Determining Accuracy

You can prompt the model to **self-assess**, but remember it cannot fact-check external content unless it’s given:

```markdown
Check your answer against the provided documentation. Highlight any parts that are uncertain or potentially outdated.
```

**Strategies for verifying:**

* Cross-reference multiple sources manually or via multi-step prompts.
* Ask the model to summarize differences between versions or docs.

---

### 6️⃣ Can LLMs Cross-Check on the Fly?

* **Without external plugins or retrieval systems:**
  The model relies on **its training knowledge**, not live data. It cannot verify an external website unless you provide its text in the prompt.
* **With retrieval/plug-in enabled systems:**
  Yes, it can query sources (e.g., ChatGPT with Browsing or Retrieval-Augmented Generation).

**Key point:** LLMs **always revert to training data if not explicitly instructed with external resources**.

---

### 7️⃣ Settings vs. Prompt

| Strategy                    | Can it be done in settings? | Should it be per prompt?     |
| --------------------------- | --------------------------- | ---------------------------- |
| Use specific docs/resources | ❌ Not in settings           | ✅ Yes, include in prompt     |
| Version-specific context    | ❌ Not in settings           | ✅ Yes, include in prompt     |
| Cite sources / confidence   | ❌ Not in settings           | ✅ Yes, per prompt            |
| Live verification           | ✅ If browsing/RAG enabled   | ✅ Per prompt, specify source |

**Conclusion:** Most accuracy-focused strategies are **per-prompt instructions**, not global settings, unless the platform supports **retrieval-augmented modes**.

---

### ✅ Key Takeaways

1. LLMs do **not inherently fact-check**; accuracy is a function of how you instruct them.
2. **Always provide the latest or authoritative resources** in the prompt for high confidence answers.
3. **Version-specific instructions** prevent hallucination from older knowledge.
4. **Confidence ratings and step-by-step reasoning** help assess reliability.
5. **Cross-checking on the fly** requires either plugins or explicitly supplied documents.
6. Most of this is **per-prompt**, not global settings.

---

If you want, I can create a **ready-to-use Markdown template** specifically for “accuracy-focused prompting” that you could drop into any chat with ChatGPT, Claude, or other LLMs. It would integrate version control, documentation references, confidence, and cross-checking instructions.

Do you want me to do that next?

  
---

## 🧭  TL;DR Summary

> Use **natural language for meaning**,  
> **Markdown for structure**,  
> **JSON for precision**.  
>
> Always define: *role → task → constraints → format → example.*

---


