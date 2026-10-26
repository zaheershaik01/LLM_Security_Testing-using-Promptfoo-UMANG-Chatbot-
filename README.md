# UMANG Promptfoo Expanded Assessment

This project expands the original 10-test scan, which used only five plugin families. It targets the same confirmed HTTP endpoint and uses local Ollama generation/grading.

## Files

- `promptfoo_redteam_expanded_local.yaml` — expanded bounded dynamic prompt suite.
- `plugins/hidden-context-exposure.yaml` — custom rubric for hidden-context exposure without pretending to know UMANG's exact private system prompt.
- `COVERAGE_MATRIX.md` — mapping to current OWASP GenAI LLM Top 10 (2026), limitations and architecture-dependent areas.
- `RUN_EXPANDED_SCAN.md` — Windows execution and evidence instructions.

The supplied Promptfoo PDF uses the tool's built-in OWASP LLM 2025-era taxonomy, while OWASP published a 2026 edition. Keep native tool output and the manual 2026 coverage matrix distinct in the submission.
