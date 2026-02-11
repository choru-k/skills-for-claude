# skills-for-claude

Custom skills for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) — multi-AI orchestration, context handoffs, session forking, and development guides.

## Skills

| Skill | Description |
|-------|-------------|
| **call-ai** | Run prompts against external AIs (Codex, Gemini, Claude) with parallel execution, retries, and Zellij pane streaming |
| **complete-prompt** | Generate structured XML context prompts for AI-to-AI handoffs (9 modes + `--refs`) |
| **context-fork** | Fork the current session to Haiku, Sonnet, or Opus with full conversation context preserved |
| **second-opinion** | Get second opinions from external AIs on programming questions with parallel execution and synthesis |
| **skill-creator** | Guide for creating effective Claude Code skills with SKILL.md templates and validation scripts |
| **agent-development** | Comprehensive guide for creating Claude Code subagents with frontmatter, permissions, and hooks |
| **hook-development** | Comprehensive guide for creating Claude Code hooks with command, prompt, and agent hook types |

## Installation

### Plugin marketplace

```bash
# Add the marketplace
claude plugin marketplace add choru-k/skills-for-claude

# Install individual skills
claude plugin install call-ai
claude plugin install complete-prompt
claude plugin install context-fork
claude plugin install second-opinion
claude plugin install skill-creator
claude plugin install agent-development
claude plugin install hook-development
```

### Manual

```bash
git clone https://github.com/choru-k/skills-for-claude.git /tmp/skills-for-claude

# Copy whichever skills you want
cp -r /tmp/skills-for-claude/plugins/call-ai/skills/call-ai ~/.claude/skills/call-ai
cp -r /tmp/skills-for-claude/plugins/complete-prompt/skills/complete-prompt ~/.claude/skills/complete-prompt
cp -r /tmp/skills-for-claude/plugins/context-fork/skills/context-fork ~/.claude/skills/context-fork
cp -r /tmp/skills-for-claude/plugins/second-opinion/skills/second-opinion ~/.claude/skills/second-opinion
cp -r /tmp/skills-for-claude/plugins/skill-creator/skills/skill-creator ~/.claude/skills/skill-creator
cp -r /tmp/skills-for-claude/plugins/agent-development/skills/agent-development ~/.claude/skills/agent-development
cp -r /tmp/skills-for-claude/plugins/hook-development/skills/hook-development ~/.claude/skills/hook-development
```

## Usage

After installation, skills appear as slash commands in Claude Code:

```
/call-ai "What are the tradeoffs of REST vs GraphQL?"
/call-ai :all "Review this architecture"
/cp debug
/cp brief --refs
/context-fork haiku "summarize our conversation"
/context-fork opus "deep analysis of this module"
/so "Redis or Memcached for session storage?"
/so :all "Review this architecture decision"
/skill-creator
/agent-development
/hook-development
```

## Prerequisites

- **call-ai** requires CLI tools: `codex`, `gemini`, and/or `claude` (install whichever providers you want to use)
- **complete-prompt** has no external dependencies
- **context-fork** requires `claude` CLI
- **second-opinion** requires `call-ai` + `complete-prompt` (install all three)
- **skill-creator**, **agent-development**, **hook-development** have no external dependencies

## License

[MIT](LICENSE)

## Author

[choru-k](https://github.com/choru-k)
