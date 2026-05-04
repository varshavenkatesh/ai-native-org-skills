# Team Intelligence Hub

Turn any meeting transcript into structured team artifacts — Jira tickets, GitHub docs, Slack FYIs, escalation PRs, and a living HTML hub — with human review before anything is published.

## What this is

A set of Claude Code slash commands for engineering teams. Scrum masters (or anyone on the team) run a command after a meeting, review the generated artifacts, approve or edit them, and publish in one step.

**Supported meeting types:**
- `standup` — action items, blockers, decisions, prod status, customer feedback, escalations
- `planning` — sprint backlog, Jira stories, sprint prep report in the Hub
- `customer-session` — feedback synthesis, commitments as Jira tickets, risk signals
- `tech-discussion` — ADRs, action items, decisions log update
- `strategy` — strategy doc, OKRs, prioritized initiatives, decisions log

---

## Prerequisites

1. **Claude Code** — [install guide](https://docs.anthropic.com/claude-code)
2. **GitHub CLI** — `brew install gh` then `gh auth login`
3. **Jira API token** — generate at https://id.atlassian.com/manage-profile/security/api-tokens
4. **Slack bot token** — create a bot at https://api.slack.com/apps with `chat:write` scope, install to your workspace

---

## Setup

### 1. Clone the repo

```bash
git clone https://github.com/your-org/team-intelligence-hub
cd team-intelligence-hub
```

### 2. Configure your teams

```bash
cp config/teams.example.yaml config/teams.yaml
```

Open `config/teams.yaml` and fill in your team's Jira project, GitHub repo, Slack channels, and escalation reviewers. Add one block per scrum team.

### 3. Set environment variables

Add to your shell profile (`.zshrc`, `.bashrc`) or a `.env` file at the repo root (it's gitignored):

```bash
export JIRA_API_TOKEN="your-jira-api-token"
export JIRA_EMAIL="you@yourorg.com"
export SLACK_BOT_TOKEN="xoxb-your-slack-bot-token"
# GITHUB_TOKEN is optional if you've already run `gh auth login`
export GITHUB_TOKEN="your-github-pat"
```

### 4. Open Claude Code in this directory

```bash
claude
```

The slash commands are now available.

---

## Usage

### `/RESOLVER` — start here

```
/RESOLVER <anything>
```

The resolver is the single entry point for all team intelligence requests. Give it a transcript path, a question about your team's brain, or anything else — it routes to the right skill automatically.

```bash
# Process a transcript (routes to /meeting-digest)
/RESOLVER ~/meetings/2026-05-02-standup.txt

# Query the brain
/RESOLVER what is Maya working on this sprint?
/RESOLVER what did we decide about Stripe SDK?
/RESOLVER what has Acme Corp reported recently?
```

---

### `/meeting-digest`

```
/meeting-digest <path> [type] [--team=<name>]
```

**Examples:**

```bash
# Single transcript file, infer meeting type and team
/meeting-digest ~/meetings/2024-01-15-standup.txt

# Folder with transcript + whiteboard images
/meeting-digest ~/meetings/2024-01-15-arch-review/

# Explicit meeting type
/meeting-digest ~/meetings/transcript.txt tech-discussion

# Explicit team (use when team can't be inferred from transcript)
/meeting-digest ~/meetings/transcript.txt planning --team="Payments"
```

**What happens:**
1. Claude reads the transcript (and analyzes any whiteboard images in the folder)
2. Infers meeting type and team if not provided
3. Extracts structured content based on meeting type
4. Shows a full review panel with all generated artifacts
5. You approve all, review individually, or cancel
6. Approved artifacts are published: Jira tickets, GitHub commits/PRs, Slack messages, Hub updates

---

## Artifact routing by meeting type

| Artifact | Standup | Planning | Customer Session | Tech Discussion | Strategy |
|----------|---------|----------|-----------------|----------------|---------|
| Jira tickets | Action items, blockers | Stories, tasks | Commitments, bugs | Action items | Next-step tasks only |
| GitHub — decisions | Yes | — | — | ADR per decision | Yes |
| GitHub — production log | Yes | — | — | — | — |
| GitHub — customer feedback | Yes | — | Yes | — | — |
| GitHub — strategy | — | — | — | — | Yes |
| GitHub PR — escalation | Yes | — | Risk signals | Needs-approval | Needs-exec-approval |
| Slack FYI | Yes | Yes | Yes | Yes | Yes |
| Entity pages (team/services/customers) | Yes | Yes | Yes | Yes | — |
| Hub — sprint prep | — | Yes | — | — | — |
| Hub — decisions | — | — | — | Yes | Yes |
| Hub — customer feedback | — | — | Yes | — | — |
| Hub — strategy | — | — | — | — | Yes |

---

## Directory structure

```
team-intelligence-hub/
├── CLAUDE.md                        # This file
├── .claude/
│   └── commands/
│       ├── RESOLVER.md              # /RESOLVER — entry point, routes all requests
│       └── meeting-digest.md        # /meeting-digest slash command
├── config/
│   ├── teams.example.yaml           # Config template — copy to teams.yaml
│   └── teams.yaml                   # Your config (gitignored)
├── prompts/
│   ├── standup.md                   # Extraction rules for standup meetings
│   ├── planning.md                  # Extraction rules for planning sessions
│   ├── customer-session.md          # Extraction rules for customer sessions
│   ├── tech-discussion.md           # Extraction rules for tech discussions
│   └── strategy.md                  # Extraction rules for strategy sessions
├── docs/                            # Team brain — committed to GitHub
│   ├── team/                        # One page per team member (db_tracked)
│   │   └── _template.md
│   ├── services/                    # One page per internal service or integration (db_tracked)
│   │   └── _template.md
│   ├── customers/                   # One page per named customer (db_tracked)
│   │   └── _template.md
│   ├── decisions/                   # Decisions log
│   ├── production-log/              # Experiment and production status log
│   ├── customer-feedback/           # Customer feedback log
│   ├── escalations/                 # Escalation documents
│   └── strategy/                    # Strategy docs and OKRs
├── hub/
│   └── templates/                   # HTML templates for Team Intelligence Hub
└── .gitignore
```

The `docs/team/`, `docs/services/`, and `docs/customers/` directories are the **team brain** — populated automatically by `/meeting-digest` after each meeting and queryable via `/RESOLVER`.

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). To add a new meeting type, add a prompt template in `prompts/` and update the routing table in `.claude/commands/meeting-digest.md`.
