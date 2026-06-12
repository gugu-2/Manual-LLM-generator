# Multi-threading the Scoring Engine

Currently, Hypasia's LLM judges (Ollama and Gemini) score row batches sequentially. This limits throughput, especially when local GPUs or API rate limits can handle concurrent requests.

This plan proposes adding multi-threading to parallelize batch processing and significantly speed up the scoring step.

## Proposed Changes

### `src/hypasia/scorer/composite.py`
We will update the `score_rows` pipeline to process batches concurrently:
- Update `score_rows` signature to accept a `threads: int = 4` parameter.
- Refactor `_score_with_gemini` and `_score_with_ollama` to use `concurrent.futures.ThreadPoolExecutor(max_workers=threads)`.
- Split the `rows` into batches, submit each batch to the thread pool, and use `as_completed` to process results as they finish.
- Ensure that the rich `progress.advance()` calls are safely updated as each thread completes its batch.

### `src/hypasia/cli.py`
We need to expose the thread count via the CLI:
#### [MODIFY] `src/hypasia/cli.py`
- Add a new argument `threads: int = typer.Option(4, "--threads", "-w", help="Number of concurrent threads for scoring")` to the `run` command.
- Add the same `threads` argument to the `score` command.
- Pass the `threads` argument down into `score_rows`.

## User Review Required

> [!IMPORTANT]
> Since Ollama defaults to queueing requests sequentially on a single GPU, using too many threads on a consumer GPU might increase memory overhead without speeding up inference. I suggest defaulting to `4` threads, which keeps the queue saturated and takes advantage of IO wait time. Do you agree with a default of 4?

> [!WARNING]
> Multi-threading will cause progress updates to jump in chunks (e.g., jumping by 5 or 10 rows at a time depending on batch size) rather than incrementing perfectly linearly. This is expected behavior.

## Verification Plan

### Automated Tests
- Run `hypasia score sample_dataset.jsonl --judge ollama --threads 4` and verify that scoring completes without race conditions or dropped rows.
- Verify that `hypasia run` still works end-to-end with the new thread argument.
