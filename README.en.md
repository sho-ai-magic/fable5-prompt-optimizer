[日本語](README.md) | English

[![Claude Platform Docs: Prompting Claude Fable 5](https://img.shields.io/badge/Claude%20Platform%20Docs-Prompting%20Claude%20Fable%205-f2f1ec?style=for-the-badge&labelColor=1a1a1a)](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5)

# fable5-prompt-optimizer

A [Claude Skill](https://docs.claude.com/en/docs/claude-code/skills) that turns "I want to do X" into a prompt that follows the recommended patterns from Anthropic's official "[Prompting Claude Fable 5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5)" guide — no existing prompt required. If you already have a prompt, it can also rewrite that one to comply with the guide. Works in both [claude.ai (chat)](https://claude.ai) and [Claude Code](https://docs.claude.com/en/docs/claude-code).

## Overview

The skill accepts two kinds of input:

- **Just a description of what you want** (e.g. "Create a prompt for a customer-support chatbot that handles returns") → it drafts a prompt from scratch that satisfies your goal, then applies the Fable 5 recommended patterns.
- **An existing prompt** (a system prompt, agent instructions, a one-off task request, etc.) → it keeps the content and intent intact and rewrites it to follow the recommended patterns.

In both cases, it selects only the behavior-tuning patterns recommended for Claude Fable 5 / Mythos 5 that actually apply (summarized as checklist items G1–G13 in [`references/fable5-guidelines.md`](fable5-prompt-optimizer/references/fable5-guidelines.md)), and produces the result as a Markdown file.

- Your stated goal, or the original intent and task content of an existing prompt, is never changed. Only Fable-5-specific "behavior tuning" instructions are added or adjusted, and only where relevant.
- Instead of pasting in every guideline section mechanically, it looks at what kind of prompt is involved (an agentic prompt, a one-off generation task, a long-running autonomous task, etc.) and applies only the matching items.
- The output includes both the "final prompt" and a "summary of changes" (what was added or modified, and which guideline item it maps to).

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

First, download the `fable5-prompt-optimizer/` folder to your machine using one of the following:

- **Download as a ZIP from GitHub:** On this repo's page ([https://github.com/sho-ai-magic/fable5-prompt-optimizer](https://github.com/sho-ai-magic/fable5-prompt-optimizer)), click the green "Code" button → "Download ZIP", then unzip it and use the `fable5-prompt-optimizer-main/fable5-prompt-optimizer/` folder.
- **Clone with git:**
  ```bash
  git clone https://github.com/sho-ai-magic/fable5-prompt-optimizer.git
  ```
  Use the `fable5-prompt-optimizer/fable5-prompt-optimizer/` folder produced by the clone.

Then upload that `fable5-prompt-optimizer/` folder from claude.ai's "Settings" → "Customize" → "Skills" (see [how to create a skill](https://support.claude.com/en/articles/12512180)).

## Usage

The same phrasing triggers the skill in both Claude Code and claude.ai.

**Creating a prompt from a description** (no existing prompt needed)

- "Create a Fable 5 prompt for a customer support chatbot that handles returns"
- "Make me a Fable 5 prompt for a coding agent that runs unattended overnight"

**Rewriting a prompt you already have** (paste the prompt text)

- "Optimize this prompt for Fable 5"
- "Rewrite this system prompt for Claude Fable 5"
- "Make this prompt comply with the Fable 5 prompt guide"

Once triggered, the skill first determines whether the input is a description of a goal or an existing prompt. For a goal description, it drafts a prompt from scratch to satisfy that goal; for an existing prompt, it works from that text as-is. It then determines the type of prompt (system prompt, one-off task, autonomous agent, etc.), applies only the relevant guideline items, and outputs the finished prompt as a Markdown file.

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
