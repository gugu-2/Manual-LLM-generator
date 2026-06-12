# Contributing to Hypasia AI

Hypasia is built with an explicitly modular architecture. The easiest way to contribute is by adding new Data Parsers or LLM Connectors.

## Adding a New Document Parser

Want to mine Audio files? EPUBs? Video Transcripts? 
All parsers live in `src/hypasia/mining/parsers/`.

**1. Create your parser file:**
```python
# src/hypasia/mining/parsers/audio.py
from pathlib import Path
from hypasia.schema import HypasiaRow
import whisper # your logic here

def parse_audio(path: Path) -> list[HypasiaRow]:
    # Extract text from audio
    text = whisper.transcribe(path)
    
    # Return formatted rows
    return [
        HypasiaRow(
            instruction="Transcribe the following audio.",
            response=text,
            source=str(path),
            source_type="audio"
        )
    ]
```

**2. Register it in the Dispatcher:**
Open `src/hypasia/mining/parsers/dispatcher.py` and map the file extension:
```python
_EXTENSION_MAP = {
    ".pdf": "pdf",
    ".mp3": "audio", # <-- New mapping
    ".wav": "audio", # <-- New mapping
}

def parse_file(path: Path) -> list[HypasiaRow]:
    # ...
    elif parser_type == "audio":
        from hypasia.mining.parsers.audio import parse_audio
        return parse_audio(path)
```

## Adding a New Cloud Code Generator Target

If you want to generate code for Azure ML or Modal instead of AWS/GCP, edit `src/hypasia/api/routes/finetune.py`.

1. Define a new template (e.g., `AZURE_TEMPLATE = """..."""`).
2. Add the UI button to `web/src/pages/FineTuneStudio.jsx`.
3. Update the Python logic to format the new template.
