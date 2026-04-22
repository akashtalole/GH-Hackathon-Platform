# Participant Guide — GitHub Hackathon Platform

> **Who this is for:** Anyone submitting a Challenge, Idea, or Solution to the hackathon.

---

## Your Journey at a Glance

```mermaid
journey
    title Participant Journey
    section Discover
      Find the hackathon repo: 5: Participant
      Read Rules & browse Challenges: 4: Participant
      Join Discussions (Q&A): 5: Participant
    section Plan
      Pick a Challenge: 5: Participant
      Submit an Idea: 4: Participant
      Get idea acknowledged: 3: Participant, Organizer
    section Build
      Build your solution: 5: Participant
      Ask questions in Discussions: 4: Participant
      Watch leaderboard for inspiration: 3: Participant
    section Submit
      Submit a Solution issue: 5: Participant
      AI evaluation runs automatically: 5: System
      Read your score comment: 4: Participant
    section Results
      Watch leaderboard rank: 4: Participant
      Appeal score if needed: 3: Participant
      Winners announced via Release: 5: Organizer
```

---

## Submission Flow

```mermaid
flowchart TD
    A([Start — You have a GitHub account]) --> B[Browse Challenges\nIssues filtered by type/challenge]
    B --> C{Found a challenge\nyou want to solve?}
    C -- No --> D[Propose a new Challenge\nusing Challenge Submission form]
    C -- Yes --> E[Optionally submit an Idea\nusing Idea Submission form]
    D --> F[Organizer reviews challenge]
    E --> G[Build your solution]
    F --> G
    G --> H[Submit Solution\nusing Solution Submission form]
    H --> I{Repo public?\nREADME has setup docs?}
    I -- No --> J[Fix repo, edit\nyour submission issue]
    J --> I
    I -- Yes --> K[Organizer reviews submission]
    K --> L{Approved?}
    L -- Needs more info --> M[Respond to organizer\ncomment on issue]
    M --> K
    L -- Yes --> N[AI Evaluation Pipeline runs\nautomatically via GitHub Actions]
    N --> O[Score comment posted\non your issue]
    O --> P{Happy with score?}
    P -- Yes --> Q[Track rank on Leaderboard]
    P -- No --> R[File appeal in\nDiscussions › Appeals]
    R --> S[Organizer reviews appeal\nmay trigger re-evaluation]
    S --> Q
    Q --> T{Selected as finalist?}
    T -- Yes --> U[Judges review your repo\nand score manually]
    U --> V{Selected as winner?}
    V -- Yes --> W([🏆 Winner announced\nvia GitHub Release])
    V -- No --> X([Thank you for participating!])
    T -- No --> X
```

---

## Submission Status States

```mermaid
stateDiagram-v2
    direction LR

    [*] --> pending_review : Submit issue\n(auto-label applied)
    pending_review --> pending_evaluation : Organizer approves
    pending_review --> needs_more_info : Organizer requests\nclarification
    needs_more_info --> pending_review : You update issue\nor reply to comment

    pending_evaluation --> evaluating : Actions workflow\ntriggered
    evaluating --> evaluated : AI scores posted\nas issue comment

    evaluated --> finalist : Judge applies\nfinalist label
    finalist --> winner : Winner selected\nby organizer workflow
    finalist --> evaluated : Not selected\n(still evaluated)

    pending_review --> disqualified : Rules violation
    evaluated --> disqualified : Rules violation

    winner --> [*]
    disqualified --> [*]

    state pending_review { }
    state needs_more_info { }
    state pending_evaluation { }
    state evaluating { }
    state evaluated { }
    state finalist { }
    state winner { }
    state disqualified { }
```

---

## How to Submit an Idea

```mermaid
sequenceDiagram
    actor You
    participant GitHub as GitHub Issues
    participant Organizer
    participant Label as Label Bot

    You->>GitHub: Open New Issue → Idea Submission template
    GitHub-->>You: Structured form displayed
    You->>GitHub: Fill in: title, challenge #, pitch,\nproposed solution, team members
    You->>GitHub: Submit form
    GitHub->>Label: Auto-apply labels:\ntype/idea, status/pending-review
    Label-->>GitHub: Labels applied
    GitHub-->>You: Issue #N created
    Organizer->>GitHub: Reviews your idea
    Organizer->>GitHub: Adds category/* label\n(e.g. category/sustainability)
    GitHub-->>You: 🔔 Notification — label changed
    Note over You,Organizer: You're on record!\nStart building your solution.
```

---

## How to Submit a Solution

```mermaid
sequenceDiagram
    actor You
    participant Repo as Your GitHub Repo
    participant GitHub as GH Hackathon Issues
    participant Actions as GitHub Actions
    participant AI as GitHub Models API\n(GPT-4o)
    participant Board as Projects v2

    You->>Repo: Push finished code\nAdd README with setup docs
    You->>Repo: Make repository public
    You->>GitHub: Open New Issue → Solution Submission template
    You->>GitHub: Fill in all fields including repo URL
    GitHub->>GitHub: Auto-label: type/solution\nstatus/pending-evaluation
    GitHub-->>You: Issue #N created
    GitHub->>Actions: Trigger: issues.labeled\n(status/pending-evaluation)
    Actions->>GitHub: Add status/evaluating label
    Actions->>AI: POST /chat/completions\nwith rubric + issue body
    AI-->>Actions: JSON scores (5 dimensions)
    Actions->>GitHub: Post score table as comment
    Actions->>Board: Update scoring fields\nvia GraphQL mutation
    Actions->>GitHub: Add status/evaluated + bot/evaluated labels
    GitHub-->>You: 🔔 Notification — evaluation complete
    You->>GitHub: Read score comment on your issue
```

---

## Understanding Your Score

```mermaid
%%{init: {"pie": {"textPosition": 0.5}}}%%
pie title Score Dimensions (50 points total)
    "Innovation (10)" : 10
    "Technical Quality (10)" : 10
    "Documentation (10)" : 10
    "Feasibility (10)" : 10
    "Impact (10)" : 10
```

| Score Range | Tier | What it means |
|-------------|------|--------------|
| 45–50 | 🏆 Exceptional | Top-tier, production-ready, highly innovative |
| 35–44 | 🥇 Strong | Solid execution, good documentation, real impact |
| 25–34 | 🥈 Good | Functional but has gaps — consider improving docs or scope |
| 15–24 | 🥉 Developing | Core idea is there but implementation needs more work |
| 0–14 | ⚠️ Needs work | Submission is incomplete or very early stage |

---

## Timeline Overview

```mermaid
gantt
    title Hackathon Phases (example 4-week event)
    dateFormat  YYYY-MM-DD
    section Registration
    Browse challenges & register team    :active, reg, 2026-05-01, 7d
    Submit Challenge proposals           :reg2, 2026-05-01, 7d
    section Ideas
    Submit Idea submissions              :idea, 2026-05-08, 7d
    section Build & Submit
    Build your solution                  :build, 2026-05-08, 14d
    Solution submission window           :sol, 2026-05-15, 7d
    section Judging
    AI evaluation pipeline runs          :eval, 2026-05-22, 5d
    Human judge review                   :judge, 2026-05-24, 3d
    section Results
    Winners announced                    :milestone, 2026-05-27, 0d
    Results published on leaderboard     :results, 2026-05-27, 1d
```

---

## Key Links

| Resource | Link |
|----------|------|
| Submit (any type) | [New Issue → Choose template](https://github.com/akashtalole/gh-hackathon-platform/issues/new/choose) |
| Browse challenges | [Issues filtered by type/challenge](https://github.com/akashtalole/gh-hackathon-platform/issues?q=label:type/challenge) |
| Live leaderboard | [Hackathon website leaderboard](https://akashtalole.github.io/gh-hackathon-platform/leaderboard.html) |
| Discussions | [Community Q&A and Ideas](https://github.com/akashtalole/gh-hackathon-platform/discussions) |
| Rules | [Full rules page](https://akashtalole.github.io/gh-hackathon-platform/rules.html) |
| Appeals | [Discussions → Appeals](https://github.com/akashtalole/gh-hackathon-platform/discussions/categories/appeals) |
