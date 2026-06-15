# Diagnosis scratchpad

Run the practice simulator, read YOUR telemetry, and note what you find.
Fault classes to hunt: error_spike · latency_spike · cost_blowup · quality_drift ·
infinite_loop · tool_failure · pii_leak.

| symptom (from telemetry) | which requests | suspected cause | config fix? | wrapper fix? |
|---|---|---|---|---|
| 0/20 ok, `wrapper_error`, no trace/usage | all practice requests before fix | PyInstaller binary could not import `openai` from venv | install OpenAI SDK in venv | prepend venv site-packages and stdlib to `sys.path` before `call_next` |
| verbose answers and unstable totals | single-question smoke tests | weak prompt, high temperature, model narration | temperature 0.2, shorter context, tool budget | recompute final total from tool trace and return parseable `Tong cong:` line |
| emails appear in practice questions | prac-002, prac-008 | baseline can echo customer contact info | `redact_pii: true` | redact email/phone in answers and telemetry |
| invalid coupons/out-of-stock/shipping failures need refusal | multiple practice requests | baseline may fabricate payable totals | normalize unicode, clear catalog override, retry/cache | convert tool failure observations to `Tong cong: unavailable` |

Completion checklist from docs:

- `python harness/selfcheck.py` passes.
- Run current phase simulator with key loaded: `set -a; . ./.env; set +a; ./bin/practice/observathon-sim --practice --config solution/config.json --wrapper solution/wrapper.py --out run_output.json`.
- For public/private phases, use `bin/<phase>/observathon-sim`, then run `bin/<phase>/observathon-score --run run_output.json --findings solution/findings.json --team TranQuangHuy --out score.json` when score binary is available.
- Commit/push `solution/`, `run_output.json`, and `score.json`; include logs/traces if requested.
