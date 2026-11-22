# Paper draft materials (starting point; rewrite in your own words)

> arXiv bans authors who submit unvetted LLM-generated text. Use this as scaffolding: rewrite it, check every claim, and add your own analysis.

## Candidate title

**Open-Clinic-SRA: A Zero-Install SOC Testbed and Threat Taxonomy for Prompt Injection and Federated Learning Poisoning in Healthcare AI**

(Avoid claiming "detection framework" unless you add evaluation on non-synthetic or independently built data.)

## Abstract (~200 words)

Hospitals are deploying large language model (LLM) agents that read clinical documents and call tools, and they increasingly train diagnostic models across institutions with federated learning. Both expand the attack surface in ways that conventional security operations center (SOC) tooling does not model. Prompt injection hidden in clinical text can redirect an agent toward data exfiltration, and a single malicious federated participant can implant a backdoor in a shared model. We present Open-Clinic-SRA, an open, zero-install testbed that brings these AI-specific threats into the SOC workflow. Its contributions are (i) CLIN-AGENT, a five-class threat taxonomy for clinical AI deployments that maps each class to MITRE ATLAS/ATT&CK, required log sources, detection signals and clinical impact; (ii) a browser-based SIEM simulation with six scenarios, including indirect prompt injection and federated poisoning, for training and tabletop exercises; (iii) a deterministic synthetic labeled event dataset; and (iv) transparent baseline detection rules with an evaluation harness. Baseline results expose a concrete weakness in robust-statistics outlier detection for small federated cohorts. All artifacts are released under the MIT license with a persistent DOI to support teaching and reproducible follow-up work.

## Outline (6–8 pages, workshop/short-paper length)

1. **Introduction**: why AI agents and federated learning change the hospital SOC; contributions list
2. **Background & related work**: MITRE ATLAS; OWASP Top 10 for LLM Applications; prompt-injection literature (direct/indirect); FL poisoning and robust aggregation (Krum, trimmed mean, norm clipping); healthcare security education tools. *You must do this literature review yourself and cite real papers.*
3. **CLIN-AGENT taxonomy**: the table from `docs/taxonomy.md` plus a justification for each class
4. **Testbed design**: architecture, scenarios, framework mapping, export format; a screenshot figure
5. **Dataset & baselines**: generator design, schema, rules R1–R5, results table, R3 false-positive analysis
6. **Use cases**: training, SRA logging-gap analysis, tabletop exercises
7. **Limitations & ethics**: synthetic data, scripted detections, pattern-based injection detection is evadable, no real PHI
8. **Conclusion & future work**: harder datasets, adaptive attacks, integration with real SIEMs (Wazuh/Elastic Sigma rules)

## Strengthening the paper before submission (pick at least one)

- Have 5–10 SOC analysts or students use the demo and report a short pre/post knowledge quiz. That gives you a real user study, which makes it an education paper.
- Run R2 against a public prompt-injection dataset. Real evaluation data makes the detection claims meaningful.
- Run an actual small FL simulation (e.g. Flower + a public medical imaging set) and evaluate R3 against real updates.

## Venue ideas (check deadlines yourself)

- Security-education and SOC workshops; healthcare informatics venues (AMIA symposium/posters); IEEE conference poster and workshop tracks on AI security
- After peer review: post the accepted version to arXiv (cs.CR)
