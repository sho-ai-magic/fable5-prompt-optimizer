[日本語](README.md) | English

# fable5-prompt-optimizer

A [Claude Skill](https://docs.claude.com/en/docs/claude-code/skills) that automatically rewrites an existing prompt (system prompt or task instruction) to follow the recommended patterns from Anthropic's official "[Prompting Claude Fable 5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5)" guide. Works in both [claude.ai (chat)](https://claude.ai) and [Claude Code](https://docs.claude.com/en/docs/claude-code).

## What it does

The skill analyzes a prompt you provide (a system prompt, agent instructions, a one-off task request, etc.), selects only the behavior-tuning patterns recommended for Claude Fable 5 / Mythos 5 that actually apply to that prompt (summarized as checklist items G1–G13 in [`references/fable5-guidelines.md`](fable5-prompt-optimizer/references/fable5-guidelines.md)), and produces a rewritten Markdown file.

- The original intent and task content of the prompt is never changed. Only Fable-5-specific "behavior tuning" instructions are added or adjusted, and only where relevant.
- Instead of pasting in every guideline section mechanically, it looks at what kind of prompt you gave it (an agentic prompt, a one-off generation task, a long-running autonomous task, etc.) and applies only the matching items.
- The output includes both the "final rewritten prompt" and a "summary of changes" (what was added or modified, and which guideline item it maps to).

## Installation

### Using it in Claude Code

Place the `fable5-prompt-optimizer/` directory from this repo into your Claude Code skills directory.

```bash
git clone https://github.com/sho-ai-magic/fable5-prompt-optimizer.git
mkdir -p ~/.claude/skills
cp -r fable5-prompt-optimizer/fable5-prompt-optimizer ~/.claude/skills/fable5-prompt-optimizer
```

To scope it to a single project instead, place it under that project's `.claude/skills/` directory rather than `~/.claude/skills/`.

### Using it in claude.ai (chat)

Upload the `fable5-prompt-optimizer/` folder as a custom skill from claude.ai's "Capabilities" settings (see [how to create a skill](https://support.claude.com/en/articles/12512180)).

## Usage

Paste the prompt you want to optimize, then ask something like the following (the same phrasing triggers the skill in both Claude Code and claude.ai):

- "Optimize this prompt for Fable 5"
- "Rewrite this system prompt for Claude Fable 5"
- "Make this prompt comply with the Fable 5 prompt guide"

Once triggered, the skill determines the type of prompt (system prompt, one-off task, autonomous agent, etc.), applies only the relevant guideline items, and outputs the finished prompt as a Markdown file.

## Guideline items (G1–G13)

Summary of each checklist item defined in `references/fable5-guidelines.md`. Only the items matching your prompt's type are applied automatically.

| # | Item | Summary |
|---|---|---|
| G1 | Overall stance | Fable 5 can handle harder, longer-running tasks than prior models. A baseline check for whether instructions are being too conservative (not something appended directly to a prompt) |
| G2 | Curb over-planning | For autonomous/long-running tasks: avoid endlessly weighing options under ambiguity — act once there's enough information |
| G3 | Concise instructions | Leverage strong instruction-following: instead of a long list of individual prohibitions, use a short directive ("lead with the conclusion," "prioritize readability while cutting irrelevant detail") to curb verbose output |
| G4 | Fact-grounded progress reports | During long autonomous runs, avoid completion claims that weren't actually verified — cross-check against tool results before reporting |
| G5 | Guard against overreach | Make boundaries explicit so the model doesn't take unrequested actions (sending email, changing system state, etc.) |
| G6 | Parallel subagent use | Delegate independent subtasks to subagents and encourage aggressive parallelization |
| G7 | Cross-session memory | For agents that work across multiple sessions on the same project, record and reference lessons learned in files |
| G8 | Avoid premature stopping / over-confirmation | For fully autonomous pipelines: don't just announce an action without executing it, and don't stop to ask for permission when there's already enough information |
| G9 | Clear confirmation points for interactive prompts | Only ask for confirmation on destructive/irreversible actions or clear scope changes; otherwise proceed autonomously |
| G10 | Avoid referencing context budget | For harnesses that surface remaining token count, avoid cutting work short, summarizing, or suggesting a new session because of it |
| G11 | Convey intent and background | State upfront what the task is for and who the output serves — improves accuracy |
| G12 | Readability of the final user-facing report | Don't reuse in-progress shorthand for the final report — rewrite it in full sentences for a reader who wasn't watching the work happen |
| G13 | Tooling for delivering interim results to the user | For harness developers only: clarify when to use a dedicated tool to deliver partial results without ending the turn (only where applicable) |

## File structure

```
fable5-prompt-optimizer/
├── SKILL.md                          # The skill definition and procedure
└── references/
    └── fable5-guidelines.md          # The G1-G13 guideline checklist
```

## Reference

This skill is based on an independent summary and reorganization of Anthropic's official "[Prompting Claude Fable 5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5)" guide. It is not a verbatim excerpt of the original text.

## License

[MIT License](LICENSE)
