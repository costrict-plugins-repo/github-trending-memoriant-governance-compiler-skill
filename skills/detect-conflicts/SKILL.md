# detect-conflicts

Scan a compiled governance graph for policy conflicts: contradictory rules, duplicate mandates, and logical inconsistencies between sections.

## Trigger

User says something like:
- `/detect-conflicts`
- "find conflicts in my policy"
- "check for contradictions in my governance graph"
- "are there any conflicting rules?"

## What This Skill Does

Reads a compiled governance graph and uses semantic analysis to identify:
1. **Direct contradictions** — rules that explicitly negate each other (e.g., "allow X" vs. "prohibit X")
2. **Duplicate mandates** — rules that say the same thing in different sections
3. **Precedence ambiguities** — rules with no clear resolution order when they conflict
4. **Scope overlaps** — multiple sections claiming authority over the same domain

## Step-by-Step Instructions

### Step 1: Load the Graph

Read `governance-graph/graph.json` (or ask the user for a path).

Extract all rule nodes with their labels and parent section context.

### Step 2: Keyword-Based Contradiction Detection

Build a contradiction pattern table. For each rule, check for antonym pairs in the rule text:

| Pattern A | Pattern B | Conflict Type |
|-----------|-----------|---------------|
| "allow" / "permit" / "enable" | "prohibit" / "deny" / "block" | Permission conflict |
| "must" / "required" / "shall" | "must not" / "prohibited" / "never" | Mandate conflict |
| "encrypt" | "plaintext" / "unencrypted" | Security conflict |
| "log all" / "audit everything" | "do not log" / "no logging" | Audit conflict |
| "human approval required" | "automatic" / "no approval" | Authorization conflict |
| "rotate every N days" + "rotate every M days" (N≠M) | — | Frequency conflict |

For each matched pair, record:
- Rule A id and text
- Rule B id and text
- Conflict type
- Parent sections of both rules

### Step 3: Semantic Similarity Analysis

Group rules by semantic theme using keyword clustering:
- Authentication rules: keywords — password, credential, auth, login, token, key
- Encryption rules: keywords — encrypt, TLS, SSL, cipher, plaintext
- Access rules: keywords — access, permission, role, privilege, least privilege
- Audit rules: keywords — log, audit, trail, record, monitor
- Retention rules: keywords — retain, delete, expire, purge, keep

Within each cluster, compare rule pairs for contradictions using the same pattern table.

### Step 4: Duplicate Detection

Rules are duplicates if:
- Their normalized text (lowercase, punctuation stripped) has >80% token overlap
- They appear in different sections (same-section duplicates are noted but lower severity)

### Step 5: Generate Conflict Report

```
Policy Conflict Detection Report
═══════════════════════════════════════════════════════
Graph:       governance-graph/graph.json
Rules scanned: <N>
Conflicts found: <N>

CRITICAL CONFLICTS (require resolution before enforcement):
───────────────────────────────────────────────────────────
[CONFLICT-1] Permission Conflict
  Rule A: "<text>" (section: <Section Name>)
  Rule B: "<text>" (section: <Section Name>)
  Issue: Rule A permits what Rule B prohibits
  Recommendation: Establish which section takes precedence, or add a scope qualifier

[CONFLICT-2] Frequency Conflict
  Rule A: "Rotate credentials every 30 days" (section: Access Control)
  Rule B: "Rotate credentials every 90 days" (section: Security Policy)
  Issue: Incompatible rotation schedules
  Recommendation: Consolidate into one authoritative schedule

WARNINGS (review recommended):
───────────────────────────────────────────────────────────
[WARN-1] Duplicate Mandate
  Rule A: "<text>" (section: <Section A>)
  Rule B: "<text>" (section: <Section B>)
  Issue: Substantially identical rule appears in two sections
  Recommendation: Consolidate or add a cross-reference

INFORMATIONAL:
───────────────────────────────────────────────────────────
  - <N> rules have no detectable conflicts
  - <N> sections have overlapping scope keywords (review manually)

Summary: <N> critical, <N> warnings, <N> info
```

### Step 6: Save Report

Write conflict report to `governance-graph/conflicts.md`.

## Severity Levels

| Level | Meaning |
|-------|---------|
| CRITICAL | Direct contradiction — policy cannot be deterministically enforced |
| WARNING | Ambiguity or duplication — enforcement is inconsistent |
| INFO | Scope overlap — may be intentional |

## Limitations

This skill uses pattern matching and keyword analysis, not full semantic NLU. Flag ambiguous cases for human review. Always recommend legal/compliance review before treating output as authoritative.
