# Tech Stack Research — Claude Code Skill

A [Claude Code skill](https://docs.anthropic.com/en/docs/claude-code/skills) that researches a company's **real** production tech stack by triangulating GitHub repos, job postings, engineering blogs, and infrastructure signals.

## What it does

Run `/research-tech-stack stripe` and the skill will:

1. Scan the company's GitHub org (repos, forks, dependency files)
2. Search job postings across Greenhouse, Ashby, BuiltIn, and HN
3. Find engineering blog posts and architecture talks
4. Check cloud provider case studies and engineer profiles
5. Detect infrastructure signals (Sidekiq → Ruby, Celery → Python, etc.)
6. Cross-reference everything and output a structured assessment with confidence ratings
7. Save results to `tech-stacks/{company-slug}.md` in your project directory

It's designed to cut through common misleading signals — TypeScript on the frontend doesn't mean TypeScript on the backend, a Python SDK doesn't mean a Python platform, and many companies hide their "boring" Rails/Django backend behind flashy marketing.

## Install

### Global (available in all projects)

```bash
# Clone into your Claude Code skills directory
git clone https://github.com/irinanazarova/skill-tech-stack-research ~/.claude/skills/research-tech-stack
```

### Per-project (shared with teammates via the repo)

```bash
# From your project root
git clone https://github.com/irinanazarova/skill-tech-stack-research .claude/skills/research-tech-stack
```

Or add as a git submodule:

```bash
git submodule add https://github.com/irinanazarova/skill-tech-stack-research .claude/skills/research-tech-stack
```

## Usage

```
/research-tech-stack [company-name or domain]
```

Examples:

```
/research-tech-stack figma
/research-tech-stack crowdstrike.com
/research-tech-stack "Surge AI"
```

## Output

Each run produces a structured markdown file in `tech-stacks/` with:

- **What they do** — one-line company description
- **Core Backend** — language/framework with confidence level and evidence
- **Frontend** — framework with sources
- **Databases** — OLTP, analytics, caching
- **Infrastructure** — cloud, orchestration, job queues, streaming
- **Key Architectural Patterns** — e.g., "dual-language: Rails for platform, Go for agents"
- **Confidence Notes** — what's confirmed vs. inferred
- **All sources** — every URL consulted, organized by signal strength
