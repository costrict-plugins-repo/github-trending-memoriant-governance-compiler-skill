# trace-compliance

Trace a compliance path through a governance graph: given a scenario or action, identify which rules apply, whether the action is permitted, and what conditions must be met.

## Trigger

User says something like:
- `/trace-compliance`
- "trace this action through my policy"
- "is this allowed under my governance rules?"
- "what rules apply to <scenario>?"
- "compliance check for <action>"

## What This Skill Does

Takes a described action or scenario and walks the governance graph to:
1. Identify all rules that apply to the action
2. Determine the compliance verdict (PERMITTED, PROHIBITED, CONDITIONAL, AMBIGUOUS)
3. List required conditions for conditional permission
4. Generate a traceable evidence path (which rules were evaluated and how)

## Step-by-Step Instructions

### Step 1: Get the Scenario

Ask the user: "Describe the action or scenario to evaluate. For example: 'A service account reads production customer data without logging' or 'Agent spawns a sub-agent to make a financial transaction.'"

### Step 2: Load the Graph

Read `governance-graph/graph.json`. If the file doesn't exist, ask for the path or suggest running `/compile-policy` first.

### Step 3: Extract Keywords from Scenario

Parse the scenario description for:
- **Subject**: who or what is doing the action (agent, user, service account, etc.)
- **Verb**: the action being taken (read, write, delete, execute, spawn, access, etc.)
- **Object**: what is being acted upon (data, file, API, credential, sub-agent, etc.)
- **Context modifiers**: without logging, in production, with approval, automatically, etc.

### Step 4: Score Rules Against the Scenario

For each rule node in the graph, compute a relevance score:
- +3 if the rule's subject keyword matches the scenario subject
- +3 if the rule's verb keyword matches the scenario verb
- +3 if the rule's object keyword matches the scenario object
- +2 if a context modifier matches
- +1 for any partial keyword overlap

Include rules with score >= 2 in the trace.

### Step 5: Evaluate Applicable Rules

For each included rule, classify it as:
- **PERMITS**: rule explicitly allows the action ("may", "can", "is permitted", "allowed")
- **PROHIBITS**: rule explicitly forbids the action ("must not", "prohibited", "never", "deny")
- **REQUIRES**: rule mandates a condition that must be met ("must", "required", "shall")
- **AMBIGUOUS**: rule is relevant but verdict is unclear

### Step 6: Determine Overall Verdict

| Condition | Verdict |
|-----------|---------|
| Any PROHIBITS rule with no override | PROHIBITED |
| Only PERMITS rules | PERMITTED |
| PERMITS + REQUIRES conditions | CONDITIONAL |
| No applicable rules found | AMBIGUOUS — no policy coverage |
| Conflicting PERMITS and PROHIBITS | AMBIGUOUS — escalate |

### Step 7: Generate Compliance Trace Report

```
Compliance Trace Report
═══════════════════════════════════════════════════════
Scenario:    "<user-provided scenario>"
Graph:       governance-graph/graph.json
Verdict:     CONDITIONAL ⚠

Applicable Rules:
──────────────────────────────────────────────────────
[REQUIRES] rule-12: "Log access attempts" (Section: Data Handling)
  → This action requires: access attempt must be logged
[REQUIRES] rule-34: "Level 3: Irreversible actions require human supervisor approval"
  → This action requires: human supervisor approval before proceeding
[PERMITS]  rule-9:  "Level 1: Read-only operations allowed without approval" (Section: Permission Levels)
  → Permitted if action is read-only

Conditions for PERMITTED status:
  1. Access attempt is logged to audit trail
  2. If action is irreversible: obtain human supervisor approval

Rules Evaluated (not applicable):
  - 34 rules scored below threshold (not shown)

Evidence Path:
  scenario → [data + read + production] →
    section: Data Handling → rule-12 (REQUIRES logging)
    section: Permission Levels → rule-9 (PERMITS read), rule-11 (REQUIRES approval for irreversible)

Recommendation:
  Confirm whether the action is reversible. If reversible:
    - Ensure logging is active → action is PERMITTED
  If irreversible:
    - Obtain human supervisor approval + ensure logging → action is PERMITTED
```

### Step 8: Save Trace

Write to `governance-graph/traces/trace-<timestamp>.md`.

## Output Files

- `governance-graph/traces/trace-<ISO-timestamp>.md`
