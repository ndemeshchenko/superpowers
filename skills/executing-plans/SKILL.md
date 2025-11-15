---
name: executing-plans
description: Use when partner provides a complete implementation plan to execute in controlled batches with review checkpoints - loads plan, reviews critically, executes tasks in batches, reports for review between batches
---

# Executing Plans

## Overview

Load plan, review critically, execute tasks in batches, report for review between batches.

**Core principle:** Batch execution with checkpoints for architect review.

**Announce at start:** "I'm using the executing-plans skill to implement this plan."

## The Process

### Step 1: Load and Review Plan
1. Read plan file
2. **Get gpt-5 plan critique:**
   - Use `mcp__zen__chat` with model "gpt-5"
   - Prompt: "Review this implementation plan critically before execution. Check for: completeness, clarity, missing dependencies, potential risks, edge cases not covered."
   - Include plan file in `files` parameter
   - Use `continuation_id: "plan-review-YYYY-MM-DD-<feature-name>"`
   - Set `use_websearch: true` for best practices
3. Review critically - consider gpt-5 feedback plus your own analysis
4. If concerns (from gpt-5 or your review): Raise them with your human partner before starting
5. If no concerns: Create TodoWrite and proceed

### Step 2: Execute Batch
**Default: First 3 tasks**

For each task:
1. Mark as in_progress
2. Follow each step exactly (plan has bite-sized steps)
3. Run verifications as specified
4. Mark as completed

### Step 2.5: Determine Review Depth
After batch execution, assess complexity to set review type:

**Use "full" review when:**
- 100+ lines changed across batch
- 5+ files modified
- New architectural patterns introduced
- Security-sensitive code (auth, validation, data handling)
- Complex business logic

**Use "quick" review when:**
- <100 lines changed
- <5 files modified
- Simple CRUD operations
- Routine refactoring
- Documentation updates

### Step 3: Report
When batch complete:

1. **Get gpt-5 batch review:**
   - Identify changed files: `git diff --name-only <batch-start> HEAD`
   - Use `mcp__zen__codereview` with model "gpt-5"
   - Parameters:
     - `step`: "Review batch N (Tasks X-Y). Plan requirements: [extract relevant task descriptions from plan]. Focus on: requirements alignment, test coverage, error handling"
     - `step_number: 1`, `total_steps: 1`, `next_step_required: false`
     - `findings: ""`
     - `relevant_files`: [absolute paths from git diff]
     - `review_type`: "full" or "quick" (from Step 2.5)
     - `focus_on`: "alignment with plan requirements, test coverage"
     - `use_websearch: true`

2. **Process gpt-5 findings by severity:**
   - **Critical/High**: Auto-fix immediately before reporting to human
   - **Medium/Low**: Include in report for human decision

3. **Present enhanced report:**
   ```
   ## Batch N Complete (Tasks X-Y)

   **Implementation:**
   - [What was implemented]

   **Verification:**
   - [Test results, build output]

   **GPT-5 Review:**
   - Critical issues: [None / Fixed: description]
   - High severity: [None / Fixed: description]
   - Medium severity: [list or "None"]
   - Low severity: [list or "None"]

   Ready for your feedback.
   ```

### Step 4: Continue
Based on feedback:
- Apply changes if needed
- Execute next batch
- Repeat until complete

### Step 5: Complete Development

After all tasks complete and verified:
- Announce: "I'm using the finishing-a-development-branch skill to complete this work."
- **REQUIRED SUB-SKILL:** Use superpowers:finishing-a-development-branch
- Follow that skill to verify tests, present options, execute choice

## GPT-5 Review Integration

This skill uses gpt-5 (via Zen MCP tools) for expert validation at two checkpoints:

### Plan Review (Step 1)
- **Tool:** `mcp__zen__chat`
- **Purpose:** Validate plan completeness, clarity, identify risks before execution
- **When:** Before creating TodoWrite and starting execution
- **Action:** Raise concerns to human if found

### Batch Review (Step 3)
- **Tool:** `mcp__zen__codereview`
- **Purpose:** Validate implementation against plan requirements, identify issues
- **When:** After each batch (3 tasks) completes
- **Review depth:** Adaptive - "full" for complex changes, "quick" for simple

### Severity-Based Auto-Fix
gpt-5 identifies issues with severity levels. Handle as follows:

| Severity | Action | Rationale |
|----------|--------|-----------|
| **Critical** | Auto-fix immediately | Blocks functionality, security risks |
| **High** | Auto-fix immediately | Significant quality/correctness issues |
| **Medium** | Report to human | Important but not blocking |
| **Low** | Report to human | Nice to have, cleanup items |

**Auto-fix workflow:**
1. Apply fix for critical/high issues
2. Re-run verifications
3. Report what was fixed in batch summary
4. If fix fails: Stop and ask for help

**Human decision workflow:**
- Present medium/low findings in batch report
- Human decides: fix now, fix later, or acceptable as-is
- Proceed based on human feedback

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
- Use gpt-5 to review plan before starting (Step 1)
- Use gpt-5 to review each batch before reporting (Step 3)
- Auto-fix critical/high issues, report medium/low to human
- Adaptive review depth: "full" for complex, "quick" for simple
- Review plan critically first (with gpt-5 feedback)
- Follow plan steps exactly
- Don't skip verifications
- Reference skills when plan says to
- Between batches: just report and wait
- Stop when blocked, don't guess
