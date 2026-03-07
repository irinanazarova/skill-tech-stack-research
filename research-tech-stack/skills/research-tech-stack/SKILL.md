---
name: research-tech-stack
description: Research a startup's real tech stack by triangulating GitHub repos, job postings, engineering blogs, and other sources. Use when asked about a company's technology, backend language, infrastructure, or engineering stack.
argument-hint: [company-name or domain]
disable-model-invocation: true
allowed-tools: WebSearch, WebFetch, Bash(gh *), Bash(mkdir *), Bash(curl *), Read, Write, Glob, Grep, Task
---

# Tech Stack Research Skill

You are researching the **real** tech stack of: **$ARGUMENTS**

Your goal is to determine what technologies this company actually uses in production — not what they market, not what their SDKs are written in, not what their frontend uses. The **real backend, infrastructure, databases, and deployment stack**.

Source: https://github.com/irinanazarova/skill-tech-stack-research

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

### 5. NEVER Infer Backend from Industry/Domain
A drone company does NOT mean Python. A fintech does NOT mean Java. An AI company does NOT mean Python backend. Industry category tells you NOTHING about the web backend framework. dScribe AI builds autonomous drones with computer vision — their backend is Ruby on Rails. Figma is a design tool — their backend is Ruby Sinatra. Domain-based guesses are wrong more often than they're right. If you have no evidence, say "Unknown" — do NOT guess based on what seems typical for the industry.

### 5b. React SPA Frontend Does NOT Rule Out Rails
A React/CRA/Next.js frontend with no Turbolinks, no CSRF meta tags, and no Rails asset fingerprinting does NOT mean "not Rails." Many Rails apps serve as API-only backends behind decoupled React SPAs. In this architecture, you will see ZERO Rails indicators in the HTML. Finta (YC) has a React CRA + Ant Design frontend — but is confirmed Rails via sfruby.com job posting and SF Ruby conference demo. NEVER conclude "not Rails" based solely on frontend evidence.

### 6. GitHub Forks Are the Highest Signal
Forked repos reveal real infrastructure: sidekiq-cron = Ruby backend, celery forks = Python, devise/clearance = Rails auth, ember-toucan = Ember.js. Check forks BEFORE original repos. See [signals.md](signals.md) for the full fork cheat sheet.

### 7. Job Post Sources Matter
Greenhouse and Ashby have the most technical detail. Generic careers pages and LinkedIn have the least. Check ALL ATS platforms, not just the company website.

## RESEARCH PROCEDURE

Execute these phases IN ORDER. Do as many searches in parallel as possible within each phase.

### Phase 1: GitHub Organization (HIGHEST SIGNAL)
Search for the company's GitHub org. For each org found:
- List ALL repositories, especially forks
- Note programming languages for each repo
- Look at dependency files: Gemfile (Ruby), go.mod (Go), package.json (Node), Cargo.toml (Rust), requirements.txt (Python)
- **PAY SPECIAL ATTENTION TO FORKS** — these reveal real infrastructure deps. See [signals.md](signals.md) for the fork cheat sheet.

### Phase 2: Job Postings (check ALL sources in parallel)
Search these sources simultaneously:
1. `"{company}" software engineer jobs` (web search — add the current year to the query)
2. Fetch `job-boards.greenhouse.io/{company}` (direct ATS)
3. Fetch `jobs.ashbyhq.com/{company}` (direct ATS)
4. `site:builtin.com "{company}" engineer` (web search)
5. `"{company}" OR "{domain}" backend engineer requirements` (web search)
6. `site:news.ycombinator.com "{company}"` for HN Who's Hiring posts
7. `site:sfruby.com "{company}"` — SF Ruby community job board (high signal for hidden Rails companies)
8. `site:rubyonremote.com "{company}"` — Ruby-specific remote job board

For EACH job posting found, extract:
- Required/preferred programming languages
- Frameworks mentioned (Rails, Django, FastAPI, Express, etc.)
- Databases mentioned
- Infrastructure tools (Kubernetes, Docker, Terraform, etc.)
- Background job systems (Sidekiq, Celery, BullMQ)

### Phase 3: Conference Talks & Meetups (HIGH SIGNAL)
Search in parallel:
1. `"{company}" site:rubyevents.org` (Ruby conference/meetup talks)
2. `"{company}" site:railsconf.org OR site:rubyconf.org` (major Ruby conferences)
3. `"{company}" site:gophercon.com OR site:golangevents.com` (Go conferences)
4. `"{company}" site:rustconf.com` (Rust conferences)
5. `"{company}" founder OR CTO meetup talk` (web search)

Conference talks are extremely high signal — founders presenting at language-specific meetups confirms the stack. dScribe AI (a drone company) was found to be Rails because their CTO presented at SF Ruby meetup.

### Phase 4: Engineering Blog & Architecture Posts
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

### Phase 5: Cloud Provider & Supporting Sources
Search in parallel:
1. `"{company}" site:cloud.google.com` (case studies)
2. `"{company}" site:aws.amazon.com` (case studies)
3. `"{company}" software engineer ruby OR rails OR python OR golang linkedin` (engineer profiles)
4. `"{company}" tech stack site:himalayas.app` (aggregator — weak signal)
5. `deepwiki.com/{github-org}/{repo}` if open-source

### Phase 6: Infrastructure Signal Detection
Search: `"{company}" OR "{domain}" heroku OR sidekiq OR celery OR kafka OR kubernetes`

See [signals.md](signals.md) for infrastructure → backend mapping and category calibration tables.

## ANALYSIS RULES

After gathering evidence, apply these checks:

1. **Separate frontend from backend.** React/Next.js/Vue = frontend. Don't count it as backend TypeScript.
2. **Separate SDKs from platform.** Client libraries tell you about customers, not internal stack.
3. **Separate ML from web backend.** Python for PyTorch training ≠ Python as the web server.
4. **Look for dual-language patterns.** Many companies run: boring stack (Rails/Django) for auth/billing/CRUD + performance stack (Go/Rust) for data ingestion/agents/real-time.
5. **Check for migrations.** If hiring for a new language while existing code is in another, flag the transition.
6. **Rate confidence.** High = multiple corroborating sources. Medium = one strong source. Low = inferred from SDKs/product type.
7. **Never conclude "not X" from absence of frontend evidence.** A decoupled SPA frontend tells you nothing about the backend.

## OUTPUT FORMAT

See [template.md](template.md) for the full output template.

Save results to `tech-stacks/{company-slug}.md` (create the directory if it doesn't exist). The slug should be lowercase, hyphenated (e.g., `crowdstrike.md`, `surge-ai.md`).

## IMPORTANT REMINDERS

- **Do NOT stop after the first search.** Phase 1 alone is not enough. You MUST check job postings (Phase 2) and blogs (Phase 3) at minimum.
- **Do NOT trust a single source.** Cross-reference everything.
- **Do NOT skip GitHub forks.** This is where hidden stacks live.
- **Do NOT assume "no mention of X = they don't use X."** Rails is systematically underreported.
- **Do NOT guess backend from industry.** Drone company ≠ Python. AI company ≠ Python backend. Fintech ≠ Java. If you didn't find evidence, say "Unknown."
- **Do NOT conclude "not Rails" from a React/SPA frontend.** Rails API backends behind React SPAs have zero Rails indicators in the HTML.
- **DO run searches in parallel** within each phase to be fast.
- **DO cite every source** with a URL so findings are verifiable.
- **DO save results** to `tech-stacks/{company-slug}.md`.
