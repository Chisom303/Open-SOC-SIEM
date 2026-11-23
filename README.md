# Open SOC SIEM

**A zero-install security operations training platform for students and researchers.** Two browser tools, no installation:

- **Attack Simulator** (`simulator.html`): watch six attacks unfold in a simulated SOC, including prompt injection against AI agents and federated learning poisoning, mapped to MITRE ATT&CK and ATLAS.
- **Log Analyzer** (`analyzer.html`): upload real log files and get explained findings, with challenge mode and tunable rules for teaching.

It also includes a threat taxonomy for clinical AI agents, a synthetic labeled dataset, baseline detectors and ready-made teaching labs.

> Formerly released as *Open-Clinic-SRA SIEM Demo* (v1.0.x).

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.24448612.svg)](https://doi.org/10.5281/zenodo.24448612)
![version](https://img.shields.io/badge/version-2.2-green) ![license](https://img.shields.io/badge/license-Apache--2.0-blue)

> **Cite as:**
> Alozie, C. (2026). *Open SOC SIEM: A zero-install security operations training platform with an attack simulator and log analyzer* (Version 2.2) [Computer software]. Zenodo. https://doi.org/10.5281/zenodo.24448612
>
> BibTeX and other formats: use GitHub's **"Cite this repository"** button (generated from [`CITATION.cff`](CITATION.cff)).

![Open SOC SIEM Attack Simulator screenshot](docs/screenshot.png)

---

## Run it in 2 minutes (no install)

**Start here:** open `index.html` (live: `https://chisom303.github.io/Open-SOC-SIEM/`) and choose **Try a sample investigation** or **Build a classroom lab**.

**Attack Simulator:** open `simulator.html` (live: `https://chisom303.github.io/Open-SOC-SIEM/simulator.html`) and click **▶ Prompt Injection** or **▶ Federated Learning Poisoning**.

**Log Analyzer:** open `analyzer.html` (live: `https://chisom303.github.io/Open-SOC-SIEM/analyzer.html`), then drop in your own log files or click **Load all samples**.

That's it. Nothing to install. A downloaded copy runs fully offline. The hosted page counts anonymous visits and scenario clicks with [GoatCounter](https://www.goatcounter.com), a privacy-friendly analytics service; no simulated or exported data is sent anywhere.

To reproduce the dataset and the baseline results (Python 3.9+, standard library only):

```bash
python scripts/generate_logs.py   # rebuilds data/synthetic_events.{jsonl,csv} deterministically
python scripts/detect.py          # runs 5 baseline rules, prints precision/recall/F1
```

## What's in this repo

| Component | Path | What it's for |
|---|---|---|
| Homepage | `index.html` | Starting point with the two main paths: a sample investigation for learners and the lab builder for instructors |
| Attack Simulator | `simulator.html` | Teaching, demos, tabletop exercises. Six scenarios, mapped to MITRE ATT&CK and MITRE ATLAS |
| Threat taxonomy | [`docs/taxonomy.md`](docs/taxonomy.md) | Five attack classes against clinical AI agents, each with log sources, detection signals and clinical impact |
| Synthetic labeled dataset | [`data/`](data/) | 340 events (51 malicious, 16 attack episodes), JSONL + CSV, with a [data card](data/DATA_CARD.md) |
| Baseline detectors | `scripts/detect.py` | Five transparent rules and an evaluation harness, a starting point to compare against |
| Event export | **⬇ Export events** button | Saves every log and alert from a demo session as JSONL |
| Log Analyzer | [`analyzer.html`](analyzer.html) | Upload your own log files (Linux auth/syslog, Apache/Nginx, JSON lines, CSV). It flags brute force, web scanning, injection attempts, prompt injection and more, explains each finding with its MITRE technique, and charts the results. Download the report as HTML (printable to PDF) or Markdown, or the findings and events as CSV. Runs entirely in the browser. Instructors can add their own detection rules and build labs (see below). |
| Sample logs | [`samples/`](samples/) | Practice files with planted attacks: `auth.log`, `access.log`, `agent.jsonl` |
| Jupyter notebook | [`notebooks/Open_SOC_SIEM_Lab.ipynb`](notebooks/Open_SOC_SIEM_Lab.ipynb) | Step-by-step walkthrough: generates the dataset, runs the five baseline rules, scores them, and reproduces the failure analysis, with an explanation before every code cell |
| Teaching materials | [`TEACHING.md`](TEACHING.md) | A 50-minute lecture plan, three hands-on labs, discussion questions, and an [instructor key](docs/INSTRUCTOR_KEY.md) |

## For instructors: use your own logs

The Log Analyzer works with any logs you load, and its **Instructor tools** panel (at the bottom of the page) turns them into class exercises:

1. **Load your logs** (Linux syslog/`auth.log`, Apache/Nginx, JSON lines, or CSV, including Windows events exported to CSV).
2. **Add custom detection rules** without coding: a *text match* (flag every line containing a word or pattern) or a *threshold* (flag N matching events from one IP or user within W seconds). Give each rule a severity, an optional MITRE technique and a plain-language explanation for students. Rules are saved in your browser.
3. **Build a lab:** write questions with accepted answers, or click *Suggest questions from findings* to generate them, then **Download lab file**. Use *Preview as a student* to test it first.
4. **Share the lab file** with students (LMS, email). They click **Open a lab file**, investigate with the findings hidden, check their answers with instant feedback, and **Download my answers** to submit.

Lab files contain the logs, your rules and the questions. Answers and explanations are stored scrambled so they cannot simply be read from the file; this discourages peeking but is not strong security, so do not use it for high-stakes exams. Everything runs in the browser, and no log data is uploaded anywhere.

## Scenarios

| Scenario | Framework mapping | Detection logic shown |
|---|---|---|
| SSH brute force | ATT&CK T1110.001 | ≥6 failures per source IP in 60 s |
| Phishing → PowerShell | ATT&CK T1566.001, T1059.001 | Encoded PowerShell with Outlook as parent |
| **Indirect prompt injection** | ATLAS AML.T0051.001, ATT&CK T1567 | Instruction-override pattern in tool output + non-allowlisted egress tool call |
| Ransomware | ATT&CK T1486 | Burst of file renames per process |
| **Federated learning poisoning** | ATLAS AML.T0020, AML.T0018 | Update-norm outlier (median + 3·MAD) + backdoor trigger-set probe |
| Ransomware kill chain | ATT&CK T1566.001 → T1547.001 → T1135 → T1003.001 → T1490 → T1486 | Multi-stage timeline |

## Baseline results (synthetic data)

| Rule | Precision | Recall | F1 |
|---|---|---|---|
| R1 brute force (≥6 fails / 60 s) | 1.00 | 0.81 | 0.90 |
| R2 prompt injection | 1.00 | 1.00 | 1.00 |
| R3 FL poisoning (median+3·MAD) | 0.50 | 1.00 | 0.67 |
| R4 ransomware | 1.00 | 1.00 | 1.00 |
| R5 phishing PowerShell | 1.00 | 1.00 | 1.00 |
| **Overall** | **0.96** | **0.88** | **0.92** |

Two failures are left in on purpose, because they're instructive. R1 misses one whole brute-force episode whose six attempts span 67 seconds, just outside its window. R3 raises false positives because MAD over five clients is unstable: when the benign clients happen to cluster tightly, a normal update can cross the threshold. Both become exercises in [`TEACHING.md`](TEACHING.md).

## Limitations (please read before citing results)

- **This is a simulation.** The browser demo plays back scripted events. It is not connected to a live SIEM, EDR or LLM.
- **The dataset is synthetic, and the same author built both the data and the rules,** so the near-perfect scores above do not measure real-world detection performance. Treat them as a sanity check and as a baseline for others to beat on harder data.
- The prompt-injection rule is pattern-based and easy to evade with paraphrasing or encoding. Robust defenses need architectural controls, such as separating instructions from data and allowlisting tools and egress.
- ATLAS technique IDs follow the ATLAS knowledge base at the time of writing. Check them against the release you cite.

## Contributing

Issues and pull requests are welcome, especially new scenarios, harder synthetic data, or detectors that beat the baselines. If you use this work in a paper or course, please cite it (see above) and open an issue so it can be listed below.

**Used by:** *(your project here)*

## License

Copyright 2026 Chisom Alozie. Code, data and teaching materials are licensed under the [Apache License, Version 2.0](LICENSE); see also [NOTICE](NOTICE). If you redistribute or adapt this work, keep the LICENSE, the NOTICE and the copyright headers, and state what you changed. Versions up to 2.1 were released under the MIT License, and copies of those versions remain under MIT.

All hosts, users, IPs and clinical text are fictitious. Domains use the reserved `.example` TLD.

## Author

Chisom Alozie · University of the Cumberlands · ORCID [0009-0000-0309-3650](https://orcid.org/0009-0000-0309-3650)
