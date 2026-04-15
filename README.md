# The Engineering Playbook

Practical guides to software architecture, backend engineering, and DevOps. Patterns, trade-offs, and real-world examples.

**Live site:** https://fk78.github.io/engineering-playbook/

## Contents

| Part | Topic | Covers |
|------|-------|--------|
| 1 | Foundations | Layered architecture, coupling/cohesion, separation of concerns, DAO & Repository, testing strategies |
| 2 | API & Domain | REST, GraphQL, domain modeling, entities, value objects, aggregates, bounded contexts |
| 3 | Events & CQRS | Event-driven architecture, message queues vs streams, CQRS, event sourcing, Saga pattern |
| 4 | Distributed Systems | CAP theorem, consistency models, consensus, microservices trade-offs, circuit breaker, observability |
| 5 | Security Architecture | Authentication/authorization, OAuth 2.0/OIDC, zero trust, API security, common vulnerabilities |
| 6 | Cloud & Infrastructure | Load balancing, auto-scaling, CDNs, containers, IaC, CI/CD, disaster recovery |
| 7 | Data Architecture | Database selection, caching strategies, sharding, replication, data pipelines, ACID vs BASE |
| 8 | System Design Practice | End-to-end system design (URL shortener, chat app, e-commerce), patterns cheat sheet, interview tips |

## Features

- Code examples in TypeScript and Python
- Interactive quizzes with AI-powered grading (bring your own OpenAI API key) and local fallback
- Built with Hugo and the Docsy theme
- Deployed to GitHub Pages

## Running locally

```bash
# Prerequisites: Hugo (extended), Go, Node.js
npm install
hugo server -D
```

## Tech stack

- [Hugo](https://gohugo.io/) — static site generator
- [Docsy](https://www.docsy.dev/) — documentation theme
- [OpenAI API](https://platform.openai.com/) — optional, for AI quiz grading
