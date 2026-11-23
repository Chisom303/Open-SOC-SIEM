# Teaching with Open SOC SIEM

I built this project partly because I couldn't find a good way to *show* someone what a prompt injection looks like from the security team's side. Most of what I found was either papers that measure whether a model gets fooled, or research code that took an afternoon to set up. Neither works well when you have 50 minutes and a room of students on different laptops.

This guide is for instructors who want to use the demo and dataset in a course. It fits upper-level undergraduate or graduate classes in cybersecurity, health informatics, or AI/ML security. Students should know roughly what a log file is. They don't need to know anything about LLMs or federated learning beforehand.

Everything here runs offline. Nothing needs installing except Python for the labs, and the scripts use only the standard library.

---

## What students should be able to do afterwards

1. Explain how an instruction hidden in a clinical document can redirect an AI agent, and point to where in the logs that shows up.
2. Explain how one participant in a federated learning consortium can damage a model that every hospital shares.
3. Read a detection rule, predict what it will miss, and check that prediction against data.
4. Explain why detection is a backstop for prompt injection rather than a fix, and name the architectural controls that matter more.
5. Map an incident to MITRE ATT&CK or MITRE ATLAS.

---

## Option A: one 50-minute lecture

You only need `simulator.html` open on the projector.

| Time | What happens | Notes |
|---|---|---|
| 0–5 | **Framing.** Hospitals are starting to deploy agents that read records and call tools, and to train shared models across sites. Ask the room: "If an agent reads a poisoned document, who notices?" | Let a few people answer. Usually someone says "the model should refuse," which sets up the rest of the hour. |
| 5–15 | **Baseline: a normal attack.** Run *Ransomware Timeline*. Walk the kill chain stage by stage. Click a few alerts to show the detection logic and the ATT&CK technique. | This gives everyone a reference point for what a SIEM is good at. |
| 15–28 | **Prompt injection.** Reset, then run *Prompt Injection*. Pause on the log line with `[SYSTEM: Ignore previous instructions…]`. Ask: which part of the system was attacked? (Not the network. The *data* the agent read.) Then click the alert and show the two signals: the suspicious text, and the agent trying an outbound call it shouldn't make. | Point out that the purple ATLAS box lit up, not an ATT&CK tactic. That difference matters for the discussion later. |
| 28–40 | **Federated poisoning.** Run *Federated Learning Poisoning*. Use the panel on the right: Hospital C's update norm is 18.7 while everyone else sits around 1.4–2.1. Accuracy falls from 94.2% to 67.8%, then recovers once C is dropped. | Good question here: "What if Hospital C had been careful and kept its update only slightly larger than normal?" Don't answer it yet. Lab 2 does. |
| 40–50 | **Pair exercise.** Hand out the taxonomy table (`docs/taxonomy.md`). Each pair picks one class (C1–C5) and writes down which logs their own organization (or a hospital they can imagine) collects today, and which are missing. Collect two or three answers. | Most groups find they have no record of *what the agent read*, only what it said. That's the main takeaway. |

**Tip:** press **Export events** at the end and share the JSONL file with the class. It's a small, real artifact from the session they just watched, and it works as a warm-up for the labs.

---

## Option B: lecture plus labs (2–3 weeks)

Run the lecture first, then assign the labs. Each lab takes about 2–3 hours. They build on each other, but you can assign any one alone.

Setup for students:

```bash
git clone https://github.com/Chisom303/Open-SOC-SIEM
cd Open-SOC-SIEM
python scripts/detect.py      # should print the baseline table
```

Each lab asks students to submit code plus a short write-up (about one page). The write-up matters more than the code.

### Lab 1: The attack that went too slowly

Rule R1 flags six or more failed logins from one IP within 60 seconds. Run `detect.py`: R1's recall is 0.81, not 1.00. It misses something.

1. Change `detect.py` so it prints the events R1 missed. Look at their timestamps. What happened?
2. Propose a change to R1 that catches what it missed. Measure precision and recall again.
3. Now take the attacker's side. Using your improved rule, describe the slowest brute-force pattern that still gets past it, and estimate how many passwords that attacker could try per day.
4. Write-up question: our synthetic dataset has very little benign login noise. Would your improved rule hold up in a real hospital at 7 a.m., when hundreds of staff log in and some of them mistype? What would you add?

### Lab 2: Making the poisoning detector less jumpy

Rule R3 flags a federated client whose update norm exceeds `median + 3 × MAD` for that round. It catches both poisoned updates but also flags two honest hospitals.

1. Find the two false positives. Work out *why* the rule fired. Look at how spread out the other clients' norms were in those rounds.
2. Write a replacement rule with no false positives that still catches both attacks. Compare at least two designs.
3. The harder question: suppose Hospital C knows your rule. Change the generator, or append events by hand, to create a poisoned update designed to slip under your threshold. Does your rule catch it?
4. Write-up question: if norm thresholds can always be gamed by a careful attacker, what else should the FL server check? Think about what happens to the model, not just the size of the update.

### Lab 3: Breaking the prompt-injection rule (and why it doesn't fully matter)

Rule R2 has two parts: a regex that looks for phrases like "ignore previous instructions," and a check that blocks the agent from posting data to any destination not on an allowlist.

1. Write three injected clinical notes that get past the regex. Append them to a copy of the dataset as `llm_agent` events (template below) and confirm R2 misses them.
2. Now add a follow-up event where the agent, having been fooled, calls `http_post` to an outside domain. Does R2 catch the incident anyway? Which part of the rule did the work?
3. Design an injection whose goal needs *no* outbound call. For example, it quietly changes what the agent writes into a summary. Can either part of R2 see it? What log would you need to see it?
4. Write-up question: based on parts 1–3, argue whether a hospital should spend its next dollar on better injection *detection* or on limiting what agents are *allowed to do*. Use the taxonomy (C1 and C2) in your answer.

Event template for Lab 3 (one line of JSON per event; give each a new `event_id`):

```json
{"event_id": 9001, "ts": "2026-11-04T10:15:00Z", "host": "LLM-AGENT-API", "source": "llm_agent", "message": "tool=read_file doc=referral_letter", "label": "malicious", "scenario": "prompt_injection", "tool": "read_file", "tool_output": "YOUR INJECTED NOTE HERE"}
```

---

## Discussion questions

Use these in class or as short written reflections.

1. The ransomware alerts mapped to ATT&CK, but the prompt-injection alert mapped to ATLAS. Why does a separate framework exist? What breaks if a SOC tries to force AI attacks into ATT&CK categories?
2. In the federated scenario, the server rejected Hospital C. Hospital C is a real hospital with real patients. Who should make that call, and what should happen to C's participation afterwards?
3. Logging what an agent reads helps detect injection, but those logs now contain patient data. How would you design logging that serves the SOC without creating a new privacy problem?
4. A vendor says their clinical agent "can't be prompt-injected." What three questions would you ask them?
5. The demo shows a detection time of a few seconds. Why is that number meaningless here, and what would you need in order to measure a real one?

---

## A few honest notes for instructors

- **The demo is a simulation.** Alerts are scripted, not produced by a live detection engine. I say this on screen and I'd say it to students too. It doesn't weaken the lesson, and it keeps them from overestimating what they saw.
- **The dataset is easy on purpose.** Attacks are clean and benign noise is light. That's fine for learning how to reason about rules, but scores on this data say little about real-world performance. Lab 1, part 4 is there to make students notice that themselves.
- **The answer key is public.** Solutions are in [`docs/INSTRUCTOR_KEY.md`](docs/INSTRUCTOR_KEY.md), and since this is a public repo, students can find them. To get fresh data, change `SEED` at the top of `scripts/generate_logs.py` and regenerate. The specific missed events and false positives will change, but the lessons hold.
- Expect Lab 3 part 3 to cause the most argument. That's intended.

If you use this in a course, I'd really like to hear how it went. What confused students, what you changed, what broke. Please open an issue or email me. Feedback from actual classrooms is the fastest way for this to get better.

Chisom Alozie
