# Features Documentation

Hypasia AI is divided into two primary workspaces: The **Data Miner** and the **Fine-Tune Studio**.

## 1. The Data Miner

The Data Miner transforms raw, unstructured data into highly curated, scored `JSONL` datasets tailored for instruction fine-tuning.

### A. Web Crawling & Sitemap Parsing
Provide a URL to any documentation site, blog, or Wikipedia page. The miner will recursively follow links up to a depth of N, extracting clean text while stripping out navigation bars, footers, and ads. If a Sitemap XML is provided, it mines the entire map.

### B. Bulk URL Mining
If you have a list of thousands of URLs, you can drag and drop a `.txt` or `.csv` file into the document upload zone. The miner automatically switches to Bulk Mode, crawling and scoring every URL in the list sequentially.

### C. File Parsing
You can drag and drop various file types into the Drop Zone. Supported formats include:
- `.pdf` (Parsed using PDFPlumber)
- `.docx` (Microsoft Word documents)
- `.csv`, `.tsv`, `.xlsx`, `.parquet` (Tabular Data)
- `.txt`, `.md`, `.html`, `.jsonl` (Text Data)

### D. Dual AI Judging
The engine scores every extracted row to filter out garbage data. 
- **Ollama (Local):** If you provide no API keys, the system defaults to using Ollama on your local machine to score data. This is 100% free and private.
- **Gemini (Cloud):** If you enter a Gemini API Key in the UI, the system switches to the Gemini Pro API for lightning-fast, highly intelligent data scoring.

Rows that score below the threshold (default `7.0`) are immediately discarded. Remaining rows are marked as either **Gold** or **Silver**.

---

## 2. Fine-Tune Studio

The Fine-Tune Studio generates executable Python scripts to train Large Language Models on the datasets you exported from the Data Miner.

### A. Universal Dataset Naming
When you export a dataset from the Data Miner, you can name it (e.g., `medical_records.jsonl`). Simply paste this name into the "Dataset Filename" input in the Studio, and the generated code will automatically map to it.

### B. Deployment Targets
Generate code tailored for your specific hardware environment:
- **Local (Unsloth):** Generates heavily optimized code to fine-tune Llama 3 or Qwen on consumer GPUs (e.g., RTX 5080) natively using 4-bit quantization and xformers.
- **AWS SageMaker:** Generates a deployment script using the SageMaker HuggingFace Estimator API.
- **GCP Vertex AI:** Generates an `aiplatform.CustomContainerTrainingJob` script for Google Cloud instances.

### C. Advanced Hyperparameters
Tweak the core training parameters using UI sliders. The code generator instantly updates the Python script with your values:
- **Epochs:** (1 - 10)
- **Batch Size:** (1 - 32)
- **LoRA Rank:** (8 - 128)
- **Learning Rate:** (String input, e.g., `2e-4` or `5e-5`)

### D. AI Debugging Assistant
When training locally, CUDA OutOfMemory errors and PyTorch tensor shape mismatches are common. Paste any traceback into the AI Debugging Assistant, and the Gemini API will automatically analyze the traceback and provide a step-by-step fix.
