# Actually Runs: free n8n templates

AI automations that keep working after the demo. These are the exact workflows from the **[Actually Runs](https://www.youtube.com/@actuallyruns)** YouTube channel.
Everything runs on free, self-hosted **n8n**, with **local AI through Ollama**: $0/month, and your data stays on your machine.

▶ Watch: [My AI Automation Failed Silently for a Month. Here's the Fix](https://youtu.be/e7mx0g_FcKk)

| Workflow | What it does |
|---|---|
| [`00-error-alert.json`](workflows/00-error-alert.json) | Shared error alarm. If any workflow fails, you get a Slack message **the same day**. |
| [`01-weekly-summary.json`](workflows/01-weekly-summary.json) | Monday 08:55: merged PRs from the last 7 days → an AI-written update → Slack. |
| [`02-inbox-triage-approval.json`](workflows/02-inbox-triage-approval.json) | New email → AI sorts it and drafts a reply → **you click Approve in Slack** before anything is sent. |
| [`03-meeting-to-actions.json`](workflows/03-meeting-to-actions.json) | Meeting transcript → task / owner / due date → one Slack message per task. |
| [`04-weekly-summary-production-proof.json`](workflows/04-weekly-summary-production-proof.json) | **The version from the video:** the weekly summary plus an *Output empty or cut off?* check → *Fail Loudly* → alert. A broken report never reaches your manager. |

## Setup (about 15 minutes)
1. **Install [Ollama](https://ollama.com)** and pull a model: `ollama pull qwen3.6:27b`. On machines with 16 GB of RAM or less, use `qwen2.5:7b-instruct` instead and change the model in each *Local AI* node.
2. **Run n8n:** `npx n8n`, or `docker run -p 5678:5678 docker.n8n.io/n8nio/n8n`.
3. **Import:** go to *Workflows → Import from File*. Import `00-error-alert.json` **first**, then the others.
4. **Credentials:**
   - Create an **Ollama** credential with base URL `http://127.0.0.1:11434` (inside Docker: `http://host.docker.internal:11434`).
   - Add Slack, GitHub and Gmail credentials as needed.
5. **Error alerts:** in each workflow, set *Settings → Error workflow* to **00 · Error Alert**.
6. **Fill in the details:** set the GitHub owner/repo in 01 and 04 and your Slack channel names. Use a **test inbox** for 02 first.
7. Activate the workflows.

Test the meeting workflow:
```bash
curl -X POST http://localhost:5678/webhook/meeting -H 'Content-Type: text/plain' \
  --data-binary 'Priya: I will finish the API migration by Friday. Raj, update the dashboard by Tuesday?'
```

## The three rules behind these workflows
1. **Check the answer, not the status code.** A `200 OK` with an empty body is still a failure.
2. **Make every failure loud.** Silent failures are the expensive ones.
3. **A human approves anything that leaves.** AI drafts, people send.

New builds every Tuesday on [YouTube](https://www.youtube.com/@actuallyruns). MIT licensed: use them, change them, ship them.
