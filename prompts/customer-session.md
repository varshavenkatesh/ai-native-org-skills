# Customer Session — Extraction & Artifact Spec

Customer interviews, feedback sessions, user research calls, support escalations, or CSM syncs.
Preserve the customer's voice — quote directly where possible. Anonymize if the customer requested confidentiality.

---

## Extract: Session metadata

- `customer_name` — company or person name (mark as `[CONFIDENTIAL]` if anonymization requested)
- `customer_segment` — enterprise / mid-market / SMB / internal / prospect
- `session_type` — interview | feedback | support escalation | QBR | demo | onboarding
- `attendees` — who was on the call (our team members)
- `date` — session date

## Extract: Pain points

For each problem or frustration the customer expressed:
- `pain` — description of the issue in the customer's own words (direct quote if available)
- `severity` — `critical | high | normal | low` (infer from customer's tone/language)
- `frequency` — how often they encounter this (if stated)
- `workaround` — any workaround they currently use

## Extract: Feature requests

For each feature, improvement, or capability the customer asked for:
- `request` — what they want, one sentence
- `use_case` — why they need it, their context
- `priority_signal` — their language around urgency ("blocking us", "would be nice", "critical for renewal", etc.)

## Extract: Bugs and reliability issues

For each bug or reliability problem mentioned:
- `issue` — what broke or behaved unexpectedly
- `steps_to_reproduce` — if described
- `impact` — how it affected the customer

## Extract: Commitments made

Any explicit commitment our team made during the session:
- `commitment` — exactly what was committed to
- `owner` — who made the commitment
- `deadline` — date or timeframe stated
- `caveat` — any conditions or caveats mentioned

## Extract: Positive signals and compliments

Things the customer praised or said were working well:
- `signal` — what they liked
- `context` — the feature or workflow they were praising

## Extract: Churn or risk signals

Any language suggesting dissatisfaction, evaluating alternatives, or renewal risk:
- `signal` — the specific language used (quote directly)
- `severity` — `high-risk | watch | informational`

---

## Artifact output spec

### Jira tickets

**One ticket per commitment made:**
```
Summary  : Committed to {customer_name}: {commitment}
Type     : Task
Priority : Highest (if deadline < 2 weeks) | High | Medium
Assignee : {owner}
Labels   : customer-commitment, {customer_segment}, {YYYY-MM-DD}
Due date : {deadline}
Description:
  **Customer:** {customer_name} ({customer_segment})
  **Committed by:** {owner} on {date}
  **Commitment:** {commitment}
  **Caveat:** {caveat, or "none"}
```

**One ticket per critical or high-severity bug:**
```
Summary  : [Customer] {issue}
Type     : Bug
Priority : Highest (critical) | High
Labels   : customer-reported, {customer_name or segment}
Description:
  **Reported by:** {customer_name} on {date}
  **Impact:** {impact}
  **Steps to reproduce:** {steps, or "see transcript"}
```

### GitHub — customer feedback log

Create `{customer_feedback_path}/{YYYY-MM-DD}-{customer-slug}.md`:

```markdown
# {customer_name} — {session_type} — {date}

**Segment:** {customer_segment}
**Attendees:** {attendees}

## Pain points

{for each pain point}
### {pain — short title}
> "{direct quote if available}"

- **Severity:** {severity}
- **Frequency:** {frequency, or "not stated"}
- **Workaround:** {workaround, or "none"}

## Feature requests

{for each request}
### {request — short title}
- **Use case:** {use_case}
- **Priority signal:** "{priority_signal}"

## Commitments made

| Commitment | Owner | Deadline | Jira |
|-----------|-------|---------|------|
{row per commitment — link to Jira ticket once created}

## Positive signals

{bullet list}

## Risk signals

{bullet list with severity tags}
```

### Hub HTML — Customer Feedback page

Update `hub/customer-feedback.html` on `hub_branch`. If the file doesn't exist, create it from `hub/templates/customer-feedback.html`.

Update header variables: increment `{{customer_count}}` if new customer; set `{{last_updated}}` to today's date.

In `<!-- HUB:INSERT:customer-cards -->`, prepend a new customer card (or update the existing card for this customer if one is already present):

```html
<div class="customer-card">
  <div class="header">
    <div>
      <h3>{customer_name}</h3>
      <div class="since">{customer_segment}{relationship context if known, e.g. "· customer since 2024-11"}</div>
    </div>
    <span class="risk-badge risk-{high|medium|low}">{risk level} risk</span>
  </div>

  <div style="font-size:12px;font-weight:600;color:#8b949e;margin-bottom:8px;text-transform:uppercase;letter-spacing:0.04em">{date} — {session_type}</div>

  {for each pain point, severity critical or high:}
  <div class="feedback-item">
    <div class="date">{date}</div>
    <div class="text">{pain or bug description}</div>
    <div class="ticket"><a href="#">{jira_key if created}</a> · {type} · {priority}{· assignee if known}</div>
  </div>

  {if commitments were made:}
  <div style="font-size:12px;font-weight:600;color:#8b949e;margin:16px 0 8px;text-transform:uppercase;letter-spacing:0.04em">Open commitments</div>
  {for each commitment:}
  <div class="commitment-item">
    <div class="commitment-status open"></div>
    <div>
      <div class="text">{commitment}</div>
      <div class="meta">{jira_key if created} · due: {deadline}</div>
    </div>
  </div>
</div>
```

### Slack FYI

Post to `{slack.channel}`:

```
*Customer session: {customer_name}* — {date}

*Key themes*
• {top 2-3 pain points or requests}

*Commitments made*
• {owner}: {commitment} by {deadline}
...

*Risk signals* (if any)
• {signal}

Full notes: {link to GitHub feedback doc}
```

If churn/risk signals were extracted, also post to the escalation channel:
```
:warning: *Renewal risk signal — {customer_name}*
"{direct quote}"
@{em} @{pm} — recommend follow-up
```
