# Tech Stack Research — Claude Code Plugin

A [Claude Code](https://code.claude.com) plugin that researches a company's **real** production tech stack by triangulating GitHub repos, job postings, engineering blogs, and infrastructure signals.

## Install

```
/plugin marketplace add irinanazarova/skill-tech-stack-research
/plugin install research-tech-stack@skill-tech-stack-research
```

## Usage

```
/research-tech-stack figma
/research-tech-stack crowdstrike.com
/research-tech-stack "Surge AI"
```

## What it does

1. Scans the company's GitHub org — repos, forks, dependency files
2. Searches job postings across Greenhouse, Ashby, BuiltIn, and HN
3. Finds engineering blog posts and architecture talks
4. Checks cloud provider case studies and engineer profiles
5. Detects infrastructure signals (Sidekiq = Ruby, Celery = Python, etc.)
6. Cross-references everything and outputs a structured assessment with confidence ratings
7. Saves results to `tech-stacks/{company-slug}.md` in your project directory

It cuts through common misleading signals — TypeScript on the frontend doesn't mean TypeScript on the backend, a Python SDK doesn't mean a Python platform, and many companies hide their "boring" Rails/Django backend behind flashy marketing.

## Output

Each run produces a dated markdown file with:

- **What they do** — one-line company description
- **Core Backend** — language/framework with confidence level and evidence
- **Frontend** — framework with sources
- **Databases** — OLTP, analytics, caching
- **Infrastructure** — cloud, orchestration, job queues, streaming
- **Key Architectural Patterns** — e.g., "dual-language: Rails for platform, Go for agents"
- **Confidence Notes** — what's confirmed vs. inferred
- **All sources** — every URL consulted

## License

MIT
