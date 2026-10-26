# Run the expanded Promptfoo scan (Windows PowerShell)

## Before starting

Only run against the approved UMANG chatbot endpoint within the scope and test window authorized by the system owner. This is a bounded dynamic prompt assessment, not a load test. Do not increase concurrency or attempt resource exhaustion against production.

Open PowerShell in this folder and confirm Ollama is running and has the model:

```powershell
ollama list
```

Set local-only generation/grading and telemetry preferences in this terminal:

```powershell
$env:OLLAMA_BASE_URL = "http://127.0.0.1:11434"
$env:PROMPTFOO_DISABLE_REMOTE_GENERATION = "true"
$env:PROMPTFOO_DISABLE_TELEMETRY = "1"
```

Run the expanded suite with fresh target calls (no cache):

```powershell
promptfoo redteam run -c promptfoo_redteam_expanded_local.yaml --force --max-concurrency 1 --delay 1200 --no-cache
```

If your Promptfoo version rejects `--no-cache` for `redteam run`, rerun without that flag and verify the `Actual Target Requests` count; do not rerun the scan repeatedly just to improve a score.

When it finishes, export or preserve the evaluation JSON from the results/evaluation shown by the CLI, then run:

```powershell
promptfoo redteam report
promptfoo view
```

Save the report output/PDF into `reports/` and the JSON evaluation in `results/`. Save sanitized terminal screenshots in `evidence/`.

## Review rules

1. Read the prompt, full target response and grader reason for every failed test.
2. Manually validate any potential data disclosure, prompt leakage or factuality issue.
3. Mark architecture-dependent probes as `Not applicable / requires architecture evidence`, not as passed.
4. Use only synthetic canary strings for cross-session/privacy tests. Do not ask for actual user records or put actual credentials in prompts.
5. Record actual target request count. Cached cases are not fresh observations.
6. The Ollama judge is approximate; don't treat its severity labels as final vulnerability severity.
