# Signal Detection Reference

## Fork Cheat Sheet

Forked repos reveal real infrastructure dependencies. Check forks BEFORE original repos.

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

## Infrastructure → Backend Mapping

| Signal | Likely Backend |
|--------|---------------|
| Heroku | Rails or Python |
| Sidekiq + Redis | Ruby |
| Celery + Redis/RabbitMQ | Python |
| Kafka | Go or Java consumers |
| Firecracker microVMs | Rust or Go |
| Temporal | Go or TS |
| eBPF | C/Go |

## HTTP Header Detection

Rails apps expose distinctive HTTP headers even behind SPAs and CDNs:

| Header/Cookie | Implies |
|---------------|---------|
| `x-runtime: 0.012345` | Rails (this header is Rails-specific) |
| `_appname_session` cookie | Rails session cookie convention |
| `x-request-id` + `x-runtime` together | Very strong Rails signal |
| Default security headers (`x-frame-options`, `x-xss-protection`, `x-content-type-options`, `x-permitted-cross-domain-policies`) all present | Rails default middleware |
| Asset fingerprinting: `application-{hash}.css` | Rails asset pipeline (Sprockets/Propshaft) |
| `data-turbo-frame`, `data-controller` in HTML | Hotwire (Rails 7+) |

**Tip:** Run `curl -sI https://app.{domain}` to check headers. This works even when the frontend is a decoupled React SPA — the API responses still carry Rails headers.

## Category Calibration — USE WITH EXTREME CAUTION

These patterns exist but MUST NOT be used to infer or guess a backend. They are only useful for knowing what extra searches to run. Real-world counterexamples are everywhere:

| Category | Common Backend | Known Counterexamples |
|----------|---------------|----------------------|
| AI/ML platforms | Python + Go | Braintrust = Rust + TS |
| Drones/CV/IoT | Python | dScribe AI = Ruby on Rails |
| Cybersecurity | Go + Python | Tines = Ruby on Rails |
| Developer tools | TypeScript OR Go | Figma = Ruby Sinatra, StackBlitz = Rails |
| Fintech/Accounting | varies | Finta = Rails (behind React SPA) |
| Data platforms | Go + Rust | — |
| Design tools | varies | Figma = Ruby + C++/WASM |
| Security SaaS | Rails or Django + Go | — |

**Rule: If your research finds no evidence of the backend, report "Unknown" — never default to the "Common Backend" column above.**
