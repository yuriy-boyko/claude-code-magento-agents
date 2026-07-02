# Installation Guide

This repository contains 30 Claude Code subagent definitions for Magento 2 development. Each agent is a standalone Markdown file with YAML frontmatter (`name`, `description`, `model`, `tools`) — there is nothing to build or compile, you just need to get the `.md` files into a location Claude Code scans for agents.

Claude Code recursively scans two locations for subagents, so the existing category folders (`01-core-development/`, `02-frontend-development/`, etc.) can be dropped in as-is — no need to flatten anything:

| Scope | Path | Availability |
|---|---|---|
| **Project** | `.claude/agents/` (inside a repo) | Only in that project, shareable via git |
| **User** | `~/.claude/agents/` | Every project on your machine |

Pick one based on whether you want these agents available in a single Magento 2 project or globally.

## Option A — Install for one project (recommended for teams)

Run this from the root of your Magento 2 project so the agents are versioned alongside it and every teammate gets them automatically:

```bash
mkdir -p .claude/agents
git clone https://github.com/yuriy-boyko/claude-code-magento-agents.git .claude/agents/magento
```

To keep them up to date later:

```bash
git -C .claude/agents/magento pull
```

If you don't want the agents tracked as a nested git repo in your project's history, add it as a submodule instead:

```bash
git submodule add https://github.com/yuriy-boyko/claude-code-magento-agents.git .claude/agents/magento
```

## Option B — Install globally (available in every project)

```bash
mkdir -p ~/.claude/agents
git clone https://github.com/yuriy-boyko/claude-code-magento-agents.git ~/.claude/agents/magento
```

Update anytime with:

```bash
git -C ~/.claude/agents/magento pull
```

## Verify the install

Inside Claude Code, run:

```
/agents
```

Open the **Library** tab — all 30 agents should be listed (their `name` fields are prefixed `magento-`, e.g. `magento-code-reviewer`, `magento-php-specialist`). If an agent is missing, double check the `.claude/agents/` (or `~/.claude/agents/`) path and that the clone completed without error.

## Using the agents

- **Automatic delegation**: just describe your task normally (e.g. "review this module for security issues") — Claude matches it against each agent's `description` field and delegates on its own.
- **Explicit invocation**: type `@` in the prompt and pick the agent by name (e.g. `@magento-hyva-specialist`), or type `@magento-hyva-specialist` directly, to force that specific agent to handle the task.
- **Whole-session mode**: start Claude Code already running as one agent with `claude --agent magento-code-reviewer`.

Several agent files list a `subagents:` field in their frontmatter (e.g. `feature-developer` lists `magento-php-specialist`, `magento-code-reviewer`, ...). This is documentation only — Claude Code has no `subagents:` frontmatter field, so it doesn't restrict or force anything. It's a hint (also mirrored in each category's `README.md` as "Delegates to") about which specialists an orchestrator agent is expected to pull in; actual delegation is still driven by matching task context to `description` fields.

## Agent reference

| Category | Count | Examples |
|---|---|---|
| [01-core-development](./01-core-development/) | 6 | `magento-code-reviewer`, `magento-feature-developer`, `magento-issue-debugger` |
| [02-frontend-development](./02-frontend-development/) | 4 | `magento-hyva-specialist`, `magento-luma-specialist` |
| [03-backend-development](./03-backend-development/) | 3 | `magento-api-developer`, `magento-cronjob-developer` |
| [04-performance-security](./04-performance-security/) | 4 | `magento-performance-analyst`, `magento-security-analyst` |
| [05-language-specialists](./05-language-specialists/) | 7 | `magento-php-specialist`, `magento-alpine-specialist` |
| [06-infrastructure](./06-infrastructure/) | 3 | `magento-deployment-engineer`, `magento-environment-engineer` |
| [07-ecommerce-specialists](./07-ecommerce-specialists/) | 3 | `magento-catalog-analyst`, `magento-order-analyst` |

Each category folder has its own `README.md` with a full breakdown of what each agent does and which specialists it delegates to.

## Uninstalling

Remove the cloned directory:

```bash
rm -rf .claude/agents/magento        # project install
rm -rf ~/.claude/agents/magento      # global install
```

## Troubleshooting

- **Agent not showing up in `/agents`**: confirm the `.md` files actually landed under `.claude/agents/` or `~/.claude/agents/` — Claude Code only scans those two roots (plus any dir added with `--add-dir`).
- **Name collision**: agent identity comes from the `name:` field, not the file path or filename. If you've also written custom agents with a `magento-*` name, the one closest to your project (or listed first in the same scope) wins — run `/doctor` to see which definition is active.
- **Wrong model used**: each agent pins its own `model` (`sonnet` or `opus` for most; `code-reviewer` and `security-analyst` use `opus`). Override per-session with the `CLAUDE_CODE_SUBAGENT_MODEL` env var if needed.
