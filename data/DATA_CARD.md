# Data Card: Open-Clinic-SRA Synthetic Events v1.0.0

**Files:** `synthetic_events.jsonl`, `synthetic_events.csv` (same content)
**Generator:** `scripts/generate_logs.py` (seed `20261121`, deterministic)
**License:** MIT

## Summary

| | |
|---|---|
| Total events | 340 |
| Malicious | 51 (16 attack episodes) |
| Benign | 289 |
| Simulated span | 5 days, 5 hosts |
| Scenarios | bruteforce (4 ep.), prompt_injection (4), phishing (3), ransomware (3), federated_poisoning (2) |

## Schema (key fields)

| Field | Description |
|---|---|
| `event_id` | Sequential ID, sorted by time |
| `ts` | ISO-8601 UTC timestamp |
| `host`, `source` | Simulated host; log source (`auth`, `sysmon`, `llm_agent`, `federated`, `agent`) |
| `message` | Human-readable log line |
| `label` | `benign` / `malicious` (ground truth) |
| `scenario`, `episode_id` | Attack scenario and episode grouping (null for benign) |
| `attck_technique`, `atlas_technique` | Framework labels on malicious events |
| Source-specific | `src_ip`, `outcome`, `user`, `tool`, `tool_output`, `dest`, `fl_round`, `client`, `update_norm`, `local_loss`, `image`, `parent_image`, `file_renames_5s` |

## Intended use

Teaching, prototyping detection rules, and smoke-testing SIEM pipelines. It is **not** a benchmark of real-world detection performance.

## Known limitations

- Attack patterns are clean and stereotyped, much easier than real adversaries.
- Benign noise is sparse compared with a real hospital network.
- The rules in `scripts/detect.py` and this data come from the same author, so evaluation is not independent.
- The clinical text is short and fictitious, with no real PHI.

## Suggested extensions

Paraphrased or encoded injections, adaptive FL attacks that stay within norm bounds, higher benign volume, and multi-host lateral movement.
