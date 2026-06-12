# Architecture Flow

Hypasia AI is designed as a decoupled, modular system. It is split into a **Frontend Client** and a **Backend Engine**.

## 1. High-Level Flow
1. **User Input:** The user provides a URL, an uploaded document (PDF/DOCX), or a Bulk URL `.txt` file via the React UI.
2. **Extraction:** The Backend API receives the request and routes it to the appropriate Extractor (Trafilatura for web, PDFPlumber for PDFs, etc.).
3. **Chunking & Normalization:** The raw text is chunked into logical instruction-response pairs. Extraneous formatting is stripped.
4. **AI Scoring:** The data is piped through the Scoring Engine. The engine uses either a local `Ollama` instance or the `Gemini API` to score the data across 6 axes.
5. **UI Rendering:** The scored dataset is returned to the React frontend, displaying "Gold", "Silver", or "Rejected" badges.
6. **Code Generation:** The user defines hyperparameters in the Fine-Tune Studio. The Backend formats the appropriate template (Unsloth, AWS, or GCP) and injects the exported dataset name.

## 2. Core Components

### A. The Mining Engine (`src/hypasia/mining/`)
Responsible for reading external sources.
- **`crawler/`**: Uses `trafilatura` for high-speed HTML parsing, and `playwright` for JavaScript-heavy DOM rendering. Supports recursive BFS crawling up to depth N, and Sitemap XML parsing.
- **`parsers/`**: File dispatcher that handles specific binary and text formats (PDFs, DOCX, CSV, Text). 
- **`connectors/`**: Dedicated connectors for external APIs (e.g., HuggingFace Datasets).

### B. The Cleaning Engine (`src/hypasia/cleaner/`)
Responsible for filtering garbage data.
- **`dedup.py`**: Identifies and removes duplicate or heavily overlapping text strings.
- **`length.py`**: Discards rows that are too short (insufficient context) or too long (exceed context window limits).

### C. The Scoring Engine (`src/hypasia/scorer/`)
The brain of the system. Assigns a 1-10 score to every row.
- **`composite.py`**: The aggregator. Takes weights (e.g., 25% Specificity, 20% Clarity) and outputs a final Score and Tier.
- **`gemini_judge.py`**: Leverages Google's Gemini models for high-quality, fast evaluation via REST API.
- **`ollama_judge.py`**: Leverages local Llama/Qwen models for completely offline, free, private scoring.

### D. The Code Generator (`src/hypasia/api/routes/finetune.py`)
A template engine that injects dynamic hyperparameters (Epochs, Batch Size, Learning Rate, LoRA Rank) into boilerplate Python scripts tailored for specific cloud hardware or local unsloth engines.

### E. Frontend UI (`web/src/`)
Built with React and Vite. It eschews traditional component libraries in favor of a bespoke, vanilla CSS architecture based on the Replicate design system (Bricolage Grotesque fonts, `#ea2804` CTAs, fully rounded interactive elements).
