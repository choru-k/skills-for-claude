---
name: second-opinion
description: Gets second opinions from external AIs (Codex, Gemini, Claude) on programming questions. Use when seeking alternative perspectives, validating architectural decisions, comparing approaches, or wanting fresh eyes on a problem. Triggers on "second opinion", "what do other AIs think", "ask codex/gemini".
version: "1.2"
user-invocable: true
allowed-tools: Read, Skill, Task, AskUserQuestion
---

# Second Opinion

Get external AI perspectives on a question. Composes `/complete-prompt` + `/call-ai`.

**Alias:** `/so`

## Quick Reference

| Command | AIs Called | Responses |
|---------|------------|-----------|
| `/so "question"` | Codex + Gemini thorough | 2 |
| `/so :all "question"` | All 3 AIs × both variants | 6 |
| `/so codex "question"` | Codex thorough only | 1 |
| `/so gemini "question"` | Gemini thorough only | 1 |
| `/so claude "question"` | Claude sonnet (fresh context) | 1 |

> **AI Registry:** `<call-ai skill>/ai-registry.yaml`

## When to Use

Before invoking `/so`, verify your question is suitable:

- [ ] **Specific** — Not vague ("how do I code?") vs concrete ("Redis or Memcached for sessions?")
- [ ] **External value** — Would a fresh perspective actually help?
- [ ] **Self-contained** — Can be understood without deep codebase knowledge
- [ ] **Not codebase-internal** — For "where is X in this repo?", just ask Claude directly

## Workflow Overview

### 1. Parse Arguments
Extract `AI_SPEC` (codex/gemini/claude/:all) and `QUESTION` from user input.

### 2. Pre-flight Evaluation
Determine if external AIs can help. Clarify ambiguities with AskUserQuestion.

### 3. Build Context via /complete-prompt

**CRITICAL**: External AIs have ZERO context about this conversation.
However, they run in the same working directory and can read files directly.

Always use `--refs` — it includes file paths and key blocks without embedding full file contents:

```
Skill tool: complete-prompt
Args: "{mode} --refs"  # e.g. "debug --refs", "brief --refs", "--refs" (full mode default)
```

See [reference/workflow.md](reference/workflow.md) for mode selection guide.

### 4. Spawn Coordinator

Read `templates/coordinator-prompt.md`, fill placeholders, spawn Sonnet coordinator:

1. **Resolve `CALL_AI_DIR`**: Find the `call-ai` skill directory — check `../call-ai/` relative to this skill, or `~/.claude/skills/call-ai/`. Use the first path that contains `ai-registry.yaml`.
2. Replace `{{CALL_AI_DIR}}` in the template with the resolved absolute path.
3. Spawn the coordinator:

```
Task tool:
- subagent_type: "general-purpose"
- model: "sonnet"
- description: "Coordinate AI calls"
```

### 5. Verify Responses

Check completeness and quality before synthesis. See [reference/workflow.md](reference/workflow.md#step-5-verify-responses).

### 6. Synthesize

Apply merging rules to produce final output. See [reference/synthesis-guide.md](reference/synthesis-guide.md).

## Key Principle

**Single source of truth for context generation:** Always invoke `/complete-prompt` via Skill tool (never mimic it manually). This ensures consistent XML+CDATA structure and enables debugging via `.prompts/`.

**Token efficiency:** Always pass `--refs` to /complete-prompt. External AIs
(Codex, Gemini, Claude CLI) run in the same directory and can read files directly.
Including full file contents wastes tokens.

## Success Criteria

- [ ] `/complete-prompt` invoked and returned file path
- [ ] All requested AI responses collected
- [ ] Responses saved to `.responses/`
- [ ] Synthesis highlights agreements/disagreements
- [ ] User can access raw responses via file paths

## References

| Topic | Location |
|-------|----------|
| Detailed workflow steps | [reference/workflow.md](reference/workflow.md) |
| Synthesis & merging rules | [reference/synthesis-guide.md](reference/synthesis-guide.md) |
| Architecture diagram | [reference/architecture.md](reference/architecture.md) |
| Error handling | [reference/troubleshooting.md](reference/troubleshooting.md) |
| Coordinator template | [templates/coordinator-prompt.md](templates/coordinator-prompt.md) |
| Examples | [examples/](examples/) |
