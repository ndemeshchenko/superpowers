---
name: brainstorming
description: Use when creating or developing, before writing code or implementation plans - refines rough ideas into fully-formed designs through collaborative questioning, alternative exploration, and incremental validation using zen MCP with gpt-5.1. Don't use during clear 'mechanical' processes
---

# Brainstorming Ideas Into Designs

## Overview

Help turn ideas into fully formed designs and specs using the `mcp__zen__chat` tool with gpt-5.1 for deep collaborative brainstorming.

This skill uses zen MCP to conduct multi-turn design conversations that explore alternatives, validate incrementally, and produce comprehensive design documents.

## The Process

### 1. Gather Context

Before starting brainstorming:

- Check current project state (files, docs, recent commits)
- Identify relevant files that provide context
- Understand user's initial idea or requirements

### 2. Initial Brainstorming Call

Use `mcp__zen__chat` with these parameters:

```text
model: "gpt-5.1"
prompt: Comprehensive brainstorming prompt including:
  - User's idea/requirements
  - Project context
  - Request to: understand purpose, constraints, success criteria
  - Ask ONE clarifying question to refine the idea
  - Prefer multiple choice when possible
files: [relevant project files as absolute paths]
use_websearch: true
continuation_id: "brainstorm-YYYY-MM-DD-<topic>"
```

**Important:** The prompt should instruct gpt-5.1 to ask ONE question at a time, focusing on understanding before proposing solutions.

### 3. Iterative Refinement

Continue calling `mcp__zen__chat` with the same `continuation_id`:

- Each call builds on previous conversation context
- User answers questions → next call asks follow-up OR moves to alternatives
- Maintain focus on: purpose, constraints, success criteria
- Use YAGNI ruthlessly - challenge unnecessary features

### 4. Explore Approaches

Once requirements are clear, call `mcp__zen__chat` requesting:

- 2-3 different architectural approaches with trade-offs
- Recommendation with reasoning
- Lead with recommended option and explain why

### 5. Design Presentation

Call `mcp__zen__chat` to present the design:

- Request design in sections (architecture, components, data flow, error handling, testing)
- Present 200-300 words per section
- After each section, check with user if it looks right
- Be ready to backtrack and clarify

### 6. Synthesis

Once design is validated:

- Ask gpt-5.1 to provide final comprehensive design document
- Include all validated sections
- Format for markdown documentation

## After the Design

**Documentation:**

- Write the validated design to `docs/plans/YYYY-MM-DD-<topic>-design.md`
- Use elements-of-style:writing-clearly-and-concisely skill if available
- Commit the design document to git

**Implementation (if continuing):**

- Ask: "Ready to set up for implementation?"
- Use superpowers:using-git-worktrees to create isolated workspace
- Use superpowers:writing-plans to create detailed implementation plan

## Key Principles

- **One question at a time** - Instruct gpt-5.1 to ask only one question per response
- **Multiple choice preferred** - Easier to answer than open-ended when possible
- **YAGNI ruthlessly** - Challenge unnecessary features in prompts
- **Explore alternatives** - Always request 2-3 approaches before settling
- **Incremental validation** - Present design in sections, validate each
- **Continuation ID** - Use same ID throughout to maintain conversation context
- **Web search enabled** - Let gpt-5.1 research best practices and current standards

## Example Workflow

```
1. User: "I want to add user authentication"

2. Claude: [Gathers context files]
   Calls mcp__zen__chat:
   model: gpt-5.1
   prompt: "User wants to add authentication. Current project: [context]. Ask ONE clarifying question about requirements."
   continuation_id: "brainstorm-2025-01-14-auth"

3. gpt-5.1 responds: "What type of users will authenticate? (a) Internal employees (b) External customers (c) Both"

4. User: "External customers"

5. Claude: Calls mcp__zen__chat again with same continuation_id
   prompt: "User answered: external customers. Ask next most important question."

... continue until requirements clear ...

6. Claude: Calls for alternatives
   prompt: "Requirements are clear. Propose 2-3 auth approaches with trade-offs."

7. Claude: Calls for design sections
   prompt: "User chose approach X. Present architecture section (200-300 words)."

... validate each section ...

8. Claude: Calls for final synthesis
   prompt: "All sections validated. Provide complete design document."

9. Claude: Writes to docs/plans/2025-01-14-auth-design.md and commits
```

## Tool Parameters Reference

**Required:**

- `model: "gpt-5.1"` - Always use gpt-5.1 for brainstorming
- `prompt` - Detailed context and specific request for this turn
- `continuation_id` - Reuse same ID to maintain conversation state

**Recommended:**

- `files` - Array of absolute file paths for context
- `use_websearch: true` - Enable research of best practices

**Optional:**

- `temperature` - Lower (0.3-0.5) for focused questions, higher (0.7-0.9) for creative alternatives
- `thinking_mode` - Use "medium" or "high" for complex architectural decisions
