---
name: skill_agency
description: Meta-skill to author, validate, scaffold, and register new skills
---

## agency protocol

when the user requests a new skill or capability:

### 1. request parsing & validation
- **extract intent:** skill name (must match `^[a-z_]{3,32}$`), purpose (extract from user utterance)
- **validate namespace:** check if `./.claude/skills/<skill_name>/` exists
  - if exists: HALT and present user with (a) skip, (b) merge, (c) backup+overwrite. do not proceed without explicit choice.
  - if not exists AND you can't write to `./.claude/`: attempt `./.skills/` as fallback, then report failure with reason
- **check dependencies:** if the skill will invoke external scripts/tools, verify they're accessible NOW. don't assume.

### 2. architecture decision
decide INDEPENDENTLY whether this skill should be:
- **self-contained:** single SKILL.md file, no external dependencies → go to scaffolding path A
- **composite:** requires helper scripts or data files → go to scaffolding path B
- **infeasible with current constraints:** report to user with specific blockers

### 3. scaffolding path A (self-contained)
```
./.claude/skills/<skill_name>/
├── SKILL.md
```

SKILL.md must include:
- yaml frontmatter: `name`, `description`, `version: 1.0`
- **scope statement:** explicit boundary of what this skill does/doesn't do
- **decision trees:** if-then logic for common cases (malformed input, missing context, permission errors)
- **fallback behavior:** what the agent does when it can't complete the primary task
- **examples:** 3+ concrete use cases with expected outcomes
- **validation checklist:** steps the agent takes before claiming success

### 4. scaffolding path B (composite)
```
./.claude/skills/<skill_name>/
├── SKILL.md
├── scripts/
│   ├── <helper_script>.py
│   └── <helper_script>.sh
├── data/
│   └── <data_file>.json
└── README.md (describes interdependencies)
```
