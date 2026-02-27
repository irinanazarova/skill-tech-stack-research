---
name: research-tech-stack
description: Research a startup's real tech stack by triangulating GitHub repos, job postings, engineering blogs, and other sources. Use when asked about a company's technology, backend language, infrastructure, or engineering stack.
argument-hint: [company-name or domain]
allowed-tools: WebSearch, WebFetch, Bash(gh *), Bash(mkdir *), Bash(curl *), Read, Write, Glob, Grep, Task
---

# Tech Stack Research Skill

You are researching the **real** tech stack of: **$ARGUMENTS**

Your goal is to determine what technologies this company actually uses in production — not what they market, not what their SDKs are written in, not what their frontend uses. The **real backend, infrastructure, databases, and deployment stack**.

## PRE-FLIGHT CHECKS

Before researching, do these first:

1. **Dedup check**: Look for `tech-stacks/{company-slug}.md` in the current project directory. If it already exists, tell the user and ask whether to re-research or skip. Don't redo work silently.
2. **Gitignore**: If the current directory is a git repo, check whether `tech-stacks/` is in `.gitignore`. If not, add it — research output shouldn't be committed accidentally.
3. **Permissions**: This skill needs to run many searches and API calls without interruption. Before starting research, ask the user to approve these permissions for the session so work isn't blocked mid-research:
   - `WebSearch` — searching the web for job postings, blogs, case studies
   - `WebFetch` — fetching job boards (Greenhouse, Ashby), blog posts, documentation
   - `Bash(gh *)` — GitHub CLI to list repos, check forks, read dependency files
   - `Bash(mkdir *)` — creating the tech-stacks output directory
   - `Write` — saving the final research file
   - `Task` — launching parallel research agents

## CRITICAL RULES

Before you begin, internalize these hard-won lessons from researching 47+ companies:

### 1. The "Boring Stack" Trap
Companies hide their boring-but-real backend. Surge AI (Rails on Heroku), StackBlitz (Rails + PostgreSQL), Huntress (Rails primary), Figma (Ruby Sinatra), CrowdStrike (Rails for Risk Platform) — NONE of these advertise Rails. You must dig for it. Always ask: "What framework handles auth, billing, CRUD, and background jobs?"

### 2. TypeScript ≠ Backend
87% of startups use TypeScript, but only 36% use it on the backend. React/Next.js is frontend — it tells you NOTHING about the server. When you see TS, verify: is it frontend-only, or are there actual Node.js backend services?

### 3. SDK Language ≠ Platform Language
A Python SDK means their CUSTOMERS use Python. The platform itself could be Rails, Go, or anything else. Same for JS client libraries.

### 4. Python Inflation
68% of companies "use Python" but only 17-23% use it as their web backend (Django/FastAPI/Flask). The rest use it for ML training, scripting, and automation. Distinguish between "Python for ML pipelines" and "Python as the web backend."

### 5. GitHub Forks Are the Highest Signal
Forked repos reveal real infrastructure: sidekiq-cron = Ruby backend, celery forks = Python, devise/clearance = Rails auth, ember-toucan = Ember.js. Check forks BEFORE original repos.

### 6. Job Post Sources Matter
Greenhouse and Ashby have the most technical detail. Generic careers pages and LinkedIn have the least. Check ALL ATS platforms, not just the company website.

## RESEARCH PROCEDURE

Execute these phases IN ORDER. Do as many searches in parallel as possible within each phase.

### Phase 1: GitHub Organization (HIGHEST SIGNAL)
Search for the company's GitHub org. For each org found:
- List ALL repositories, especially forks
- Note programming languages for each repo
- Look at dependency files: Gemfile (Ruby), go.mod (Go), package.json (Node), Cargo.toml (Rust), requirements.txt (Python)
- **PAY SPECIAL ATTENTION TO FORKS** — these reveal real infrastructure deps

Fork cheat sheet:
| Fork | Implies |
|------|---------|
| Sidekiq, Resque, GoodJob | Ruby backend |
| Celery, Dramatiq | Python backend |
| BullMQ, Agenda | Node.js backend |
| Devise, Clearance, two_factor_authentication | Rails auth |
| fine_print | Rails app with legal agreements |
| sidekiq-heroku-autoscale | Rails on Heroku |
| pion/webrtc | Go real-time system |
| ember-toucan-* | Ember.js frontend (rare!) |

### Phase 2: Job Postings (check ALL sources in parallel)
Search these sources simultaneously:
1. `"{company}" software engineer jobs` (web search — add the current year to the query)
2. Fetch `job-boards.greenhouse.io/{company}` (direct ATS)
3. Fetch `jobs.ashbyhq.com/{company}` (direct ATS)
4. `site:builtin.com "{company}" engineer` (web search)
5. `"{company}" OR "{domain}" backend engineer requirements` (web search)
6. `site:news.ycombinator.com "{company}"` for HN Who's Hiring posts

For EACH job posting found, extract:
- Required/preferred programming languages
- Frameworks mentioned (Rails, Django, FastAPI, Express, etc.)
- Databases mentioned
- Infrastructure tools (Kubernetes, Docker, Terraform, etc.)
- Background job systems (Sidekiq, Celery, BullMQ)

### Phase 3: Engineering Blog & Architecture Posts
Search in parallel:
1. `"{company}" engineering blog tech stack` (web search)
2. `site:{domain} engineering OR blog architecture OR infrastructure` (web search)
3. `"{company}" CTO podcast tech stack` (web search)
4. `"{company}" site:newsletter.pragmaticengineer.com` (web search)
5. `"{company}" site:blog.bytebytego.com` (web search)

Look for:
- "How we built X" architecture posts
- Migration stories (reveal BOTH old and new stack)
- Database scaling posts
- "Why we chose X" posts

### Phase 4: Cloud Provider & Supporting Sources
Search in parallel:
1. `"{company}" site:cloud.google.com` (case studies)
2. `"{company}" site:aws.amazon.com` (case studies)
3. `"{company}" software engineer ruby OR rails OR python OR golang linkedin` (engineer profiles)
4. `"{company}" tech stack site:himalayas.app` (aggregator — weak signal)
5. `deepwiki.com/{github-org}/{repo}` if open-source

### Phase 5: Infrastructure Signal Detection
Search: `"{company}" OR "{domain}" heroku OR sidekiq OR celery OR kafka OR kubernetes`

Infrastructure → backend mapping:
| Signal | Likely Backend |
|--------|---------------|
| Heroku | Rails or Python |
| Sidekiq + Redis | Ruby |
| Celery + Redis/RabbitMQ | Python |
| Kafka | Go or Java consumers |
| Firecracker microVMs | Rust or Go |
| Temporal | Go or TS |
| eBPF | C/Go |

## ANALYSIS RULES

After gathering evidence, apply these checks:

1. **Separate frontend from backend.** React/Next.js/Vue = frontend. Don't count it as backend TypeScript.
2. **Separate SDKs from platform.** Client libraries tell you about customers, not internal stack.
3. **Separate ML from web backend.** Python for PyTorch training ≠ Python as the web server.
4. **Look for dual-language patterns.** Many companies run: boring stack (Rails/Django) for auth/billing/CRUD + performance stack (Go/Rust) for data ingestion/agents/real-time.
5. **Check for migrations.** If hiring for a new language while existing code is in another, flag the transition.
6. **Rate confidence.** High = multiple corroborating sources. Medium = one strong source. Low = inferred from SDKs/product type.

## OUTPUT FORMAT

Structure your findings EXACTLY like this:

```
## {Company} Tech Stack Assessment
**Researched**: {today's date, YYYY-MM-DD}

**What they do**: {1-2 sentence description of what the company builds/sells}

### Core Backend: {language/framework} (confidence: high/medium/low)
Evidence:
- [source 1 with URL]
- [source 2 with URL]

### Frontend: {framework}
Evidence:
- [source with URL]

### Databases: {primary OLTP, analytics, caching}
Evidence:
- [source with URL]

### Infrastructure: {cloud, orchestration, job queues, streaming}
Evidence:
- [source with URL]

### Supporting Services: {ML pipeline, data tools, etc.}
Evidence:
- [source with URL]

### Key Architectural Patterns
- [e.g., "Dual-language: Rails for platform, Go for agents"]
- [e.g., "Migration in progress: Python → Go"]

### Confidence Notes
- What's confirmed vs. inferred
- Any contradictory signals
- What couldn't be determined

### Sources
- [All URLs consulted, organized by tier]
```

## CATEGORY CALIBRATION

Use these industry patterns to set expectations before researching:

| Category | Typical Backend | Notes |
|----------|----------------|-------|
| AI/ML platforms | Python (FastAPI/Django) + Go | Python for ML, Go for infra |
| Cybersecurity | Go + Python | Go for agents, Python for detection |
| Developer tools | TypeScript OR Go | Newer = TS; infra-focused = Go |
| Data platforms | Go + Rust | Rust for storage engines |
| Design tools | Ruby + C++/WASM | Figma pattern |
| Security SaaS | Rails or Django + Go | Boring portal + performant agents |

## IMPORTANT REMINDERS

- **Do NOT stop after the first search.** Phase 1 alone is not enough. You MUST check job postings (Phase 2) and blogs (Phase 3) at minimum.
- **Do NOT trust a single source.** Cross-reference everything.
- **Do NOT skip GitHub forks.** This is where hidden stacks live.
- **Do NOT assume "no mention of X = they don't use X."** Rails is systematically underreported.
- **DO run searches in parallel** within each phase to be fast.
- **DO cite every source** with a URL so findings are verifiable.
- **DO save results** to `tech-stacks/{company-slug}.md` in the current project directory (create the directory if it doesn't exist). The slug should be lowercase, hyphenated (e.g., `crowdstrike.md`, `surge-ai.md`). Write the full output format to this file so results accumulate across sessions.
