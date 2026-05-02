# Tech Discussion — Extraction & Artifact Spec

Architecture reviews, design discussions, RFC walkthroughs, incident postmortems, tech debt prioritization, or any meeting where technical decisions are made.
Precision matters here — capture decisions exactly as stated, not paraphrased.

---

## Extract: Discussion context

- `topic` — what the discussion was about (one sentence)
- `driver` — what prompted this discussion (upcoming feature, incident, scaling concern, etc.)
- `attendees` — who participated
- `date` — meeting date

## Extract: Options considered

For each option or approach that was presented or discussed:
- `option` — brief label for the option
- `description` — what this approach entails
- `pros` — advantages raised
- `cons` — concerns or drawbacks raised
- `champion` — who proposed or advocated for it (if stated)

## Extract: Decisions made

For each decision reached (even tentative ones — mark them):
- `decision` — exactly what was decided, one sentence
- `rationale` — the reason given
- `alternatives_rejected` — which options were not chosen and why
- `owner` — who is responsible for implementing or following through
- `status` — `decided | tentative | needs-approval`
- `approval_needed_from` — if status is `needs-approval`, who must approve

## Extract: Open questions

Things that were explicitly left unresolved or parked for later:
- `question` — what is unresolved
- `owner` — who will investigate or decide
- `by_when` — deadline if stated

## Extract: Action items

Tasks arising from the discussion:
- `description` — what needs to be done
- `owner` — who owns it
- `due` — due date or sprint target
- `linked_decision` — which decision this supports

## Extract: Risks and constraints identified

- `risk` — the concern
- `severity` — `high | medium | low`
- `mitigation` — if discussed

---

## Artifact output spec

### Jira tickets — one per action item

```
Summary  : {description}
Type     : Task | Spike | Tech Debt
Priority : High | Medium | Low
Assignee : {owner}
Labels   : tech-discussion, {YYYY-MM-DD}
Description:
  **Context:** {topic} discussion on {date}
  **Why this task exists:** {linked_decision or rationale}
  **Due:** {due, or "this sprint"}
```

For decisions with `status: needs-approval`, create one additional ticket:
```
Summary  : [APPROVAL NEEDED] {decision} — {owner}
Type     : Task
Priority : High
Assignee : {approval_needed_from}
Labels   : approval-needed, tech-discussion
```

### GitHub — Architecture Decision Record (ADR)

For each decision with `status: decided`, create `{decisions_path}/ADR-{YYYY-MM-DD}-{slug}.md`:

```markdown
# ADR: {decision title}

**Date:** {YYYY-MM-DD}
**Status:** Accepted
**Deciders:** {attendees}
**Area:** {affected service, system, or feature}

## Context

{driver — what problem or situation prompted this decision}

## Options considered

### Option A: {option label}
{description}
- **Pros:** {pros}
- **Cons:** {cons}

{repeat for each option}

## Decision

{decision — exact statement}

**Rationale:** {rationale}

**Rejected alternatives:** {alternatives_rejected}

## Consequences

**Positive:** {what this enables or improves}
**Negative / tradeoffs:** {what we give up or accept}
**Risks:** {risks, if any}

## Action items

| Task | Owner | Due |
|------|-------|-----|
{row per action item}

## Open questions

| Question | Owner | By when |
|---------|-------|--------|
{row per open question}
```

For decisions with `status: tentative`, create the same file but set **Status:** to `Proposed` and add a note at the top:

```markdown
> **Pending approval from:** {approval_needed_from}
```

### Hub update — Decisions log

Append to the team's decisions page in the Hub:

```markdown
## {decision title} — {date}

**Status:** {Accepted | Proposed}
**Area:** {area}
**Owner:** {owner}

{decision — one sentence}

[Full ADR →]({github_link})
```

### Slack FYI

Post to `{slack.channel}`:

```
*Tech discussion: {topic}* — {date}

*Decisions*
• {decision} (owner: {owner})
...

*Needs approval* (if any)
• {decision} — {approval_needed_from} to sign off

*Action items*
• {owner}: {description} by {due}
...

*Open questions*
• {question} — {owner} to investigate

ADR: {link to GitHub}
```
