# API Reference

The Hypasia AI Backend is built on FastAPI and serves a REST architecture. The base URL is `http://localhost:8000`.

---

## 1. Data Mining Endpoints

### `POST /api/mine/run`
Run the mining pipeline on a specific URL or HuggingFace dataset string.

**Payload (JSON)**
```json
{
  "source": "https://en.wikipedia.org/wiki/Artificial_intelligence",
  "judge": "gemini",
  "ollama_model": "llama3.1",
  "threshold": 7.0,
  "api_key": "AIzaSy..."
}
```
*Note: `api_key` is optional. If omitted, the system falls back to `.env` or local Ollama depending on the `judge`.*

**Response**
```json
{
  "status": "success",
  "rows": [
    {
      "instruction": "What is Artificial Intelligence?",
      "response": "Artificial intelligence is...",
      "score": 8.5,
      "tier": "gold"
    }
  ]
}
```

### `POST /api/mine/upload`
Upload a document (PDF, DOCX) or a `.txt` file containing bulk URLs to be mined.

**Payload (FormData)**
- `file`: The physical file binary.
- `judge`: "gemini" or "ollama" (string)
- `threshold`: Minimum score threshold (string, e.g., "7.0")
- `api_key`: Optional API key (string)

**Response**
Identical to `/api/mine/run`.

---

## 2. Fine-Tuning Endpoints

### `POST /api/finetune/generate`
Generate Python training code.

**Payload (JSON)**
```json
{
  "target": "unsloth",
  "model_name": "unsloth/llama-3-8b-Instruct-bnb-4bit",
  "dataset_path": "my_dataset.jsonl",
  "lora_rank": 32,
  "epochs": 3,
  "batch_size": 4,
  "learning_rate": "2e-4"
}
```
*Note: `target` can be `unsloth`, `aws`, or `gcp`.*

**Response**
```json
{
  "status": "success",
  "code": "from unsloth import FastLanguageModel\n..."
}
```

---

## 3. Debugging Endpoints

### `POST /api/debug/analyze`
Analyze a PyTorch/CUDA traceback error.

**Payload (JSON)**
```json
{
  "error_message": "RuntimeError: CUDA out of memory. Tried to allocate 1.20 GiB..."
}
```

**Response**
```json
{
  "status": "success",
  "message": "This is a CUDA OutOfMemory error. To fix this:\n1. Decrease your per_device_train_batch_size to 1.\n2. Increase gradient_accumulation_steps to 8."
}
```
