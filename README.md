# Team Intelligence Hub

Turn any meeting transcript into structured team artifacts — Jira tickets, GitHub docs, Slack messages, escalation PRs, and a living HTML hub — with human review before anything is published.

Built as Claude Code slash commands. No new tools to learn: open a terminal, run a command, review what Claude drafts, approve or edit, publish.

---

## What you get

Drop in a transcript. Get back:

| Artifact | Where it goes |
|---|---|
| Action item & blocker tickets | Jira |
| Decisions, ADRs, production log, customer feedback | GitHub (committed to your docs repo) |
| Escalation documents | GitHub PR (with assigned reviewers) |
| FYI summary | Slack |
| Sprint prep, decisions, customer feedback pages | Team Intelligence Hub (GitHub Pages) |
| Living pages per team member, service, and customer | `docs/team/`, `docs/services/`, `docs/customers/` |

You review every artifact before anything is published. Nothing touches Jira, Slack, or GitHub until you approve it.

---

## Prerequisites

| Tool | Install |
|---|---|
| [Claude Code](https://docs.anthropic.com/claude-code) | See install guide |
| [GitHub CLI](https://cli.github.com) | `brew install gh` then `gh auth login` |
| Jira API token | Generate at [id.atlassian.com](https://id.atlassian.com/manage-profile/security/api-tokens) |
| Slack bot token | Create a bot at [api.slack.com/apps](https://api.slack.com/apps) with `chat:write` scope |

---

## Setup — 4 steps

### Step 1 — Clone the repo and open it in Claude Code

```bash
git clone https://github.com/varshavenkatesh/ai-native-org-skills
cd ai-native-org-skills
claude
```

![Step 1: Clone the repo and open Claude Code](docs/screenshots/01-clone-and-open.svg)

---

### Step 2 — Configure your team

Copy the example config and fill in your team's details:

```bash
cp config/teams.example.yaml config/teams.yaml
```

Open `config/teams.yaml` and replace the placeholders:

```yaml
teams:
  - name: "Platform"
    aliases:
      - "platform"
      - "infra"
    jira:
      base_url: "https://yourorg.atlassian.net"
      project_key: "PLAT"
    github:
      org: "yourorg"
      repo: "platform-team-docs"
    slack:
      channel: "#platform-team"
      escalation_channel: "#platform-leadership"
    escalation_reviewers:
      em: "your-github-username"
      pm: "pm-github-username"
      tech_lead: "techlead-github-username"
```

Add one block per scrum team. The `aliases` list is how Claude identifies which team a transcript belongs to without you having to specify it manually.

![Step 2: Filling in teams.yaml](docs/screenshots/02-teams-config.svg)

---

### Step 3 — Set environment variables

Add these to your shell profile (`.zshrc` / `.bashrc`) or a `.env` file at the repo root:

```bash
export JIRA_API_TOKEN="your-jira-api-token"
export JIRA_EMAIL="you@yourorg.com"
export SLACK_BOT_TOKEN="xoxb-your-slack-bot-token"
export GITHUB_TOKEN="your-github-pat"   # optional if gh auth login is done
```

`.env` is gitignored — it will never be committed.

---

### Step 4 — Run your first digest

Point Claude at any transcript. The sample in this repo is a good place to start:

```
/RESOLVER samples/standup-2026-05-02.txt
```

Or use `/meeting-digest` directly:

```
/meeting-digest samples/standup-2026-05-02.txt
```

![Step 4: Running your first /RESOLVER command](docs/screenshots/03-resolver-command.svg)

---

## What happens next

Claude reads the transcript, infers the meeting type and team, then presents a full review panel before touching anything:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  MEETING DIGEST — REVIEW BEFORE PUBLISHING
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Meeting : standup
  Team    : Payments
  Date    : 2026-05-02
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[1] JIRA TICKET — PAY
    Summary  : Fix invoice PDF export timeout for >50 line items
    Type     : Bug
    Priority : High
    Assignee : Maya
    Labels   : customer-reported, month-end
    ─────────────────────────────
    Acme Corp reported invoice PDF export times out for invoices
    with more than 50 line items. Reproducible locally. Finance team
    running 200-line-item exports manually. High priority given month-end.
    ─────────────────────────────
    Duplicate check: will search PAY board before creating

[2] JIRA TICKET — PAY
    Summary  : Update payment retry logic for Stripe v4 idempotency key format
    Type     : Task
    Priority : High
    Assignee : Dev
    ─────────────────────────────
    Stripe SDK v4 changes idempotency key format. Retry logic in
    payment processor must be updated before Stripe migration PR merges
    to avoid duplicate charges on retries.

[3] GITHUB COMMIT — yourorg/platform-team-docs → docs/decisions/decisions.md
    Action  : append
    ─────────────────────────────
    ## 2026-05-02 — Standardize idempotency keys on UUID v7
    Decision: Use UUID v7 (not v4) for idempotency keys across all
    payment service calls. UUID v7 is time-ordered, simplifies retry
    deduplication, and aligns with Stripe v4 expectations.
    Agreed by: Arjun, Rohan, Dev

[4] GITHUB PR — yourorg/platform-team-docs
    Title     : [ESCALATION] Salesforce billing integration — capacity decision needed
    Reviewers : em-github-username, pm-github-username, techlead-github-username
    Labels    : escalation, standup
    ─────────────────────────────
    Priya flagged that the Salesforce enterprise billing integration
    (Q2 deadline) conflicts with current sprint capacity (Stripe
    migration + 3DS2). Requesting EM decision: push timeline or descope.

[5] SLACK — #payments-team
    ─────────────────────────────
    *Payments Standup — 2026-05-02*
    • Stripe SDK v4 migration branch ready for review (Arjun)
    • One-click checkout experiment at 20% → ramping to 50% (Rohan)
    • Invoice PDF timeout bug filed — high priority (Maya)
    • Stripe sandbox creds being sent to Dev (Rohan)
    :warning: Escalation PR opened: Salesforce integration capacity

─────────────────────────────────────────────
  Found 5 artifacts. How would you like to proceed?

  A  — Approve all and publish
  R  — Review individually (approve / edit / skip each)
  X  — Cancel
─────────────────────────────────────────────
```

Press **A** to publish everything, **R** to step through and edit individual artifacts, or **X** to cancel. Nothing is sent until you choose.

![Review panel in Claude Code](docs/screenshots/04-review-panel.svg)

---

## Querying the team brain

After a few meetings, the `docs/` directory becomes a searchable knowledge base. Use `/RESOLVER` to query it:

```
/RESOLVER what is Maya working on this sprint?
/RESOLVER what did we decide about idempotency keys?
/RESOLVER what has Acme Corp reported recently?
/RESOLVER what shipped last week?
```

Claude reads the entity pages and decision log directly and answers from the content — no hallucination, no fabrication.

![Querying the team brain with /RESOLVER](docs/screenshots/05-brain-query.svg)

---

## Supported meeting types

| Type | Command | What's extracted |
|---|---|---|
| `standup` | `/meeting-digest transcript.txt standup` | Action items, blockers, decisions, prod status, customer feedback, escalations |
| `planning` | `/meeting-digest transcript.txt planning` | Sprint backlog, Jira stories, sprint prep report |
| `customer-session` | `/meeting-digest transcript.txt customer-session` | Feedback synthesis, commitments as tickets, risk signals |
| `tech-discussion` | `/meeting-digest transcript.txt tech-discussion` | ADRs, action items, decisions log update |
| `strategy` | `/meeting-digest transcript.txt strategy` | Strategy doc, OKRs, prioritized initiatives |

Meeting type is inferred automatically if you don't specify it.

---

## Multiple teams

Add one block per team in `config/teams.yaml`. Claude identifies the right team from the transcript content (via `aliases`) and routes all artifacts to the correct Jira project, GitHub repo, and Slack channels automatically. Pass `--team="Payments"` to be explicit:

```
/meeting-digest transcript.txt --team="Payments"
```

---

## Folder mode (transcript + whiteboard photos)

If your meeting folder contains both a transcript and whiteboard photos, point at the folder:

```
/meeting-digest ~/meetings/2026-05-02-arch-review/
```

Claude reads the transcript and analyzes any `.png` / `.jpg` / `.webp` images for decisions, diagrams, and action items that supplement what was said.

---

## Error handling

If any publish step fails (Jira API down, Slack token expired, etc.), Claude reports the error and offers:

```
Retry / Save draft locally / Skip
```

Saved drafts land in `.meeting-digest-drafts/` (gitignored) so you can re-publish later without re-running the full digest.

---

## Directory structure

```
ai-native-org-skills/
├── .claude/
│   └── commands/
│       ├── RESOLVER.md          # /RESOLVER — entry point
│       └── meeting-digest.md    # /meeting-digest — core processor
├── config/
│   ├── teams.example.yaml       # Copy this to teams.yaml
│   └── teams.yaml               # Your config (gitignored)
├── prompts/
│   ├── standup.md
│   ├── planning.md
│   ├── customer-session.md
│   ├── tech-discussion.md
│   └── strategy.md
├── docs/                        # Team brain (committed to GitHub)
│   ├── team/                    # One page per team member
│   ├── services/                # One page per service or integration
│   └── customers/               # One page per named customer
├── samples/
│   └── standup-2026-05-02.txt   # Try this first
└── CLAUDE.md                    # Full reference docs
```

---

## Replacing the sample screenshots

The screenshots in `docs/screenshots/` are SVG mockups. To replace them with real captures:

1. Take a screenshot of the relevant step in Claude Code
2. Save it to `docs/screenshots/` with the `.svg` filename shown in the `![...]` image tags above (or change the extension to `.png` and update the README link)
3. Commit and push

| Filename | What to capture |
|---|---|
| `01-clone-and-open.svg` | Terminal showing `git clone` + `claude` opening |
| `02-teams-config.svg` | `teams.yaml` open with real values filled in |
| `03-resolver-command.svg` | Claude Code terminal with `/RESOLVER samples/standup-2026-05-02.txt` typed |
| `04-review-panel.svg` | The full review panel output in Claude Code |
| `05-brain-query.svg` | A `/RESOLVER` brain query and Claude's answer |
