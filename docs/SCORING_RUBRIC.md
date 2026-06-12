# Scoring Rubric

The Hypasia AI Scoring Engine ensures that only the highest-fidelity data enters your fine-tuning pipeline. 

By default, any row scoring below `7.0` is immediately dropped.

## The 6 Evaluation Axes

Whether running via **Ollama (Local)** or **Gemini (Cloud)**, the AI Judge evaluates each extracted `[Instruction, Response]` pair across six distinct dimensions. These axes are combined using a weighted average.

### 1. Specificity (25%)
**What it measures:** Does the instruction ask for a distinct, concrete answer rather than a vague or overly broad request?
- **10/10:** "Write a Python function to parse a Trafilatura JSON output into a Pandas DataFrame."
- **3/10:** "Tell me about data."

### 2. Clarity (20%)
**What it measures:** Is the response well-formatted, grammatically correct, and easy to parse?
- **10/10:** Proper markdown formatting, bullet points, no trailing garbage characters.
- **3/10:** Broken HTML tags scattered throughout raw text.

### 3. Completeness (20%)
**What it measures:** Does the response fully answer the derived instruction without being cut off mid-sentence?
- **10/10:** Complete thought, logical conclusion.
- **3/10:** Ends abruptly due to document chunking limits.

### 4. Difficulty (15%)
**What it measures:** Would this task be challenging for a baseline language model?
- **10/10:** Complex reasoning, multi-step math, or highly specific API usage.
- **3/10:** Simple factual retrieval ("What color is the sky?").

### 5. Uniqueness (10%)
**What it measures:** Is this data novel, or is it likely highly represented in standard pre-training corpora (like Wikipedia/CommonCrawl)?
- **10/10:** Internal company documentation, newly released framework APIs.
- **3/10:** Standard Python print statements.

### 6. Domain Relevance (10%)
**What it measures:** Is the data relevant to the target domain of the fine-tuning run?
- *Note: This axis is highly dependent on the context provided in the system prompt. By default, it rewards technical depth.*

## Scoring Tier System
Once the composite score is calculated:
- **`Gold`**: `Score >= 9.0` (Perfect for highly impactful LoRA adapters).
- **`Silver`**: `7.0 <= Score < 9.0` (Good for bulk SFT).
- **`Rejected`**: `Score < 7.0` (Discarded automatically).
