# Sample logs for the Open SOC SIEM Log Analyzer

Practice files with planted attacks. All IP addresses come from the reserved documentation ranges (RFC 5737) or private ranges, and all hosts and users are invented.

| File | Format | What's hidden in it |
|---|---|---|
| `auth.log` | Linux auth log | A brute-force attack that succeeds, a refused sudo attempt, a new account created by the attacker, and a username-enumeration run from a second address |
| `access.log` | Nginx/Apache combined log | A wordlist scan with an exposed `.env` file, plus SQL injection, script injection and path traversal attempts from a tool that names itself |
| `agent.jsonl` | JSON lines from an AI agent | An indirect prompt injection hidden in a fetched web page, the agent's attempt to send data out, and a direct jailbreak attempt in chat |

Open `analyzer.html`, click **Choose files**, and pick one or more of these. Try **Challenge mode** first and see how many you can find before revealing the answers.

## Example lab file

`example-lab.json` is a ready-made lab built from the three sample logs, with two custom rules and eight questions. Open `analyzer.html`, click **Open a lab file**, and choose it to see the student view.
