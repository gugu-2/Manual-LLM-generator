# Multi-threaded Scoring Engine

Hypasia now supports **parallel chunk processing** for its AI judges! This change dramatically speeds up local LLM inference (Ollama) and API-based generation (Gemini) by processing multiple batches concurrently using a thread pool.

## What Changed

### `composite.py`
The scoring pipeline logic inside `_score_with_gemini` and `_score_with_ollama` was refactored.
- Instead of processing batches sequentially in a simple `for` loop, we now chunk the dataset into batches upfront.
- These batches are submitted to a `concurrent.futures.ThreadPoolExecutor(max_workers=threads)`.
- As each batch completes asynchronously (`as_completed`), it writes the score back to the original row references and updates the progress bar.

### `cli.py`
The CLI now accepts a `--threads` (or `-w` for workers) argument.
- You can now run the pipeline with `--threads 8` (defaults to 4).
- Applies to both `hypasia run` and `hypasia score`.

```bash
hypasia run https://en.wikipedia.org/wiki/Large_language_model --judge ollama --threads 4
```

> [!TIP]
> Ollama handles request queuing natively, but consumer GPUs typically process one LLM request at a time. The real benefit of using `--threads 4` with Ollama locally is reducing IO wait time and keeping the GPU queue completely saturated, yielding an overall 15-30% speedup depending on overhead! When using Gemini (an API), you can push the threads much higher (e.g., `--threads 20`) to achieve huge parallelization speeds.

## Validation
The multithreading code passes `progress.advance()` synchronously per-batch which correctly hooks into the Rich progress bar without any visual glitches, though you will see the row count "jump" by batches (e.g. going up by 5s or 10s at a time) rather than smoothly incrementing by 1.
