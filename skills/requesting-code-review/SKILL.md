---
name: requesting-code-review
description: Use when completing tasks, implementing major features, or before merging to verify work meets requirements - uses zen MCP codereview tool with gpt-5.1 to review implementation against plan or requirements before proceeding
---

# Requesting Code Review

Use `mcp__zen__codereview` with gpt-5.1 to catch issues before they cascade.

**Core principle:** Review early, review often.

## When to Request Review

**Mandatory:**

- After each task in subagent-driven development
- After completing major feature
- Before merge to main

**Optional but valuable:**

- When stuck (fresh perspective)
- Before refactoring (baseline check)
- After fixing complex bug

## How to Request

### 1. Identify Changed Files

```bash
# Get list of changed files
git diff --name-only HEAD~1 HEAD

# Or for multiple commits
git diff --name-only <base-sha> HEAD
```

### 2. Call Code Review Tool

Use `mcp__zen__codereview` with these parameters:

**Step 1 - Initial Review:**

```
model: "gpt-5.1"
step: "Review implementation: [what was implemented]. Plan/requirements: [what it should do]. Focus on: code quality, architecture, testing, requirements alignment."
step_number: 1
total_steps: 1
next_step_required: false
findings: ""
relevant_files: [absolute paths to changed files]
review_type: "full"  # or "quick" for minor changes
focus_on: "[specific concerns if any]"
standards: "[coding standards if applicable]"
```

**Tool automatically:**

- Analyzes code changes
- Checks against requirements
- Identifies issues with severity levels (critical/high/medium/low)
- Provides recommendations
- Uses gpt-5.1 for expert validation

### 3. Act on Feedback

The tool returns structured feedback with `issues_found`:

**Severity mapping:**

- **critical** → Fix immediately, blocks everything
- **high** → Fix before proceeding to next task
- **medium** → Fix before merge
- **low** → Note for later cleanup

**Action checklist:**

- [ ] Address all critical issues immediately
- [ ] Fix high-severity issues before next task
- [ ] Document medium/low issues for later
- [ ] Push back if feedback is incorrect (provide reasoning)

## Example Workflow

```
Scenario: Just completed Task 2 - Add verification function

1. Identify changed files:
   $ git diff --name-only HEAD~1 HEAD
   src/verify.ts
   src/repair.ts
   tests/verify.test.ts

2. Call mcp__zen__codereview:
   model: "gpt-5.1"
   step: "Review implementation of verification and repair functions. Requirements: Task 2 from docs/plans/deployment-plan.md - add verifyIndex() and repairIndex() with 4 issue types. Focus on: correctness, test coverage, error handling."
   step_number: 1
   total_steps: 1
   next_step_required: false
   findings: ""
   relevant_files: [
     "/path/to/src/verify.ts",
     "/path/to/src/repair.ts",
     "/path/to/tests/verify.test.ts"
   ]
   review_type: "full"

3. Tool returns:
   issues_found: [
     {severity: "high", description: "Missing progress indicators for long-running operations"},
     {severity: "low", description: "Magic number 100 for reporting interval should be constant"}
   ]

4. Fix high-severity issue (progress indicators)
5. Note low-severity issue for later refactoring
6. Continue to Task 3
```

## Integration with Workflows

**Subagent-Driven Development:**

- Call `mcp__zen__codereview` after EACH task
- Set `review_type: "quick"` for small tasks
- Set `review_type: "full"` for complex tasks
- Fix issues before moving to next task

**Executing Plans:**

- Review after each batch (3 tasks)
- Use `review_type: "full"`
- Get feedback, apply, continue

**Ad-Hoc Development:**

- Review before merge (use `review_type: "full"`)
- Review when stuck (use `focus_on` to specify concern)

## Advanced Usage

### Focused Reviews

Specify particular concerns:

```
focus_on: "performance optimization in data processing loop"
```

### Security Reviews

```
review_type: "security"
focus_on: "authentication and authorization logic"
```

### Follow-up Reviews

After fixing issues, use `continuation_id` to reference previous review:

```
continuation_id: "review-2025-01-14-task2"
step: "Re-review after fixing progress indicators issue."
```

### Custom Standards

```
standards: "Follow Airbnb JavaScript style guide. All functions must have JSDoc comments. Test coverage >80%."
```

## Tool Parameters Reference

**Required:**

- `model: "gpt-5.1"` - Always use gpt-5.1 for code review
- `step` - Description of what to review and requirements
- `step_number: 1` - First step
- `total_steps: 1` - Single-step review
- `next_step_required: false` - Complete in one call
- `findings: ""` - Empty initially
- `relevant_files` - Absolute paths to files needing review

**Recommended:**

- `review_type` - "full" (default), "security", "performance", or "quick"
- `focus_on` - Specific aspects to emphasize
- `standards` - Coding standards to enforce

**Optional:**

- `continuation_id` - For follow-up reviews
- `severity_filter` - Minimum severity to report ("critical", "high", "medium", "low", "all")
- `use_websearch: true` - Research best practices (enabled by default)

## Red Flags

**Never:**

- Skip review because "it's simple"
- Ignore critical or high-severity issues
- Proceed with unfixed high-severity issues
- Argue with valid technical feedback

**If feedback wrong:**

- Push back with technical reasoning
- Show code/tests that prove it works
- Use `continuation_id` to continue discussion with context

## Migration Note

**Legacy approach:** This skill previously used Task tool with `superpowers:code-reviewer` subagent.

**Supporting files (now legacy):**

- `requesting-code-review/code-reviewer.md` - Template (no longer needed)
- `agents/code-reviewer.md` - Agent definition (no longer needed)

These files are kept for reference but zen MCP provides superior structured review.
