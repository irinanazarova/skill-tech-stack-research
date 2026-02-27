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

## Category Calibration

Use these industry patterns to set expectations before researching:

| Category | Typical Backend | Notes |
|----------|----------------|-------|
| AI/ML platforms | Python (FastAPI/Django) + Go | Python for ML, Go for infra |
| Cybersecurity | Go + Python | Go for agents, Python for detection |
| Developer tools | TypeScript OR Go | Newer = TS; infra-focused = Go |
| Data platforms | Go + Rust | Rust for storage engines |
| Design tools | Ruby + C++/WASM | Figma pattern |
| Security SaaS | Rails or Django + Go | Boring portal + performant agents |
