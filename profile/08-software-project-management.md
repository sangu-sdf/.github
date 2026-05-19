# Chapter 8: Software Project Management

## Introduction

Writing code is only one part of building software. In fact, many software projects fail not because of technical problems, but because of poor planning, unclear communication, unrealistic deadlines, and mismanaged resources. The history of software development is littered with projects that ran over budget, shipped years late, or were abandoned entirely -- not because the developers were incompetent, but because nobody managed the project effectively.

**Software project management** is the discipline of planning, organizing, coordinating, and controlling the activities involved in building software. It bridges the gap between a great idea and a successfully delivered product. Whether you are a solo developer working on a side project or part of a 200-person engineering team at a major company, understanding project management principles will make you more effective and your projects more successful.

In this chapter, you will learn what software project management is and why it matters, explore the phases of the software development lifecycle (SDLC), understand the Agile methodology and its core values, and dive into two of the most popular Agile frameworks: **Scrum** and **Kanban**. You will also learn about practical tools like Trello and GitHub Projects that help teams put these methodologies into practice.

---

## 8.1 What Is Software Project Management?

### 8.1.1 Definition

**Software project management** refers to the planning, scheduling, organizing, and controlling of a software development project. It encompasses everything needed to ensure that software is delivered on time, within budget, and to the required quality standards.

This is not limited to the technology industry. Project management practices exist across all industries -- manufacturing, construction, finance, marketing, healthcare -- but software projects have unique characteristics that make them especially challenging to manage:

- **Intangibility:** Unlike a building or a car, software has no physical form. It is difficult to measure progress when you cannot see the product taking shape.
- **Complexity:** Modern software systems involve millions of lines of code, dozens of dependencies, and intricate interactions between components.
- **Rapid change:** Technology evolves quickly. The tools, frameworks, and best practices you use today may be outdated in a few years.
- **Human factors:** Software is built by people, and people have different skill levels, communication styles, and productivity patterns.

### 8.1.2 Why Is Software Project Management Important?

Software projects face a unique set of risks and challenges:

- **Frequent technology changes:** New frameworks, languages, and tools emerge constantly.
- **Immature technology:** Teams sometimes build on cutting-edge technology that is not yet stable.
- **Sloppy development practices:** Without management discipline, code quality tends to degrade over time.
- **Staff turnover:** Developers change jobs frequently, taking knowledge with them.
- **Unclear requirements:** Stakeholders often do not know exactly what they want until they see it.

Software project management helps overcome these challenges by:

- **Inspiring involvement:** Getting end users and stakeholders engaged in the process, so requirements are clear and feedback is continuous.
- **Managing risks:** Identifying potential problems early and creating mitigation strategies.
- **Creating transparency:** Ensuring everyone knows the project status, what is done, what is in progress, and what remains.
- **Setting realistic goals:** Using historical data and estimation techniques to create achievable timelines.
- **Ensuring quality:** Building quality assurance into the process, not treating it as an afterthought.

Without project management, a software team is essentially working in chaos -- writing code without a clear plan, with no visibility into progress, and no coordinated effort to deliver a working product.

---

## 8.2 Types of Software Projects

Software projects come in many forms, each with its own characteristics, constraints, and management needs. Understanding the type of project you are working on helps you choose the right management approach.

### 8.2.1 New Software Products

These are greenfield projects where a software vendor or startup builds a completely new product from scratch. Examples include launching a new SaaS platform, building a new mobile app, or creating a new developer tool.

**Characteristics:** High uncertainty, flexible requirements, need for rapid prototyping, significant upfront design decisions.

### 8.2.2 Updates, Patches, and Version Releases

The majority of software work is not building new products -- it is maintaining and improving existing ones. This includes bug fixes, security patches, feature enhancements, and major version upgrades.

**Characteristics:** Existing codebase to work within, backward compatibility concerns, established user base with expectations, regression risk.

### 8.2.3 Mobile Applications

Mobile app development has its own set of constraints: multiple platforms (iOS, Android), device fragmentation, app store review processes, offline capability requirements, and battery/performance constraints.

**Characteristics:** Platform-specific requirements, frequent release cycles, UI/UX emphasis, app store compliance.

### 8.2.4 Internal (Homegrown) Software

Many organizations build custom software for internal use: inventory management systems, HR tools, reporting dashboards, or workflow automation. These projects serve internal users rather than external customers.

**Characteristics:** Known user base, tighter integration with existing systems, less formal UI requirements, business-specific logic.

---

## 8.3 The Software Development Lifecycle (SDLC)

### 8.3.1 What Is the SDLC?

The **Software Development Lifecycle (SDLC)** is a structured framework that defines the activities and phases involved in developing software. It provides a systematic approach to building software that meets quality standards and user expectations.

The SDLC was standardized in large part by the **Project Management Institute (PMI)**, which defined five phases of project management. For software specifically, the lifecycle is commonly described in **six phases**.

Depending on the methodology you use (Agile, Waterfall, iterative, V-shaped, spiral, or hybrid), these phases may be linear (completed one after another), overlapping, or repeated in cycles. In modern Agile development, teams cycle through these phases rapidly and repeatedly.

### 8.3.2 The Six Phases

#### Phase 1: Initiation and Planning

This is where the project begins. The team defines the project's purpose, scope, objectives, timeline, and budget. Key activities include:

- Identifying stakeholders and their needs
- Defining the project scope (what is included and what is not)
- Creating a high-level project plan
- Estimating resources, costs, and timelines
- Identifying risks and creating mitigation plans
- Getting approval to proceed (the "green light")

**Output:** Project charter, initial project plan, resource allocation.

#### Phase 2: Requirements

During this phase, the team gathers and documents detailed requirements -- what the software must do, how it must behave, and what constraints it must operate within.

- Conduct interviews with stakeholders and end users
- Document functional requirements (what the system should do)
- Document non-functional requirements (performance, security, scalability)
- Create user stories or use cases
- Prioritize requirements

**Output:** Requirements specification document, user stories, acceptance criteria.

#### Phase 3: Design

The design phase translates requirements into a technical blueprint. This includes both high-level architecture and detailed component design.

- Define system architecture (monolithic, microservices, serverless, etc.)
- Design the database schema
- Design APIs and interfaces between components
- Create UI/UX wireframes and mockups
- Choose technology stack (programming languages, frameworks, tools)
- Plan for security, scalability, and maintainability

**Output:** Architecture diagrams, database schemas, API specifications, UI mockups.

#### Phase 4: Build (Implementation)

This is where the actual coding happens. Developers write the code according to the design specifications.

- Write source code following coding standards
- Implement unit tests alongside the code
- Conduct code reviews
- Integrate code into a shared repository using version control
- Build and deploy to development/staging environments

**Output:** Working source code, unit tests, build artifacts.

#### Phase 5: Testing

Although testing should happen throughout development (especially in Agile), this phase represents the focused, comprehensive testing effort before release.

- Execute test plans covering all types of testing (unit, integration, system, UAT)
- Track and resolve defects
- Perform regression testing after fixes
- Validate against requirements and acceptance criteria
- Get sign-off from stakeholders

**Output:** Test results, defect reports, sign-off approval.

#### Phase 6: Implementation (Deployment)

The final phase delivers the software to its users and transitions it to operational status.

- Deploy to production environment
- Migrate data if necessary
- Train end users
- Create documentation and user guides
- Set up monitoring and alerting
- Plan for ongoing maintenance and support

**Output:** Deployed software, user documentation, support plan.

### 8.3.3 Iterative vs. Linear

In the traditional **Waterfall** model, these six phases happen sequentially -- you complete one before starting the next, and you do not go back. This works well when requirements are stable and well-understood (like building a bridge), but it is poorly suited for software where requirements change frequently.

In **Agile** approaches, teams cycle through these phases rapidly (often in 1-4 week iterations), delivering small increments of working software each time. This allows for continuous feedback and adaptation.

---

## 8.4 Best Practices for Software Project Managers

Regardless of the methodology you use, certain best practices contribute to project success:

### 8.4.1 Understand and Articulate Goals

Before writing a single line of code, make sure the entire team understands:
- What problem are we solving?
- Who are we solving it for?
- What does success look like?
- What are the constraints (time, budget, technology)?

A team that does not understand the "why" behind their work will make poor decisions at every level.

### 8.4.2 Gather Team Feedback

The people doing the work have the best understanding of what is realistic, what challenges exist, and what improvements could be made. Regular feedback loops -- through retrospectives, one-on-ones, and open forums -- are essential.

### 8.4.3 Break Down the Plan into Daily Tasks

A project plan that says "Build the user management module" is too vague. Break it down into specific, concrete tasks:
- Create the User database table
- Implement the User entity class
- Build the registration API endpoint
- Write unit tests for the registration service
- Create the registration form UI

Each task should be small enough to complete in a day or less.

### 8.4.4 Stick to the Plan (But Be Flexible)

Discipline is important, but so is adaptability. A good project manager follows the plan but makes adjustments when the situation demands it -- new information, changed requirements, unexpected technical challenges.

### 8.4.5 Share Successes

Celebrating wins -- even small ones -- builds morale and motivation. When the team completes a sprint, ships a feature, or fixes a critical bug, acknowledge the achievement.

---

## 8.5 Agile Methodology

### 8.5.1 What Is Agile?

**Agile** is a project management approach that breaks the project into small phases and emphasizes continuous collaboration, feedback, and improvement. Instead of planning everything upfront and building the entire product before getting feedback, Agile teams deliver small, working increments of the product frequently, incorporating feedback after each delivery.

The key differences between Agile and traditional (Waterfall) approaches:

| Aspect | Waterfall | Agile |
|---|---|---|
| Planning | All upfront | Continuous, just enough |
| Delivery | One big release at the end | Small, frequent releases |
| Feedback | After delivery | Continuous throughout |
| Change | Resisted (scope is fixed) | Embraced (scope is flexible) |
| Documentation | Heavy and detailed | Lightweight and practical |
| Team structure | Siloed specialists | Cross-functional teams |
| Customer involvement | Beginning and end | Throughout the project |

Agile is not a single methodology -- it is a family of methodologies that share common values. The most popular Agile frameworks include **Scrum**, **Kanban**, **Extreme Programming (XP)**, and **Lean**.

### 8.5.2 The Agile Manifesto

The **Agile Manifesto**, published in 2001, is the foundational document of the Agile movement. It was written by 17 software developers who were frustrated with the heavyweight, bureaucratic processes that dominated the industry. The manifesto established four core values:

**1. Individuals and interactions over processes and tools**

While processes and tools are important, they should serve the people, not the other way around. A great team with mediocre tools will outperform a mediocre team with great tools. Face-to-face communication, trust, and collaboration are more valuable than rigid processes.

**2. Working software over comprehensive documentation**

Documentation has its place, but the primary measure of progress is working software. A 200-page design document means nothing if the software does not work. Deliver working software frequently and keep documentation lightweight and practical.

**3. Customer collaboration over contract negotiation**

Instead of hiding behind a contract and delivering whatever was specified at the start (even if the customer's needs have changed), Agile teams collaborate with customers throughout the project. Regular feedback ensures the team is building the right thing.

**4. Responding to change over following a plan**

Plans are important, but they should not be treated as sacred. When new information emerges -- changed requirements, market shifts, technical discoveries -- the team should adapt rather than blindly following an outdated plan.

> **Important:** The manifesto says "over," not "instead of." Agile does not reject processes, documentation, contracts, or plans. It simply values the items on the left more than the items on the right.

### 8.5.3 The Twelve Principles of Agile

Behind the four values are twelve supporting principles:

1. Our highest priority is to satisfy the customer through early and continuous delivery of valuable software.
2. Welcome changing requirements, even late in development.
3. Deliver working software frequently, from a couple of weeks to a couple of months, with a preference for the shorter timescale.
4. Business people and developers must work together daily throughout the project.
5. Build projects around motivated individuals. Give them the environment and support they need, and trust them to get the job done.
6. The most efficient method of conveying information is face-to-face conversation.
7. Working software is the primary measure of progress.
8. Agile processes promote sustainable development. The sponsors, developers, and users should be able to maintain a constant pace indefinitely.
9. Continuous attention to technical excellence and good design enhances agility.
10. Simplicity -- the art of maximizing the amount of work not done -- is essential.
11. The best architectures, requirements, and designs emerge from self-organizing teams.
12. At regular intervals, the team reflects on how to become more effective, then tunes and adjusts its behavior accordingly.

### 8.5.4 Why Choose Agile?

Teams choose Agile because it allows them to:

- **Respond quickly to change:** Market conditions shift, competitors release new features, and customer needs evolve. Agile teams can adapt in weeks, not months.
- **Reduce waste:** By delivering in small increments and getting frequent feedback, teams avoid building features nobody wants.
- **Improve quality:** Continuous testing, code reviews, and refactoring keep the codebase healthy.
- **Increase visibility:** Regular demos and status updates keep stakeholders informed.
- **Boost team morale:** Self-organizing teams with autonomy and trust are more engaged and productive.

### 8.5.5 Agile: Past, Present, and Future

Since the publication of the Agile Manifesto in 2001, Agile has grown from a niche practice among software developers to the dominant approach across industries. Many teams today combine practices from multiple frameworks (Scrum ceremonies with Kanban boards, for example), adapting the methodology to their specific context.

The future of Agile is moving toward greater autonomy and trust. Companies that attract the best talent are those that give teams the freedom to choose their own practices, as long as they are guided by Agile principles. The specific framework matters less than the mindset.

---

## 8.6 Scrum

### 8.6.1 What Is Scrum?

**Scrum** is the most popular Agile framework. It provides a structured set of roles, ceremonies (events), and artifacts that help teams organize their work, collaborate effectively, and deliver value incrementally.

The name comes from rugby, where a "scrum" is a formation where the team works together to move the ball forward. In software development, Scrum encourages teams to learn through experience, self-organize while working on problems, and reflect on their successes and failures to continuously improve.

While Scrum was originally developed for software teams, its principles apply to any kind of teamwork -- marketing campaigns, product launches, research projects, and more.

### 8.6.2 Scrum Roles

Scrum defines three specific roles, each with clear responsibilities:

#### Product Owner

The **Product Owner** is the voice of the customer and the business. They are responsible for maximizing the value of the product by managing the **Product Backlog** -- the prioritized list of features, bug fixes, and improvements that the team will work on.

Key responsibilities:
- Define and prioritize the product backlog
- Write clear user stories with acceptance criteria
- Make decisions about what to build and in what order
- Accept or reject completed work
- Communicate the product vision to the team

The Product Owner does not tell the team *how* to do the work -- they define *what* needs to be done and *why*.

#### Scrum Master

The **Scrum Master** is a servant-leader who ensures the team follows Scrum practices, removes obstacles that block progress, and facilitates team ceremonies.

Key responsibilities:
- Facilitate Scrum ceremonies (sprint planning, daily standup, review, retrospective)
- Remove impediments that block the team's progress
- Coach the team on Scrum practices and Agile principles
- Shield the team from external interruptions
- Help the team improve their processes

The Scrum Master is not a project manager or a boss. They do not assign tasks or make decisions for the team. They serve the team by creating an environment where the team can do its best work.

#### Development Team

The **Development Team** consists of the professionals who do the actual work of building the product. In software, this includes programmers, designers, testers, and anyone else needed to deliver a working increment of the product.

Key characteristics:
- **Cross-functional:** The team has all the skills needed to deliver the product increment.
- **Self-organizing:** The team decides how to accomplish the work, not management.
- **Small:** Ideally 3-9 people. Large enough to be capable, small enough to remain agile.
- **No titles:** Within the team, everyone is a "Developer," regardless of their specialty.

### 8.6.3 Scrum Ceremonies (Events)

Scrum defines five formal events that create regularity and minimize the need for ad-hoc meetings:

#### Sprint

The **Sprint** is the heartbeat of Scrum. It is a fixed-length timebox (typically 1-4 weeks, most commonly 2 weeks) during which the team builds a potentially releasable product increment.

Rules of the Sprint:
- The Sprint length stays consistent (do not change it every sprint).
- No changes are made that would endanger the Sprint Goal.
- Quality standards do not decrease.
- The scope can be clarified and renegotiated between the Product Owner and Development Team.

#### Sprint Planning

At the beginning of each Sprint, the team holds a **Sprint Planning** meeting to decide what to work on and how to accomplish it.

The meeting answers two questions:
1. **What can we deliver in this Sprint?** The team selects items from the Product Backlog based on priority and their capacity.
2. **How will we get it done?** The team breaks selected items into tasks and creates a plan for the Sprint.

The output is the **Sprint Backlog** -- the list of items the team commits to delivering, plus a plan for how to deliver them.

**Duration:** Up to 4 hours for a 2-week sprint.

#### Daily Standup (Daily Scrum)

Every day during the Sprint, the team holds a brief **Daily Standup** meeting (15 minutes maximum). Each team member answers three questions:

1. **What did I do yesterday?** (What progress did I make?)
2. **What will I do today?** (What do I plan to work on?)
3. **Are there any impediments?** (Is anything blocking my progress?)

The Daily Standup is not a status report to management -- it is a synchronization mechanism for the team. It helps team members coordinate their work and identify blockers early.

**Rules:**
- Same time, same place, every day
- Maximum 15 minutes
- Everyone stands (to keep it short)
- Only the Development Team members speak (others may observe)

#### Sprint Review

At the end of each Sprint, the team holds a **Sprint Review** to demonstrate the work they completed. The team shows the working software to stakeholders, gets feedback, and discusses what to work on next.

Key activities:
- Demo of completed features
- Discussion of what went well and what did not
- Review and update of the Product Backlog based on feedback
- Assessment of the marketplace, timeline, and budget

**Duration:** Up to 2 hours for a 2-week sprint.

#### Sprint Retrospective

After the Sprint Review, the team holds a **Sprint Retrospective** -- an internal meeting to reflect on the Sprint and identify improvements.

The retrospective typically answers three questions:
1. **What went well?** (What should we keep doing?)
2. **What did not go well?** (What should we stop doing?)
3. **What can we improve?** (What should we start doing?)

The team selects a few actionable improvements and commits to implementing them in the next Sprint. The retrospective is one of the most valuable Scrum ceremonies because it creates a culture of continuous improvement.

**Duration:** Up to 1.5 hours for a 2-week sprint.

### 8.6.4 Scrum Artifacts

#### Product Backlog

An ordered list of everything that is known to be needed in the product. It is the single source of requirements and is owned by the Product Owner. Items at the top are refined (detailed, estimated, prioritized) while items at the bottom may be rough ideas.

#### Sprint Backlog

The set of Product Backlog items selected for the Sprint, plus a plan for delivering them. It is owned by the Development Team and updated daily as the team learns more about the work.

#### Product Increment

The sum of all Product Backlog items completed during the Sprint and all previous Sprints. Each increment must be in a usable condition and meet the team's "Definition of Done."

### 8.6.5 A Sprint in Practice

Here is what a typical 2-week Sprint looks like:

```
Day 1 (Monday):    Sprint Planning (morning)
                   Team begins working on Sprint Backlog items

Day 2-9:           Daily Standups (15 min each morning)
                   Development, testing, code reviews
                   Product Owner available for questions

Day 10 (Friday):   Sprint Review (demo to stakeholders)
                   Sprint Retrospective (team reflection)
                   Sprint ends

Day 11 (Monday):   Next Sprint Planning begins
```

---

## 8.7 Kanban

### 8.7.1 What Is Kanban?

**Kanban** is an Agile framework that focuses on visualizing work, limiting work in progress, and maximizing flow. Unlike Scrum, which operates in fixed-length sprints, Kanban uses a continuous flow model where work items are pulled through the system as capacity becomes available.

The word "Kanban" is Japanese and translates to "visual signal" or "card." The core idea is simple: make all work visible, limit how much work is happening at once, and continuously improve the flow.

Kanban is particularly popular among service-oriented teams (IT support, DevOps, HR) and teams that handle a lot of unplanned work, but it works equally well for software development.

### 8.7.2 The Kanban Board

The centerpiece of Kanban is the **Kanban board** -- a visual representation of the workflow. The simplest board has three columns:

```
+---------------+---------------+---------------+
|   To Do       |  In Progress  |    Done       |
+---------------+---------------+---------------+
| [Task A]      | [Task D]      | [Task G]      |
| [Task B]      | [Task E]      | [Task H]      |
| [Task C]      |               | [Task I]      |
|               |               | [Task J]      |
+---------------+---------------+---------------+
```

Most teams customize their boards with additional columns that reflect their actual workflow:

```
+----------+----------+-----------+-----------+----------+----------+
| Backlog  |  To Do   | In Dev    | In Review | Testing  |   Done   |
+----------+----------+-----------+-----------+----------+----------+
| [Task A] | [Task D] | [Task G]  | [Task I]  | [Task K] | [Task M] |
| [Task B] | [Task E] | [Task H]  |           |          | [Task N] |
| [Task C] | [Task F] |           |           |          |          |
+----------+----------+-----------+-----------+----------+----------+
```

Each card on the board represents a single work item (a feature, bug fix, task, or user story). Cards move from left to right as they progress through the workflow.

### 8.7.3 Work-in-Progress (WIP) Limits

The most important concept in Kanban is the **WIP limit** -- a cap on the number of items that can be in any column at the same time. WIP limits prevent overloading the team and expose bottlenecks in the process.

```
+----------+----------+-----------+-----------+----------+
| Backlog  | To Do    | In Dev    | In Review | Done     |
|          | WIP: 5   | WIP: 3   | WIP: 2   |          |
+----------+----------+-----------+-----------+----------+
| [Task A] | [Task D] | [Task G]  | [Task I]  | [Task K] |
| [Task B] | [Task E] | [Task H]  | [Task J]  | [Task L] |
| [Task C] | [Task F] | [Task *]  |           |          |
+----------+----------+-----------+-----------+----------+
```

In this example, the "In Dev" column has a WIP limit of 3, and it is full. This means no new task can be pulled into development until one of the current tasks moves to "In Review." If "In Review" is also full, it creates back-pressure that forces the team to finish existing work before starting new work.

**Why WIP limits matter:**

- **Reduce context switching:** Developers who work on too many things at once are less productive because context switching is expensive.
- **Expose bottlenecks:** If a column is always at its WIP limit, it indicates a bottleneck that needs attention. Maybe you need more reviewers, or the testing process needs improvement.
- **Improve quality:** Focusing on fewer items at a time leads to better attention to detail and fewer mistakes.
- **Increase throughput:** Counterintuitively, doing less work in parallel often results in completing more work overall (because items spend less time waiting).

### 8.7.4 Kanban Principles

Kanban is guided by four foundational principles:

1. **Start with what you do now:** Kanban does not require a radical reorganization. You begin by visualizing your current workflow and making incremental improvements.

2. **Agree to pursue incremental, evolutionary change:** Instead of big, disruptive changes, Kanban encourages small, continuous improvements.

3. **Respect the current process, roles, and responsibilities:** Kanban does not prescribe specific roles (like Scrum Master or Product Owner). It works within your existing team structure.

4. **Encourage acts of leadership at all levels:** Everyone on the team is empowered to suggest and implement improvements.

### 8.7.5 Kanban Metrics

Kanban teams track several key metrics to measure and improve their process:

- **Lead Time:** The total time from when a work item is requested to when it is delivered.
- **Cycle Time:** The time from when work on an item begins to when it is completed.
- **Throughput:** The number of items completed per unit of time (per week, per sprint, etc.).
- **Cumulative Flow Diagram:** A chart that shows the number of items in each stage over time, helping identify bottlenecks and flow problems.

### 8.7.6 Kanban vs. Scrum

| Aspect | Scrum | Kanban |
|---|---|---|
| Cadence | Fixed-length sprints (1-4 weeks) | Continuous flow |
| Roles | Product Owner, Scrum Master, Dev Team | No prescribed roles |
| Work planning | Sprint Backlog committed per sprint | Continuous prioritization |
| WIP limits | Implicit (sprint capacity) | Explicit per column |
| Change during iteration | Not recommended during a sprint | Can add items any time |
| Meetings | Sprint Planning, Daily Standup, Review, Retro | As needed (often just daily standup) |
| Best for | New product development, feature work | Maintenance, support, continuous delivery |

Many teams use a hybrid approach called **Scrumban**, combining Scrum's ceremonies and roles with Kanban's visual boards and WIP limits.

---

## 8.8 Tools for Agile Project Management

### 8.8.1 Trello

**Trello** is a visual project management tool based on the Kanban board concept. It is simple, intuitive, and free for basic use.

Trello's structure:
- **Boards:** Each project gets a board (e.g., "My Web App")
- **Lists:** Columns on the board (e.g., "To Do," "In Progress," "Done")
- **Cards:** Individual work items that live in lists and can be moved between them

Each card can contain:
- A title and description
- Checklists for sub-tasks
- Due dates and labels (color-coded categories)
- Attachments and comments
- Assigned team members

**Best practices for using Trello:**
- Keep it simple. Do not create too many lists or cards.
- Use labels consistently (e.g., red for bugs, green for features, blue for documentation).
- Keep card descriptions clear and concise.
- Move cards promptly as work progresses.
- Archive completed cards to keep the board clean.

### 8.8.2 GitHub Projects

**GitHub Projects** provides Kanban-style project management directly integrated with your GitHub repositories. This is particularly useful because it connects your project board to your actual code -- pull requests, issues, and commits.

Key features:
- **Board view:** Classic Kanban columns with cards
- **Table view:** Spreadsheet-like view for bulk management
- **Automation:** Cards can automatically move between columns when issues are closed or pull requests are merged
- **Custom fields:** Add priority, size, sprint, or any custom attribute to cards
- **Integration:** Directly linked to GitHub Issues and Pull Requests

**Example workflow with GitHub Projects:**

1. Create a project board for your repository
2. Add columns: "Backlog," "Sprint," "In Progress," "In Review," "Done"
3. Create GitHub Issues for each task/feature/bug
4. Add issues to the project board as cards
5. When a developer starts working on an issue, they move it to "In Progress" and create a branch
6. When they open a Pull Request, the card moves to "In Review"
7. When the PR is merged, the card automatically moves to "Done"

### 8.8.3 Other Popular Tools

While Trello and GitHub Projects are great starting points, professional teams often use more full-featured tools:

- **Jira:** The most widely used project management tool in the enterprise. Supports Scrum, Kanban, and hybrid workflows with extensive customization and reporting.
- **Azure DevOps:** Microsoft's all-in-one platform for project management, version control, CI/CD, and testing.
- **Asana:** A versatile project management tool popular among non-technical teams but also used by development teams.
- **Linear:** A modern, fast project management tool designed specifically for software teams.
- **Notion:** A flexible workspace that can be configured for project management, documentation, and knowledge management.

---

## 8.9 Putting It All Together: A Real-World Example

Let us walk through how a small team might manage a project using Agile (Scrum) with a Kanban board.

**Scenario:** A team of 4 developers is building a simple task management web application.

### Week 1: Sprint Planning

The Product Owner creates user stories and prioritizes the backlog:

1. As a user, I want to create an account so I can save my tasks. (Priority: High)
2. As a user, I want to add tasks with a title and description. (Priority: High)
3. As a user, I want to mark tasks as complete. (Priority: High)
4. As a user, I want to organize tasks into categories. (Priority: Medium)
5. As a user, I want to set due dates for tasks. (Priority: Medium)
6. As a user, I want to search through my tasks. (Priority: Low)

The team commits to completing stories 1, 2, and 3 in the first sprint (2 weeks).

### Week 1-2: Sprint Execution

The team breaks stories into tasks and tracks them on their Kanban board:

```
Backlog:          To Do:               In Progress:         In Review:      Done:
[Search tasks]    [DB schema]          [Registration API]   [Login page]    [Project setup]
[Due dates]       [Task model]         [Task API endpoints]                 [CI/CD pipeline]
[Categories]      [Task list UI]
```

Daily standups keep everyone synchronized. The Scrum Master removes a blocker when the team has trouble configuring the database in the staging environment.

### Week 2: Sprint Review and Retrospective

The team demos the working application to the Product Owner:
- User registration and login works
- Users can create, view, and complete tasks
- All features are tested and deployed to staging

**Retrospective findings:**
- What went well: Good collaboration, clear user stories
- What to improve: Code reviews took too long -- consider reviewing smaller PRs more frequently
- Action item: Set a WIP limit of 2 on the "In Review" column

---

## 8.10 Summary and Key Takeaways

- **Software project management** is the planning, scheduling, and organizing of a software development project. It combats the chaos inherent in building complex software systems.
- Software projects face unique challenges: intangibility, complexity, rapid technology change, and human factors.
- The **SDLC** defines six phases: Initiation/Planning, Requirements, Design, Build, Testing, and Implementation.
- **Agile** is a project management approach that emphasizes small increments, continuous feedback, collaboration, and adaptability.
- The **Agile Manifesto** values individuals over processes, working software over documentation, customer collaboration over contracts, and responding to change over following a plan.
- **Scrum** is the most popular Agile framework, organizing work into fixed-length sprints with defined roles (Product Owner, Scrum Master, Development Team) and ceremonies (Sprint Planning, Daily Standup, Sprint Review, Sprint Retrospective).
- **Kanban** is a visual, flow-based approach using a board with columns and WIP limits to manage work continuously.
- **WIP limits** prevent overloading, expose bottlenecks, and improve overall throughput.
- Tools like **Trello**, **GitHub Projects**, and **Jira** help teams implement these methodologies in practice.
- The best teams adapt methodologies to their needs rather than following any framework rigidly.

---

## 8.11 Homework / Assignments

### Assignment 1: Create a Kanban Board

Think of a simple software application (a runnable program or a library). Using **Trello** (free) or **GitHub Projects**, create a Kanban board for managing its development.

Requirements:
- Create at least the following columns: **Backlog**, **To Do**, **In Progress**, **In Review/Testing**, **Done**
- Add at least 8-10 cards representing tasks needed to build your application
- Each card should have a clear title and a brief description
- Add labels to categorize cards (e.g., "feature," "bug," "documentation," "setup")
- Set WIP limits on the "In Progress" and "In Review" columns

**Important:** Keep it simple. Do not create excessive cards or columns. A clear, focused board is more valuable than a bloated one.

Submit a screenshot or a link to your board.

### Assignment 2: Write User Stories

For the same application you chose in Assignment 1, write at least 5 user stories in the standard format:

```
As a [type of user], I want to [action] so that [benefit/reason].
```

Each user story should include:
- A clear title
- The user story in the standard format
- At least 2 acceptance criteria (conditions that must be true for the story to be considered "done")
- A priority (High, Medium, Low)

### Assignment 3: Sprint Planning Simulation

Imagine your team has 2 developers and a 2-week sprint. Using the user stories from Assignment 2:

1. Estimate each user story using **story points** (1, 2, 3, 5, 8, 13) based on complexity.
2. Assuming your team's velocity is 20 story points per sprint, decide which stories to include in the first sprint.
3. Break down the selected stories into specific technical tasks.
4. Explain your prioritization decisions.

### Assignment 4: Methodology Comparison

Write a short report (300-500 words) comparing Scrum and Kanban. Address the following questions:
- When would you choose Scrum over Kanban? Give a specific example.
- When would you choose Kanban over Scrum? Give a specific example.
- What is Scrumban, and when might it be appropriate?
- Which approach would you prefer for your capstone project, and why?
