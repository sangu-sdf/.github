## Software Development Fundamentals — Course Overview

## Introduction

Welcome to **Software Development Fundamentals (SDF)**. This course bridges the gap between writing code that *works* and building software *professionally*. Writing correct code is only the beginning — real-world development demands clean design, robust error handling, version control, automated testing, and reliable deployment.

All concepts are language-agnostic in spirit, but we use **Java** as our implementation language throughout.

---

## Why This Course?

Consider two developers. Both can write a sorting function. Developer A writes it, it works, they move on. Developer B writes it with a clear name, handles edge cases, writes unit tests, commits with a meaningful message, and documents its usage.

Six months later, when a bug is reported, Developer A's code is nearly impossible to understand — even by Developer A. Developer B fixes it in minutes.

The difference is not talent — it is **discipline and knowledge of fundamentals**. This course teaches you to be Developer B.

---

## Course Structure

The course is split into two halves, separated by a midterm exam.

### First Half: Writing Quality Code and Managing It

| # | Topic | What You'll Learn |
|---|---|---|
| 1 | [**Clean Coding**](https://github.com/sangu-sdf/.github/blob/main/profile/01-clean-coding.md) | Naming, SRP, SOLID principles, coding conventions |
| 2 | [**Error Handling & Debugging**](https://github.com/sangu-sdf/.github/blob/main/profile/02-error-handling-and-debugging.md) | Exceptions (`try`/`catch`/`finally`), breakpoints, step-through debugging |
| 3 | **Git #1** | Repos, commits, push/pull, GitHub basics |
| 4 | **Git #2** | Branching, merging, rebasing, resolving conflicts |
| 5 | **Documentation** | Javadoc, code comments, GitHub Pages |
| 6 | **Logging** | Log levels, logging frameworks, best practices |

### Second Half: Testing, Methodology, and Deployment

| # | Topic | What You'll Learn |
|---|---|---|
| 7 | **TDD & Unit Testing** | JUnit, Red-Green-Refactor cycle |
| 8 | **Software Management** | Scrum, Kanban, GitHub Projects |
| 9 | **SDLC Models** | Agile, Waterfall, and when to use each |
| 10 | **Docker #1** | Containers, Dockerfiles, images |
| 11 | **Docker #2** | Multi-container apps with Docker Compose |
| 12 | **CI/CD** | Automated build, test, and deploy pipelines |

---

## How the Topics Connect

These topics form a continuous workflow — each one builds on the last:

```
Clean Code → Error Handling → Git (Commit → Branch/Merge)
    → Documentation → Logging → Tests (TDD)
        → Methodology (Scrum) → SDLC
            → Docker → CI/CD
```

Clean code is easier to test. Tested code is safer to deploy. Version-controlled code can be deployed automatically. The full chain is what transforms a solo coding exercise into professional software engineering.

---

## What This Course Is and Is Not

**This course IS:**
- Practical — you write code and use real tools every chapter
- Industry-relevant — every topic appears in professional software roles
- Focused on fundamentals that stay relevant across languages and frameworks

**This course is NOT:**
- A Java language course — basic syntax is assumed
- A CS theory course — no algorithms or complexity analysis
- An advanced architecture course — we touch on SOLID but not enterprise patterns

---

## Tools and Technologies

| Tool | Purpose |
|---|---|
| **Java (JDK 17+)** | Primary language |
| **IntelliJ IDEA or VS Code** | IDE |
| **Git** | Version control |
| **GitHub** | Remote repos, project management, Pages |
| **JUnit 5** | Unit testing |
| **Docker** | Containerization |
| **Docker Compose** | Multi-container orchestration |
| **GitHub Actions** | CI/CD pipelines |

---

## How to Succeed

1. **Practice consistently.** Reading about Git or clean code is not enough — use the tools repeatedly.
2. **Read error messages carefully.** They are clues, not obstacles.
3. **Ask "why" before "how."** Understanding the reason behind a practice helps you apply it in new situations.
4. **Collaborate.** Review each other's code and learn from different perspectives.
5. **Build something.** Apply these concepts to a project you care about.
