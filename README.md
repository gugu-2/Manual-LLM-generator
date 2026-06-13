# Hypasia AI

**The Developer-First Fine-Tuning Pipeline**

Hypasia AI is an end-to-end data mining and fine-tuning automation studio. It allows AI engineers to effortlessly mine websites, PDFs, HuggingFace repositories, and bulk URLs to construct high-quality, scored JSONL datasets. It then provides a unified Studio to generate production-ready fine-tuning code for local GPUs (Unsloth) or Cloud deployments (AWS SageMaker, GCP Vertex AI).

## Core Philosophy
1. **Premium Aesthetic:** A clean, editorial, "art-zine" developer UI built with React.
2. **Offline Capable:** Full support for local offline AI scoring using `Ollama`, falling back to `Gemini` when API keys are provided.
3. **Data Quality First:** Every mined row is evaluated on a 6-axis rubric (Specificity, Clarity, Completeness, Difficulty, Uniqueness, Domain Relevance) to ensure only the highest quality data makes it to your fine-tuning pipeline.

## System Architecture
Hypasia operates on a decoupled architecture:
- **Backend:** High-performance Python `FastAPI` server handling crawling, parsing, AI scoring, and code generation.
- **Frontend:** Blazing fast `Vite` + `React` single-page application heavily customized with a bespoke design system.

*For deep dives into the system, see the [Architecture Documentation](docs/ARCHITECTURE.md).*

## Getting Started

### Prerequisites
- Python 3.10+
- Node.js 18+
- [Ollama](https://ollama.ai/) (Optional, for free local AI scoring)

### 1. Install Backend
```bash
# Clone the repository
git clone https://github.com/yourusername/hypasia-ai.git
cd "hypasia-ai"

# Install the Python package and its dependencies
pip install -e .[api,js]

# (Optional) Install Playwright browsers for JS-heavy web crawling
playwright install chromium
```

### 2. Install Frontend
```bash
cd web
npm install
```

### 3. Running the Stack

You will need two terminal windows to run both the API and the UI.

**Terminal 1: Start the Backend (FastAPI)**
```bash
# Ensure UTF-8 output on Windows
$env:PYTHONUTF8="1" 
python -m uvicorn src.hypasia.api.main:app --reload
```

**Terminal 2: Start the Frontend (Vite/React)**
```bash
cd web
npm run dev
```

Finally, open your browser and navigate to `http://localhost:5173`.

## Documentation Hub
- [Features & Capabilities](docs/FEATURES.md)
- [System Architecture](docs/ARCHITECTURE.md)
- [Deployment Guide](docs/DEPLOYMENT.md)
- [Scoring Rubric](docs/SCORING_RUBRIC.md)
- [Contributing Guide](docs/CONTRIBUTING.md)
- [REST API Reference](docs/API_REFERENCE.md)
----
### Email me on majipritam47@gmail.com For any enquiry.
