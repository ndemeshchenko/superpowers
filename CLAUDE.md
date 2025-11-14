# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is **Superpowers** - a comprehensive skills library providing proven techniques, patterns, and workflows for AI coding assistants. The repository is structured as a Claude Code plugin that integrates systematic workflows for testing, debugging, collaboration, and development.

### Core Philosophy

- **Test-Driven Development** - Write tests first, always
- **Systematic over ad-hoc** - Process over guessing
- **Evidence over claims** - Verify before declaring success
- **No skill without failing test first** - Skills are process documentation and follow TDD principles

## Repository Structure

```
.claude-plugin/          # Plugin manifest and marketplace config
  plugin.json            # Version, metadata, keywords
  marketplace.json       # Marketplace publication config

skills/                  # All skills in flat namespace
  skill-name/
    SKILL.md            # Main skill content (required)
    supporting-files.*  # Only for heavy reference or reusable tools

commands/               # Slash command wrappers
  brainstorm.md         # Activates brainstorming skill
  write-plan.md         # Activates writing-plans skill
  execute-plan.md       # Activates executing-plans skill

hooks/                  # SessionStart hook
  session-start.sh      # Injects using-superpowers skill
  hooks.json           # Hook configuration

agents/                 # Agent definitions
  code-reviewer.md      # Code review agent

lib/                    # Utilities
  initialize-skills.sh  # Legacy installation script

.codex/                 # Codex integration (experimental)
  superpowers-codex     # Codex bootstrap script
  INSTALL.md           # Codex installation guide
```

## Development Commands

### Testing Skills

Skills follow the TDD cycle (RED-GREEN-REFACTOR):

1. **RED**: Create pressure scenarios, run WITHOUT skill, document baseline behavior
2. **GREEN**: Write minimal skill addressing baseline failures, verify compliance
3. **REFACTOR**: Close loopholes, add explicit counters for new rationalizations

Always use `superpowers:testing-skills-with-subagents` when creating or modifying skills.

### Version Management

Update version in `.claude-plugin/plugin.json` when making changes. Version format: `MAJOR.MINOR.PATCH`

### Plugin Updates

Users update the plugin with:
```bash
/plugin update superpowers
```

## Skill Authoring Guidelines

### SKILL.md Structure

**Frontmatter (YAML - max 1024 chars total):**
```yaml
---
name: skill-name-with-hyphens  # Only letters, numbers, hyphens
description: Use when [specific triggers] - [what it does, third person]
---
```

**Content structure:**
- Overview with core principle (1-2 sentences)
- When to Use (symptoms, use cases, when NOT to use)
- Core Pattern (before/after comparison)
- Quick Reference (table or bullets)
- Implementation (inline code or link to file)
- Common Mistakes (problems + fixes)

### Critical Rules for Skills

1. **Name**: Use only letters, numbers, hyphens (no parentheses or special chars)
2. **Description**: Start with "Use when...", include specific triggers/symptoms, third person, <500 chars ideal
3. **Token Efficiency**: Target <150 words for frequently-loaded skills, <500 words for others
4. **Cross-References**: Use skill name only with `**REQUIRED SUB-SKILL:**` or `**REQUIRED BACKGROUND:**` markers
5. **Never use `@` syntax**: It force-loads files immediately, burning context
6. **Flowcharts**: Only for non-obvious decision points, never for reference material
7. **Code Examples**: One excellent example beats many mediocre ones

### File Organization

- **Self-contained**: Everything in SKILL.md
- **With reusable tool**: SKILL.md + executable code to adapt
- **With heavy reference**: SKILL.md + separate reference docs (>100 lines)

### Testing Requirements

**NO SKILL WITHOUT A FAILING TEST FIRST** - This applies to NEW skills AND EDITS.

- Delete untested skills/changes completely - no keeping as "reference"
- Use TodoWrite for EVERY checklist item when creating skills
- Test discipline skills with 3+ combined pressures (time, sunk cost, authority, exhaustion)
- Document rationalizations verbatim during baseline testing
- Build rationalization tables and red flags lists

## SessionStart Hook Behavior

The `hooks/session-start.sh` script:
1. Loads the full content of `skills/using-superpowers/SKILL.md`
2. Injects it into every session as `<EXTREMELY_IMPORTANT>` context
3. Warns users if legacy `~/.config/superpowers/skills` directory exists
4. Returns JSON with `hookSpecificOutput.additionalContext`

This ensures every Claude instance knows to check for and use relevant skills.

## Integration Points

### Claude Code Plugin System

- Skills use Claude Code's first-party skills system (not legacy custom directory)
- Skills activate automatically when relevant based on description matching
- Commands are thin wrappers that activate corresponding skills

### Marketplace

Plugin is published to `obra/superpowers-marketplace`:
```bash
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace
```

### Codex (Experimental)

Codex support via `.codex/superpowers-codex` bootstrap script - still being refined based on feedback.

## Contributing Skills

1. Fork the repository
2. Create branch for your skill
3. Follow `superpowers:writing-skills` skill (mandatory)
4. Use `superpowers:testing-skills-with-subagents` to validate
5. Submit PR

Skills must be:
- Broadly applicable (not project-specific)
- Referenced across projects
- Non-obvious techniques
- Tested with failing baseline first

## Common Development Patterns

### Skill Discovery Optimization (CSO)

Keywords future Claude searches for:
- Error messages (exact text)
- Symptoms (flaky, hanging, zombie, pollution)
- Synonyms (timeout/hang/freeze)
- Tool names (actual commands, libraries)

### Bulletproofing Against Rationalization

For discipline-enforcing skills:
- Close every loophole explicitly
- Address "spirit vs letter" arguments upfront
- Build rationalization tables from test failures
- Create red flags lists for self-checking
- Update descriptions with violation symptoms

### Token Efficiency Techniques

- Move flag details to `--help` output
- Use cross-references instead of repeating
- Compress examples (show pattern, not verbose dialogue)
- Eliminate redundancy (don't repeat cross-referenced content)
- Verify word counts: `wc -w skills/*/SKILL.md`

## Architecture Notes

### Flat Namespace Design

All skills live in one searchable namespace - no hierarchical categories. This maximizes discoverability through descriptions.

### Hook-Based Bootstrap

Rather than manual setup, the SessionStart hook ensures consistent initialization. The `using-superpowers` skill is the single source of truth for how to use the skills system.

### Agent Integration

The `code-reviewer` agent demonstrates integration patterns for systematic code review with expert validation.
