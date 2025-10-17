---
name: Executing Plans
description: Execute detailed plans in batches with review checkpoints
when_to_use: when partner provides a complete implementation plan to execute in controlled batches with review checkpoints
version: 2.2.0
---

# Executing Plans

## Overview

Load plan, review critically, execute tasks in batches, report for review between batches.

**Core principle:** Batch execution with checkpoints for architect review.

**Announce at start:** "I'm using the Executing Plans skill to implement this plan."

## Loading the Plan

**If plan path not provided, prompt user:**

Use `AskUserQuestion` to determine the plan source:

```typescript
AskUserQuestion({
  questions: [{
    question: "Where is the plan you want to execute?",
    header: "Plan source",
    multiSelect: false,
    options: [
      {
        label: "GitHub issue",
        description: "Load plan from a GitHub issue using gh CLI"
      },
      {
        label: "./tmp/plans directory",
        description: "Load plan from a file in ./tmp/plans/"
      }
    ]
  }]
})
```

**If GitHub issue selected:**

1. Ask user for the issue number: "Which GitHub issue number contains the plan?"
2. Load the plan:
   ```bash
   gh issue view <issue-number> --json body --jq .body > /tmp/plan.md
   ```
3. Read `/tmp/plan.md` to load the plan

**If ./tmp/plans directory selected:**

1. List available plans:
   ```bash
   ls -1t ./tmp/plans/*.md | head -5  # Show 5 most recent
   ```
2. Ask user for the filename: "Which plan file do you want to execute?"
3. Read `./tmp/plans/<filename>.md` to load the plan

**Error handling:**

- **If `gh` command not available:** Inform user that GitHub CLI is not installed and ask them to either install it (`gh` CLI) or provide the plan via `./tmp/plans/` directory instead
- **If `./tmp/plans/` directory doesn't exist:** Inform user the directory doesn't exist and ask them to either create it and place the plan file there, or provide the plan via GitHub issue instead
- **If no plan files found in `./tmp/plans/`:** Inform user no plan files were found and ask them to place a `.md` file in that directory or provide the plan via GitHub issue instead
- **If specified GitHub issue or file doesn't exist:** Inform user the issue/file wasn't found and ask them to verify the issue number or filename

## The Process

### Step 1: Load and Review Plan
1. Read plan file
2. Review critically - identify any questions or concerns about the plan
3. If concerns: Raise them with your human partner before starting
4. If no concerns: Create TodoWrite and proceed

### Step 2: Execute Batch
**Default: First 3 tasks**

For each task:
1. Mark as in_progress
2. Follow each step exactly (plan has bite-sized steps)
3. Run verifications as specified
4. Mark as completed

### Step 3: Report
When batch complete:
- Show what was implemented
- Show verification output
- Say: "Ready for feedback."

### Step 4: Continue
Based on feedback:
- Apply changes if needed
- Execute next batch
- Repeat until complete

### Step 5: Complete Development

After all tasks complete and verified:
- Announce: "I'm using the Finishing a Development Branch skill to complete this work."
- Switch to skills/collaboration/finishing-a-development-branch
- Follow that skill to verify tests, present options, execute choice

## When to Stop and Ask for Help

**STOP executing immediately when:**
- Hit a blocker mid-batch (missing dependency, test fails, instruction unclear)
- Plan has critical gaps preventing starting
- You don't understand an instruction
- Verification fails repeatedly

**Ask for clarification rather than guessing.**

## When to Revisit Earlier Steps

**Return to Review (Step 1) when:**
- Partner updates the plan based on your feedback
- Fundamental approach needs rethinking

**Don't force through blockers** - stop and ask.

## Remember
- Review plan critically first
- Follow plan steps exactly
- Don't skip verifications
- Reference skills when plan says to
- Between batches: just report and wait
- Stop when blocked, don't guess
