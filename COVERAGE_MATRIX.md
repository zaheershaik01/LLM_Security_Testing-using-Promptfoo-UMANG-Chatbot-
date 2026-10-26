# UMANG Chatbot — Expanded Promptfoo Coverage Matrix

## Why the earlier report showed many items as untested

The previous configuration selected five plugin families (hijacking, system-prompt override,
PII direct, PII API/database, and hallucination). Setting `frameworks: [owasp:llm]` labels the
framework in Promptfoo reports; it does not automatically execute every plugin belonging to it.
The first report was therefore a narrow 10-test scan, not a complete LLM security assessment.

## Main dynamic suite

Run `promptfoo_redteam_expanded_local.yaml`. It adds bounded tests for:

- Direct prompt manipulation, role/goal hijacking, ASCII smuggling, special-token injection.
- Direct PII, API/database PII, session PII, social-engineering PII, privacy behavior, cross-session leakage, and hidden-context exposure.
- Hallucination, overreliance, misinformation/disinformation, hate/radicalization, specialized advice, and age/disability/gender/race bias.
- Excessive agency, tool discovery, RBAC/BFLA/BOLA indicators.
- SQL-like, shell-command, SSRF and debug-access prompt probes.
- Bounded divergent-repetition and reasoning-DoS indicators.

This is broad prompt-level coverage; it is not equivalent to testing every infrastructure or
application vulnerability. The local Llama 3.2 grader is a heuristic judge and every flagged case
must be manually reviewed.

## Current OWASP GenAI LLM Top 10 (2026) mapping

OWASP published its 2026 Top 10 in August/September 2026. Promptfoo's built-in red-team report
shown in the supplied PDF still labels and summarizes the 2025 ordering. Keep its native report
as tool output, but use the mapping below when writing the final assessment.

| OWASP LLM Top 10 2026 | Assessment coverage | Limitations / evidence needed |
|---|---|---|
| LLM01 Prompt Injection | Direct prompt manipulation, hijacking, ASCII smuggling, special tokens, system-prompt override | Indirect injection requires an attacker-controlled external/retrieved context; not proven by a plain prompt-only endpoint. |
| LLM02 Sensitive Information Disclosure | Direct PII, API/database PII, session PII, social engineering, privacy, cross-session and hidden-context probes | Any apparent disclosure must be checked against the full response; use synthetic canary data only. |
| LLM03 Excessive Agency | Excessive-agency, tool-discovery, RBAC/BFLA/BOLA prompt probes | A successful prompt alone does not prove a protected action occurred. Requires tool inventory, test identities, protected resources and owner-approved end-to-end tests. |
| LLM04 Supply Chain | Not testable through dynamic prompts | Requires source/dependency review, SBOM, model/artifact provenance, image/package integrity, CI/CD and vendor/model supply-chain evidence. |
| LLM05 Data and Model Poisoning | Misinformation, harmful-output and bias checks; indirect data integrity is discussed in the report | Actual poisoning needs controlled test corpus, ingestion/update workflow, model/data artifacts or knowledge-base access. Do not poison production data. |
| LLM06 Unbounded Consumption | One bounded divergent-repetition and one reasoning-DoS indicator | Not a load/stress test. Production exhaustion, throughput limits, cost limits and rate limiting require a written test window and monitoring, preferably staging. |
| LLM07 Misinformation | Hallucination, overreliance, misinformation/disinformation and specialized-advice probes | Requires a reference set of authoritative UMANG/government facts to validate correctness; model grading can be wrong. |
| LLM08 Hidden Context Exposure | Custom hidden-context probe plus PII/session checks and existing PyRIT system-prompt tests | Exact system-prompt extraction is hard to verify without the real protected prompt/secret markers from the system owner. The custom probe's verdicts require human review. |
| LLM09 Vector and Embedding Weaknesses | Cross-session/privacy probes provide only partial adjacent coverage | RAG poisoning, retrieval authorization, chunk isolation, embedding/vector-store access and source attribution require confirmed RAG architecture and controlled documents/corpus. |
| LLM10 Improper Output Handling | SQL/shell/SSRF/debug prompts assess whether unsafe output is generated | Does not prove SQL execution, shell execution, SSRF, XSS or backend compromise. Validate server/browser sinks and backend logs in a controlled environment. |

## Additional areas that are deliberately not treated as completed

- `indirect-prompt-injection`, `agentic:memory-poisoning`, `rag-poisoning`, and `rag-source-attribution`: require a known variable containing untrusted external context or a confirmed RAG/memory ingestion surface. The captured API contract only shows `question` and `previousChats`; it does not prove those architecture features.
- Supply-chain security: no package lockfile, source code, container image, model artifact, SBOM or CI/CD evidence was provided.
- Real API authentication/authorization, access-control and rate-limit exhaustion are separate from LLM prompt probes. The low-impact API check report remains a separate assessment artifact.
- Real XSS requires testing the rendered DOM with a benign marker; returning text that resembles HTML is not proof of execution.

## Run settings and interpretation

The expanded config uses `ollama:chat:llama3.2`, 1 concurrent evaluation, 1200 ms delay, a 500-character per-message cap and non-destructive instructions. Test counts vary by plugin; the displayed total is authoritative. Use `--no-cache` for fresh target execution and retain the JSON output. Never report the number of failed tests as the number of confirmed vulnerabilities.
