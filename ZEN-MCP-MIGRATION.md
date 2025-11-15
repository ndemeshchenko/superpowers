# Zen MCP Migration Analysis

**Date:** 2025-01-14
**Status:** 2 of 20 skills migrated

## Overview

This document tracks the migration of superpowers skills to use zen MCP tools. Zen MCP provides systematic investigation, expert validation, multi-model consensus, structured output, and web search integration - significantly enhancing skill capabilities.

### Available Zen MCP Tools

- `mcp__zen__chat` - General chat, brainstorming, collaborative thinking
- `mcp__zen__thinkdeep` - Multi-stage investigation and reasoning for complex problems
- `mcp__zen__planner` - Complex task planning with interactive planning and revision
- `mcp__zen__consensus` - Multi-model consensus through structured debate
- `mcp__zen__codereview` - Systematic code review with expert validation
- `mcp__zen__precommit` - Git changes validation before committing
- `mcp__zen__debug` - Systematic debugging and root cause analysis
- `mcp__zen__secaudit` - Security audit with OWASP analysis
- `mcp__zen__docgen` - Documentation generation with complexity analysis
- `mcp__zen__analyze` - Code analysis (architecture, performance, quality)
- `mcp__zen__refactor` - Refactoring analysis and code smell detection
- `mcp__zen__tracer` - Code tracing for execution flow or dependencies
- `mcp__zen__testgen` - Test suite generation with edge case coverage
- `mcp__zen__challenge` - Prevents reflexive agreement, forces critical thinking

### Benefits of Zen MCP Integration

**Systematic Investigation:** Multi-step workflows with evidence tracking
**Expert Validation:** Second model reviews findings for quality
**Structured Output:** Severity levels, confidence scores, issues_found arrays
**Web Search:** Research best practices during investigation
**Continuation Support:** Resume complex investigations across sessions
**Hypothesis Tracking:** Explicit confidence levels and evidence trails

---

## Completed Migrations ✓

### 1. brainstorming → `mcp__zen__chat`
**Status:** ✓ Complete
**File:** `skills/brainstorming/SKILL.md`
**Model:** gpt-5

**Changes:**
- Uses multi-turn conversations with `continuation_id` for context
- Passes relevant project files via `files` parameter
- Enables `use_websearch: true` for best practices research
- Supports temperature and thinking_mode adjustments
- Maintains YAGNI, incremental validation, one-question-at-a-time principles
- Includes detailed example workflow

### 2. requesting-code-review → `mcp__zen__codereview`
**Status:** ✓ Complete
**File:** `skills/requesting-code-review/SKILL.md`
**Model:** gpt-5

**Changes:**
- Structured review with severity levels (critical/high/medium/low)
- Support for review types: "full", "security", "performance", "quick"
- Custom `focus_on` and `standards` parameters
- `continuation_id` for follow-up reviews
- Built-in GPT-5 expert validation
- Clear severity-based action checklist
- Legacy files kept for reference: `code-reviewer.md`, `agents/code-reviewer.md`

---

## HIGH PRIORITY - Strongest Candidates (3 skills)

### 3. systematic-debugging → `mcp__zen__debug`
**Priority:** HIGH
**Current Approach:** Manual 4-phase framework (root cause investigation, pattern analysis, hypothesis testing, implementation)
**Zen Tool:** `mcp__zen__debug`
**Model:** gpt-5

**Why This Mapping:**
Perfect 1:1 match. The skill describes exactly what the debug tool provides:
- Systematic investigation with hypothesis tracking
- Confidence levels (exploring → low → medium → high → very_high → almost_certain → certain)
- Expert validation after investigation
- Web search for debugging patterns and solutions
- Structured issues_found with severity levels
- Evidence-based root cause analysis

**Migration Benefits:**
- Eliminates manual phase tracking
- Structured output with confidence scores
- Expert model validates findings
- Continuation support for complex multi-session bugs
- Web search integration for researching error patterns

**Parameters to Use:**
```
model: "gpt-5"
step: "Investigation step content"
step_number: 1 (incrementing)
total_steps: (estimate, adjust as needed)
next_step_required: true/false
findings: "Evidence and discoveries"
hypothesis: "Current theory about root cause"
confidence: "exploring" → "certain"
relevant_files: [changed files]
use_websearch: true
```

---

### 4. root-cause-tracing → `mcp__zen__tracer`
**Priority:** HIGH
**Current Approach:** Manual backward tracing through call stack with instrumentation
**Zen Tool:** `mcp__zen__tracer`
**Model:** gpt-5

**Why This Mapping:**
Tracer tool specializes in execution flow and dependency mapping:
- Precision mode for execution flow analysis (how code actually runs)
- Dependencies mode for structural relationships (what calls what)
- Evidence gathering with files_checked tracking
- Expert analysis of trace results
- Systematic investigation of call chains

**Migration Benefits:**
- Structured tracing workflow with step tracking
- Files examined automatically tracked
- Expert validation of trace analysis
- Web search for understanding framework internals
- Clear target_description explains WHAT and WHY tracing

**Parameters to Use:**
```
model: "gpt-5"
step: "Tracing step content"
step_number: 1 (incrementing)
total_steps: (estimate)
next_step_required: true/false
findings: "Trace discoveries"
target_description: "What to trace and WHY"
trace_mode: "precision" (execution flow) or "dependencies" (structure)
relevant_files: [files being traced]
use_assistant_model: true
```

---

### 5. writing-plans → `mcp__zen__planner`
**Priority:** HIGH
**Current Approach:** Manual plan creation with bite-sized tasks
**Zen Tool:** `mcp__zen__planner`
**Model:** gpt-5

**Why This Mapping:**
Planner tool provides interactive sequential planning with deep reflection:
- Interactive step-by-step planning with revision capabilities
- Branch points for exploring alternative approaches
- Structured breakdown with step_number tracking
- Revision support (revises_step_number, is_step_revision)
- Branching support (branch_from_step, is_branch_point, branch_id)
- Expert validation of plan completeness

**Migration Benefits:**
- Systematic planning workflow with explicit steps
- Built-in revision mechanism when approach changes
- Branch exploration for comparing alternatives
- Expert analysis ensures plan completeness
- Web search for researching implementation approaches

**Parameters to Use:**
```
model: "gpt-5"
step: "Planning step content (step 1: describe task, later: plan content)"
step_number: 1 (incrementing)
total_steps: (estimate)
next_step_required: true/false
is_step_revision: false (or true with revises_step_number)
is_branch_point: false (or true with branch_from_step)
branch_id: "approach-A" (when exploring alternatives)
use_assistant_model: true
```

---

## MEDIUM PRIORITY - Good Candidates (6 skills)

### 6. receiving-code-review → `mcp__zen__challenge` + `mcp__zen__chat`
**Priority:** MEDIUM
**Current Approach:** Manual verification and pushback framework
**Zen Tools:** `mcp__zen__challenge` for critical evaluation, `mcp__zen__chat` for collaborative analysis
**Model:** gpt-5

**Why This Mapping:**
Challenge tool prevents reflexive agreement when evaluating external feedback:
- Forces critical thinking instead of automatic compliance
- Analyzes whether feedback is technically correct
- Helps formulate reasoned pushback
- Chat tool provides collaborative evaluation of suggestions

**Migration Strategy:**
1. Use `mcp__zen__challenge` when receiving feedback that seems questionable
2. Use `mcp__zen__chat` for collaborative evaluation of valid suggestions
3. Maintain current "fix critical/important, push back if wrong" framework

---

### 7. test-driven-development → `mcp__zen__testgen`
**Priority:** MEDIUM
**Current Approach:** Manual RED-GREEN-REFACTOR enforcement
**Zen Tool:** `mcp__zen__testgen` (for test generation phase)
**Model:** gpt-5

**Why This Mapping:**
Testgen tool can enhance the "write failing test" phase:
- Analyzes code paths systematically
- Identifies edge cases and boundary conditions
- Generates framework-specific tests
- Expert validation of test quality
- Ensures comprehensive test coverage

**Migration Strategy:**
Keep RED-GREEN-REFACTOR discipline, but use testgen to:
1. Help identify what tests to write (edge cases, boundaries)
2. Generate comprehensive test scenarios
3. Validate test quality before proceeding to implementation

**Note:** The skill's core discipline (delete code if written first) must remain unchanged. Testgen enhances test writing, doesn't replace the discipline.

---

### 8. verification-before-completion → `mcp__zen__precommit`
**Priority:** MEDIUM
**Current Approach:** Manual evidence-before-claims checklist
**Zen Tool:** `mcp__zen__precommit`
**Model:** gpt-5

**Why This Mapping:**
Precommit tool systematically validates work before claiming completion:
- Validates git changes (staged/unstaged)
- Multi-repository support
- Impact assessment of changes
- Security review
- Identifies missing tests, incomplete features, potential bugs

**Migration Benefits:**
- Structured validation with severity levels
- Files_checked tracking ensures thorough review
- Expert validation confirms completeness
- Web search for validation best practices

---

### 9. defense-in-depth → `mcp__zen__secaudit`
**Priority:** MEDIUM
**Current Approach:** Manual 4-layer validation pattern
**Zen Tool:** `mcp__zen__secaudit`
**Model:** gpt-5

**Why This Mapping:**
Security audit tool systematizes multi-layer validation:
- OWASP Top 10 analysis
- Checks validation at multiple layers (input, processing, storage, output)
- Compliance framework support (SOC2, PCI DSS, HIPAA, GDPR)
- Severity-based issue categorization
- Threat modeling

**Migration Benefits:**
- Systematic security review at each layer
- Expert validation of security measures
- Web search for current security best practices
- Structured output with vulnerability severity

---

### 10. dispatching-parallel-agents → Multiple zen tool calls
**Priority:** MEDIUM
**Current Approach:** Manual dispatch of Task tool subagents for independent failures
**Zen Tools:** Multiple `mcp__zen__debug` or other zen tools (one per domain)
**Model:** gpt-5

**Why This Mapping:**
Can dispatch multiple zen workflows in parallel for independent problems:
- Each gets systematic investigation with expert validation
- Structured output from each investigation
- Parallel execution for speed
- Each domain gets appropriate zen tool (debug, analyze, etc.)

**Migration Strategy:**
1. Identify independent problems/domains
2. Select appropriate zen tool for each (debug, analyze, refactor, etc.)
3. Dispatch multiple tool calls in parallel
4. Synthesize results from all investigations

**Note:** Requires wrapper logic to coordinate multiple zen calls and synthesize results.

---

### 11. subagent-driven-development → Existing zen tools
**Priority:** MEDIUM
**Current Approach:** Task tool with subagents + code review between tasks
**Zen Tools:** Already uses `mcp__zen__codereview` via requesting-code-review skill
**Model:** gpt-5

**Why This Mapping:**
Skill already references requesting-code-review which now uses zen. Could potentially enhance with:
- `mcp__zen__planner` for task breakdown
- `mcp__zen__debug` for debugging tasks
- `mcp__zen__testgen` for test generation tasks

**Migration Strategy:**
Keep current Task tool approach for task execution, but leverage zen tools via existing skills (requesting-code-review, systematic-debugging, etc.)

---

## LOW PRIORITY / NOT RECOMMENDED (11 skills)

### Meta/Framework Skills (Should NOT Change)

**using-superpowers**
- **Why NOT:** Meta-skill about the skills framework itself
- **Priority:** NONE

**finishing-a-development-branch**
- **Why NOT:** Git workflow - purely procedural (merge vs PR decision tree)
- **Priority:** NONE

**sharing-skills**
- **Why NOT:** Git workflow for contributing - purely procedural
- **Priority:** NONE

**using-git-worktrees**
- **Why NOT:** Git setup with safety checks - procedural, no analysis needed
- **Priority:** NONE

### Reference/Pattern Skills (Should NOT Change)

**testing-anti-patterns**
- **Why NOT:** Educational reference about what NOT to do
- **Priority:** NONE

**condition-based-waiting**
- **Why NOT:** Provides reusable code patterns - it's a technique library, not a task
- **Priority:** NONE

### Methodology Skills (Low Value for Zen)

**executing-plans**
- **Why NOT:** About execution mechanics (TodoWrite, batching), not analysis
- **Priority:** LOW - Zen tools are for investigation/reasoning, not task execution

**testing-skills-with-subagents**
- **Why NOT:** Testing methodology for process documentation
- **Priority:** LOW - Could use `mcp__zen__chat` for meta-testing, but methodology is sound as-is

**writing-skills**
- **Why NOT:** TDD for documentation - methodology is the value
- **Priority:** LOW - Could use `mcp__zen__chat` for brainstorming improvements

---

## Migration Strategy

### Phase 1: High Priority (Immediate)
1. **systematic-debugging** → `mcp__zen__debug`
   - Clearest 1:1 mapping
   - Highest impact
   - Use as template for other migrations

2. **root-cause-tracing** → `mcp__zen__tracer`
   - Similar to debugging, different tool
   - High value for complex tracing scenarios

3. **writing-plans** → `mcp__zen__planner`
   - Different pattern, but high value
   - Complex planning scenarios benefit most

### Phase 2: Medium Priority (Next Wave)
4. **verification-before-completion** → `mcp__zen__precommit`
5. **defense-in-depth** → `mcp__zen__secaudit`
6. **receiving-code-review** → `mcp__zen__challenge` + `mcp__zen__chat`
7. **test-driven-development** → `mcp__zen__testgen` (partial enhancement)
8. **dispatching-parallel-agents** → Multiple zen tool instances

### Phase 3: Evaluation
- Monitor usage of Phase 1 & 2 migrations
- Gather feedback on zen MCP integration
- Decide if remaining low-priority skills warrant migration
- Document migration patterns for community

### Phase 4: Documentation
- Update CLAUDE.md with zen MCP patterns
- Create migration guide for skill authors
- Document best practices for zen tool parameter selection

---

## Testing Requirements

Per repository philosophy: **NO SKILL WITHOUT A FAILING TEST FIRST**

For each migration:
1. **RED:** Run pressure scenarios WITHOUT zen tool, document baseline behavior
2. **GREEN:** Migrate to zen tool, verify improved behavior
3. **REFACTOR:** Close loopholes, add explicit instructions

Use `superpowers:testing-skills-with-subagents` for validation.

---

## Tool Parameter Patterns

### Common Required Parameters
- `model: "gpt-5"` - Always use GPT-5 for expert validation
- `step` - Current work step content
- `step_number` - Current step (starts at 1)
- `total_steps` - Estimated total (adjust as needed)
- `next_step_required` - true/false for continuation
- `findings` - Discoveries from this step

### Common Optional Parameters
- `continuation_id` - For resuming complex investigations
- `use_websearch: true` - Research best practices (recommended)
- `relevant_files` - Absolute paths to files under investigation
- `temperature` - 0.3-0.5 for focused, 0.7-0.9 for creative
- `thinking_mode` - "minimal"/"low"/"medium"/"high"/"max"

### Tool-Specific Parameters
See individual tool documentation for specialized parameters like:
- `hypothesis`, `confidence` (debug)
- `trace_mode`, `target_description` (tracer)
- `is_step_revision`, `branch_id` (planner)
- `review_type`, `focus_on` (codereview)
- `severity_filter` (multiple tools)

---

## Success Metrics

For each migrated skill, track:
- **Usage frequency** - Is the skill being used more/less?
- **Error reduction** - Fewer mistakes/oversights?
- **Time efficiency** - Faster or slower than manual approach?
- **Output quality** - Better analysis, recommendations, findings?
- **User satisfaction** - Feedback from developers using skills

---

## Migration Checklist Template

For each skill migration:

- [ ] Read current skill implementation thoroughly
- [ ] Identify appropriate zen MCP tool(s)
- [ ] Map current workflow to tool parameters
- [ ] Create pressure scenarios (RED phase)
- [ ] Document baseline behavior without zen tool
- [ ] Rewrite skill to use zen tool (GREEN phase)
- [ ] Test with pressure scenarios
- [ ] Close loopholes and add explicit instructions (REFACTOR phase)
- [ ] Update SKILL.md with:
  - [ ] Tool parameters reference
  - [ ] Example workflow
  - [ ] Migration note (if replacing old approach)
- [ ] Update commands/* if needed
- [ ] Test integration with other skills
- [ ] Commit and document changes

---

## Notes

**Version:** This analysis reflects zen MCP capabilities as of 2025-01-14.

**Model Choice:** All migrations use `model: "gpt-5"` for consistency and quality. Other models (o3, gemini-2.5-pro, etc.) can be specified via user preference.

**Backwards Compatibility:** Legacy approaches are documented but not removed. Migration notes explain the change for users.

**Community Contribution:** Successful migration patterns should be shared via `superpowers:sharing-skills` workflow.

---

## Questions / Discussion

**Should we create zen-specific skills?**
- e.g., `zen-debugging` vs modifying `systematic-debugging`
- Pro: Keeps both approaches available
- Con: Duplication, confusion about which to use

**How to handle model preference?**
- Always gpt-5? Or allow user override?
- Document model selection guidance?

**Testing strategy?**
- Full TDD cycle for each migration?
- Or batch testing after multiple migrations?

**Deprecation plan for legacy approaches?**
- Keep indefinitely?
- Mark as deprecated?
- Remove after transition period?
