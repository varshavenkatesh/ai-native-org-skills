# Standup — Extraction & Artifact Spec

Extract only what is explicitly stated in the transcript. Do not infer, embellish, or fill in gaps.

---

## Extract: Action items and new work

For each action item or new work item mentioned:
- `owner` — person responsible (first name or handle is fine)
- `description` — what needs to be done, in one clear sentence
- `due` — due date or sprint target, if mentioned (leave blank if not)
- `priority` — `blocker | high | normal` (default `normal` unless indicated)
- `linked_ticket` — Jira key or PR number if referenced (e.g. PLAT-42)

## Extract: Blockers

For each blocker raised:
- `blocked_person` — who is blocked
- `blocked_on` — what they're blocked on
- `help_needed` — what unblocks them
- `unblocked_by` — who can resolve it (if mentioned)

## Extract: Tech or product decisions

Any decision made or confirmed — even informally. Include:
- `decision` — what was decided, one sentence
- `rationale` — why, if given
- `owner` — who made or confirmed it
- `area` — which service, feature, or team it affects

## Extract: Production and experiment status

Any mention of things that shipped, rolled back, ramped, or changed:
- `name` — feature or experiment name
- `status` — `shipped | rolled back | ramping | paused | monitoring`
- `rollout_pct` — percentage if mentioned
- `signals` — any metric, alert, or user signal mentioned

## Extract: Customer feedback

Any customer feedback, bug report, or user signal discussed:
- `source` — customer name or segment if mentioned; otherwise `"customer"` or `"user report"`
- `summary` — what the feedback was
- `severity` — `critical | high | normal` (default `normal` unless indicated)

## Extract: Escalations

Any item that needs EM, PM, or tech lead attention or decision:
- `issue` — what the escalation is about
- `why` — why it needs escalation (blocked decision, risk, dependency, etc.)
- `urgency` — `today | this sprint | informational`
- `reviewer` — `em | pm | tech_lead` (or multiple)

---

## Artifact output spec

### Jira tickets — one per action item, one per blocker

```
Summary  : {description} (action items) OR Blocker: {blocked_on} (blockers)
Type     : Task (action item) | Bug (blocker)
Priority : Highest (blocker) | High | Medium | Low
Assignee : {owner}
Labels   : standup-{YYYY-MM-DD}, blocker (if applicable)
Description:
  **Context from standup ({date}):**
  {1-2 sentences of context from transcript}

  **Definition of done:** {if stated}
```

Only create a ticket if no `linked_ticket` was referenced. If a linked ticket exists, note it in the review panel but skip creation.

### GitHub — decisions log

If any decisions were extracted, append to `{decisions_path}/decisions.md`:

```markdown
## {YYYY-MM-DD} — {decision title}

**Decision:** {what was decided}
**Rationale:** {why, or "not stated"}
**Owner:** {owner}
**Area:** {affected service or feature}
**Source:** standup
```

### GitHub — production log

If any production/experiment status was extracted, append to `{production_log_path}/log.md`:

```markdown
## {YYYY-MM-DD} — {name}

**Status:** {status}
**Rollout:** {rollout_pct, or "N/A"}
**Signals:** {signals, or "none mentioned"}
**Source:** standup
```

### GitHub — customer feedback

If any customer feedback was extracted, create `{customer_feedback_path}/{YYYY-MM-DD}-standup.md` (or append if file exists for today):

```markdown
## {source} — {YYYY-MM-DD}

{summary}

**Severity:** {severity}
**Source:** standup
```

### GitHub PR — escalation

For each escalation, create a PR:
- **Branch:** `escalation/standup-{YYYY-MM-DD}-{slug}`
- **Title:** `[ESCALATION] {issue} — {YYYY-MM-DD}`
- **Reviewers:** per `escalation_reviewers` in teams.yaml, matching `reviewer` field
- **Labels:** `escalation`, `standup`
- **Body:**
  ```markdown
  ## Escalation — {issue}

  **Raised in:** standup {YYYY-MM-DD}
  **Urgency:** {urgency}

  ### Context
  {why this needs escalation}

  ### Requested action
  {what the reviewer needs to decide or unblock}
  ```

### Slack FYI

Post to `{slack.channel}`. Keep it scannable — use bullets, no walls of text:

```
*{Team} Standup — {date}*

*Action items*
• {owner}: {description}
...

*Blockers* (if any)
• {blocked_person} blocked on {blocked_on} — {unblocked_by} to assist

*Shipped / Status updates* (if any)
• {name}: {status}

*Escalations* (if any)
• {issue} → PR raised, {reviewer} to review
```

If no items in a section, omit that section entirely.

### Hub update — Sprint Activity

No hub update for standup. Standup content rolls up into sprint-level hub pages (planning and retrospective).
