# Strategy — Extraction & Artifact Spec

Product and engineering strategy discussions — OKR reviews, quarterly planning, roadmap sessions, org design discussions, or any meeting where direction is set at a program or org level.
These artifacts have a longer shelf life than sprint-level artifacts. Write for an audience reading this in 6 months.

---

## Extract: Session context

- `topic` — what was discussed at a high level
- `scope` — team-level | org-level | cross-team
- `horizon` — the timeframe being planned (e.g. Q3, H2, FY26)
- `attendees` — who was present and their roles
- `date` — meeting date

## Extract: Strategic direction

The overall direction or thesis being set:
- `direction` — one to three sentences capturing the strategic bet or shift
- `rationale` — the market, customer, or internal signal driving this direction
- `what_changes` — what will be done differently as a result
- `what_stays` — what is explicitly not changing

## Extract: Goals and OKRs

For each objective or key result discussed:
- `type` — `objective | key_result | north_star_metric`
- `statement` — the goal as stated
- `owner` — team or person responsible
- `timeframe` — Q or year
- `current_baseline` — current value if mentioned
- `target` — target value if mentioned
- `confidence` — team's confidence level if expressed (`high | medium | low`)

## Extract: Initiatives and priorities

For each initiative discussed:
- `initiative` — name or description
- `priority` — `P0 | P1 | P2` or `must-do | should-do | explore`
- `rationale` — why this is prioritized
- `owner` — team or DRI (directly responsible individual)
- `timeline` — when it starts, when it should land
- `dependencies` — other teams or conditions required
- `success_metric` — how success will be measured

## Extract: Items deprioritized or stopped

Things explicitly not being pursued:
- `item` — what was deprioritized
- `reason` — why
- `revisit_when` — conditions under which it would be reconsidered

## Extract: Open strategic questions

Unresolved questions at a strategic level:
- `question` — what is undecided
- `owner` — who will resolve it
- `by_when` — deadline or next checkpoint

## Extract: Decisions made

Firm strategic decisions reached in this meeting:
- `decision` — exactly what was decided
- `rationale` — why
- `owner` — who is accountable
- `status` — `decided | proposed | needs-exec-approval`

---

## Artifact output spec

### Jira tickets

Only create Jira tickets for concrete next-step action items or investigations explicitly called out.
Do not create tickets for goals, OKRs, or initiatives — those belong in GitHub and the Hub.

```
Summary  : {action item description}
Type     : Task | Spike
Priority : High | Medium
Assignee : {owner}
Labels   : strategy-{YYYY-MM-DD}, {horizon}
Description:
  **Strategic context:** {which initiative or goal this supports}
  **Why now:** {rationale}
```

### GitHub — Strategy document

Create `{strategy_path}/{horizon}-{slug}.md`:

```markdown
# {topic} — {horizon} Strategy

**Date:** {YYYY-MM-DD}
**Scope:** {scope}
**Participants:** {attendees with roles}

## Strategic direction

{direction}

**What's driving this:** {rationale}

**What changes:** {what_changes}

**What stays:** {what_stays}

## Goals

| Type | Statement | Owner | Timeframe | Target | Confidence |
|------|-----------|-------|-----------|--------|-----------|
{row per goal/OKR}

## Initiatives

### P0 — Must do

{for each P0 initiative}
#### {initiative name}
- **Why:** {rationale}
- **Owner:** {owner}
- **Timeline:** {timeline}
- **Success metric:** {metric}
- **Dependencies:** {dependencies}

### P1 — Should do

{same structure}

### P2 — Explore

{same structure}

## Not doing (and why)

| Item | Reason | Revisit when |
|------|--------|-------------|
{row per deprioritized item}

## Open questions

| Question | Owner | By when |
|---------|-------|--------|
{row per open question}

## Decisions

| Decision | Rationale | Owner | Status |
|---------|-----------|-------|--------|
{row per decision}

---
*Generated from strategy discussion — {YYYY-MM-DD}*
```

### Hub update — Strategy Summary

Update the team's strategy page in the Hub:

```markdown
## {horizon} Strategy — {date}

**Direction:** {direction — 1-2 sentences}

**Top priorities**
1. {P0 initiatives}
2. ...

**Key goals**
- {top 2-3 OKRs with targets}

**Not doing:** {top 1-2 deprioritized items with reason}

[Full strategy doc →]({github_link})
```

Also append to the decisions log for each decided item:

```markdown
## {decision title} — {date}

**Status:** {Accepted | Proposed}
**Owner:** {owner}
**Horizon:** {timeframe}

{decision — one sentence}

[Full strategy doc →]({github_link})
```

### Slack FYI

Post to `{slack.channel}`:

```
*{horizon} Strategy — aligned* — {date}

*Direction*
{direction — 1-2 sentences}

*Top priorities*
• P0: {initiatives}
• P1: {initiatives}

*Not doing*
• {deprioritized items}

*Open questions to resolve*
• {question} — {owner} by {date}

Full doc: {link to GitHub strategy doc}
```

If any decisions have `status: needs-exec-approval`, also post to the escalation channel:

```
:calendar: *Strategy decision needs approval — {horizon}*
{decision}
@{em} or @{pm} — please review: {github_link}
```
