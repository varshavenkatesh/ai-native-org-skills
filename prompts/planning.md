# Planning — Extraction & Artifact Spec

Sprint planning, backlog grooming, or quarterly planning sessions.
Extract only what is explicitly stated. Do not infer scope or estimates not mentioned.

---

## Extract: Sprint or planning scope

- `sprint_name` — sprint name or number if mentioned
- `sprint_dates` — start and end dates if mentioned
- `goal` — sprint goal as stated by the team (quote directly if possible)
- `team_capacity` — any capacity notes (people out, reduced sprint, etc.)

## Extract: Work items committed

For each item committed to the sprint or backlog:
- `title` — work item title
- `type` — `story | task | bug | spike | tech-debt`
- `assignee` — if assigned during planning
- `points` — story points if estimated
- `priority` — `must-have | should-have | nice-to-have` for this sprint
- `dependencies` — any blockers or dependencies on other teams mentioned
- `acceptance_criteria` — if stated explicitly

## Extract: Items deferred or descoped

For each item explicitly deferred or cut from scope:
- `title` — item title
- `reason` — why it was deferred
- `target` — next sprint or backlog

## Extract: Risks and open questions

- `risk` — the concern raised
- `owner` — who raised it or who will address it
- `mitigation` — any mitigation discussed

## Extract: Dependencies on other teams

- `depends_on_team` — which team
- `what_is_needed` — what is needed from them
- `by_when` — deadline if mentioned
- `contact` — person to coordinate with

---

## Artifact output spec

### Jira tickets — one per committed work item

```
Summary  : {title}
Type     : Story | Task | Bug | Spike | Tech Debt
Priority : Highest | High | Medium | Low
Assignee : {assignee, or unassigned}
Story pts: {points, or leave blank}
Labels   : sprint-{sprint_name}, planning-{YYYY-MM-DD}
Description:
  **Sprint goal context:** {sprint goal}

  **Acceptance criteria:**
  {acceptance_criteria, or "TBD"}

  **Dependencies:** {dependencies, or "none"}
```

Also create one parent Epic ticket if none exists for the sprint goal, with all committed items linked as children.

### GitHub — sprint prep report (Hub)

Create or update `hub/sprints/{sprint_name}.md`:

```markdown
# Sprint {sprint_name} — Prep Report

**Dates:** {start} → {end}
**Goal:** {sprint_goal}
**Capacity notes:** {capacity, or "Full team"}

## Committed work

| Item | Type | Assignee | Points | Priority |
|------|------|----------|--------|----------|
{row per item}

## Deferred / descoped

| Item | Reason | Target |
|------|--------|--------|
{row per item}

## Risks

| Risk | Owner | Mitigation |
|------|-------|-----------|
{row per item}

## Cross-team dependencies

| Depends on | What | By when | Contact |
|-----------|------|---------|---------|
{row per item}

---
*Generated from planning session — {YYYY-MM-DD}*
```

### Hub HTML — Sprint Prep page

Create `hub/sprints/{sprint-name-slug}.html` on `hub_branch` using `hub/templates/sprint-prep.html`.

Replace template variables:
- `{{team_name}}` → team name from config
- `{{sprint_name}}` → sprint name or number
- `{{sprint_start}}`, `{{sprint_end}}` → sprint dates, or `"TBD"` if not stated
- `{{planning_date}}` → meeting date

Populate insertion slots with real HTML:

`<!-- HUB:INSERT:goal-blocks -->` — one block per sprint goal:
```html
<div class="goal-block">{goal text}</div>
```

`<!-- HUB:INSERT:ticket-rows -->` — one row per committed work item (use the example format in the template comment, filling in ticket key, summary, description, priority badge, type badge, and assignee).

`<!-- HUB:INSERT:capacity-cards -->` — one card per person with point/day estimates if mentioned:
```html
<div class="capacity-card">
  <div class="name">{name}</div>
  <div class="points">{points or days}</div>
  <div class="label">story points</div>
</div>
```

`<!-- HUB:INSERT:risk-items -->` — one item per risk:
```html
<div class="risk-item"><strong>{risk title}:</strong> {mitigation or "no mitigation discussed"}</div>
```

After creating the sprint page, update `hub/index.html` on `hub_branch`: prepend a link to the new sprint page in the sprint links section.

### Slack FYI

Post to `{slack.channel}`:

```
*Sprint {sprint_name} Planning — Done* 🗓️

*Goal:* {sprint_goal}
*Committed:* {N} items ({total_points} pts)

*Top priorities*
• {must-have items, up to 5}

*Risks to watch*
• {risks, if any}

*Cross-team asks*
• {dependencies, if any}

Full plan: {link to GitHub sprint prep report}
```
