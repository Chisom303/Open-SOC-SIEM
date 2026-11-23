# CLIN-AGENT Threat Taxonomy v1.0

Five attack classes against AI agents and collaborative models deployed in clinical environments. For each class it gives the entry point, the log sources a SOC needs, observable detection signals, and the clinical consequence. That last part is what separates healthcare from generic LLM security.

> Cite as: Alozie, C. (2026). CLIN-AGENT Threat Taxonomy v1.0. In *Open SOC SIEM* (Version 2.2). https://doi.org/10.5281/zenodo.24484803
>
> ATLAS/ATT&CK IDs reflect the knowledge bases at the time of writing. Check them against the release you cite.

| # | Class | Entry point | Framework mapping | Required log sources | Detection signals | Clinical impact |
|---|---|---|---|---|---|---|
| C1 | **Indirect prompt injection via clinical documents** | Text the agent ingests: EHR notes, referral letters, faxed PDFs, patient-portal messages, retrieved guidelines | ATLAS AML.T0051.001 | Agent tool-call logs with tool **output**; retrieval logs (doc ID, source) | Instruction-like content in data channels; agent action deviates from user intent right after a retrieval; new tool type invoked mid-session | Unauthorized PHI disclosure; wrong summaries entering the chart |
| C2 | **Agent tool abuse and PHI exfiltration** | Over-privileged agent tools (HTTP, email, file write, EHR write) | ATLAS AML.T0051 (as trigger); ATT&CK T1567 | Egress proxy; agent tool-call logs; EHR audit trail | Egress to non-allowlisted destinations; bulk record reads by a service identity; tool calls outside the role's normal set | HIPAA breach; integrity loss if the agent can write orders or notes |
| C3 | **Direct prompt injection / jailbreak by an insider or patient** | Chat interface (clinician-facing or patient-facing) | ATLAS AML.T0051.000 | Prompt and response logs (with PHI minimization); safety-filter verdicts | Repeated safety-filter hits per user; role-play or "admin mode" phrasing; system-prompt extraction attempts | Unsafe clinical advice; disclosure of other patients' data |
| C4 | **Federated learning poisoning / backdoor** | A compromised or malicious participating site in a federated consortium | ATLAS AML.T0020, AML.T0018 | Per-client update metadata (norm, cosine similarity to aggregate, local loss); held-out validation and trigger-set probes per round | Update-norm or direction outliers; local loss inconsistent with the cohort; validation accuracy drop after aggregation; trigger-set attack success | Systematic misdiagnosis across all sites that use the shared model; hidden behavior that activates only on a trigger |
| C5 | **Model and data supply-chain compromise** | Pretrained weights, fine-tuning datasets, agent plugins or MCP servers pulled from third parties | ATLAS AML.T0010 | Artifact hashes and provenance (SBOM/AIBOM); plugin install logs | Unsigned or unpinned model artifacts; hash mismatch; new plugin with network or file permissions | Compromise of every downstream deployment; hard to detect after deployment |

## How to use it

- **SOC teams:** use the "Required log sources" column as a logging gap checklist before deploying a clinical agent.
- **Researchers:** use the class IDs (C1–C5) to label datasets or position contributions, e.g. *"we address CLIN-AGENT C4"*.
- **Risk assessments:** pair each class with likelihood and impact for a site-specific Security Risk Assessment (SRA).

## Design principles

1. **Instruction–data separation.** Content from data channels (C1) never gains instruction authority. Detection is a backstop for this control, not a substitute.
2. **Least-privilege tools.** Most of C2's harm is bounded by restricting tools and egress, whatever the injection technique.
3. **Per-round attestation in federated learning.** Every aggregation round logs per-client statistics so C4 is auditable after the fact.

## Changelog

- v1.0 (2026-11-21): initial release.
