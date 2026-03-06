# luhmannbot

Niklas Luhmann for your repo. A Claude Code agent that reads your codebase like a scholar, reorganizes your docs like an archivist, and finds the connections you missed.

Most codebases have documentation the way most people have notes: scattered, stale, and full of buried insight. Luhmannbot brings Zettelkasten discipline to your repo — extracting ideas, linking concepts across files, cleaning up inconsistencies, and building the kind of second brain your project deserves.

## What this is

This is a **meta-repo** — a role specialization pattern for Claude Code. It contains no application code. Instead, it configures a Claude Code instance as a dedicated **documentarian**: an agent whose only job is knowledge extraction, organization, and maintenance.

The heavy lifting comes from [arscontexta](https://github.com/agenticnotetaking/arscontexta), a plugin that provides the Zettelkasten methodology, research knowledge graph, and 26 skills. This repo's job is to wire that plugin into a focused role with the right context (`CLAUDE.md`) so that Claude doesn't try to be a general-purpose coding assistant — it stays in documentarian mode.

**Why separate the role?** Claude Code loads all plugins and skills for a project directory. If your main project has its own plugins for testing, deployment, code review, etc., adding arscontexta there means skill bloat — the agent sees dozens of skills and has to figure out which ones apply. Luhmannbot isolates the documentarian role into its own launch directory. You start Claude here, point it at your project, and every skill it sees is relevant to documentation and knowledge work.

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI installed and authenticated

## Setup

Clone the repo and install the required plugin:

```bash
git clone https://github.com/YOUR_USERNAME/luhmannbot.git
cd luhmannbot
```

Then, inside Claude Code (run `claude` from the luhmannbot directory):

```
/plugin marketplace add agenticnotetaking/arscontexta
/plugin install arscontexta@agenticnotetaking --local
```

This installs the [arscontexta](https://github.com/agenticnotetaking/arscontexta) plugin — a conversational derivation engine for agent-native knowledge systems.

## Onboarding a project

Start Claude Code from this directory and tell it which project to work on:

```bash
cd luhmannbot
claude
```

Then run `/setup` to scaffold a knowledge system in your target folder:

```
Work in ~/myproject. Run /setup to build a knowledge system for this codebase.
```

`/setup` walks you through a conversation to understand your domain, then generates the full folder structure, templates, and configuration.

If you're new to Zettelkasten or arscontexta, start with `/tutorial` instead — it's a hands-on walkthrough that creates real notes in five steps.

## Skills reference

| Skill | Description |
|-------|-------------|
| `/setup` | Scaffold a new knowledge system in a target folder |
| `/tutorial` | Interactive walkthrough for new users (start here if new) |
| `/recommend` | Get architecture advice before committing to a setup |
| `/health` | Run vault diagnostics (schema, orphans, links, etc.) |
| `/ask` | Query the methodology knowledge base |
| `/architect` | Get research-backed evolution advice for an existing system |
| `/add-domain` | Add a new knowledge domain to an existing system |
| `/help` | Contextual guidance and command discovery |
| `/reseed` | Re-derive system from first principles |
| `/upgrade` | Apply methodology updates from upstream |

## Automated health checks

`/health` is designed to run unattended. It's purely diagnostic — reads your vault, writes a report to `ops/health/YYYY-MM-DD-report.md`, and touches nothing else. You can run it on a schedule with Claude Code's headless mode:

```bash
cd luhmannbot
claude -p "Work in ~/myproject. Run /health full"
```

Put that in a cron job or CI pipeline to catch orphaned notes, broken links, and schema drift before they compound. The reports accumulate over time, and `/architect` reads the history to spot trends.

Note: each run consumes API tokens. `/health quick` (schema + orphans + links only) is cheaper and sufficient for routine checks. Reserve `/health full` (all 8 diagnostic categories) for weekly or post-restructuring runs.

### Acting on health reports

Health reports diagnose — they don't fix anything. There are two ways to act on them:

**Directly from the report.** Each health report ends with ranked recommended actions that name a specific skill and file:

```
Recommended Actions (top 3, ranked by impact):
1. Run /reflect on notes/forgotten-insight.md (persistent orphan, 14 days)
2. Fix dangling link [[removed-topic]] in notes/old-note.md
3. Run /reweave on 4 stale notes to find new connections
```

You can act on these immediately in a session.

**Via `/architect` for deeper analysis.** Run `/architect` in an interactive session — it reads your accumulated health reports, scans for friction patterns in session logs and observations, then cross-references everything against the research knowledge base. It produces 3-5 ranked recommendations, each with an evidence chain (health data + friction patterns + research claims), estimated implementation time, and risk assessment. Nothing changes until you approve it.

The full loop:

```
/health  (diagnose, can run unattended)
    ↓
/architect  (analyze trends, propose changes, interactive)
    ↓
  approve / reject each recommendation
    ↓
/health  (verify the fix)
```

## Example: ingesting PRDs into a second brain

When you have source documents (PRDs, specs, research) that need to be extracted into your knowledge system, chain `/architect` → manual integration → `/health`:

```
I have 3 new PRDs in ~/cantrip/prd/. Run /architect on my second brain at
~/cantrip/docs/isc to figure out where this new knowledge should land, then
integrate it, then /health to validate.
```

`/architect` reads both the existing vault and the source documents, then proposes which notes to create vs. update — with research justification for each structural decision. You approve the plan, the agent does the extraction, and `/health` validates nothing broke.

## The pattern

Luhmannbot demonstrates a general pattern: **role repos**. Instead of loading every plugin into one project, you create small repos that each configure Claude Code for a specific role. Each role repo has:

- A `CLAUDE.md` that sets the agent's focus and conventions
- Plugin(s) installed locally that provide the role's skills
- No application code — just configuration

You could apply the same pattern for other specialized agents (a code reviewer, a test writer, a security auditor) — each with its own launch directory and curated skill set.

## License

MIT
