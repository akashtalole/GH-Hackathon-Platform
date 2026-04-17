# Platform Owner Guide — GitHub Hackathon Platform

> **Who this is for:** Organizers who set up, operate, and manage the hackathon.

---

## Platform Overview

```mermaid
graph TB
    subgraph GitHub["GitHub Platform (All Native Features)"]
        subgraph Intake["Submission Intake"]
            IF[Issue Forms\n3 templates]
            LBL[Labels\n25+ defined]
            MS[Milestones\n5 phases]
        end
        subgraph Automation["Automation Layer"]
            WF1[evaluate-submission.yml\nAI scoring pipeline]
            WF2[update-leaderboard.yml\nPages regeneration]
            WF3[select-winners.yml\nWinner selection]
            WF4[phase-gate.yml\nPhase transitions]
            WF5[setup-repository.yml\nOne-time bootstrap]
        end
        subgraph Tracking["Tracking & Review"]
            PJ[Projects v2\nJudging board]
            ENV[Environments\n4 phase gates]
        end
        subgraph Community["Community"]
            DISC[Discussions\n5 categories]
        end
        subgraph Output["Output"]
            PAGES[GitHub Pages\nLanding + Leaderboard]
            REL[GitHub Releases\nWinner announcement]
        end
        subgraph AI["AI Evaluation"]
            MOD[GitHub Models API\nGPT-4o]
        end
    end

    IF -->|issue created| WF1
    WF1 -->|calls| MOD
    MOD -->|scores JSON| WF1
    WF1 -->|score comment| IF
    WF1 -->|GraphQL mutation| PJ
    WF1 -->|dispatch| WF2
    WF2 -->|write JSON| PAGES
    WF3 -->|requires approval| ENV
    WF3 -->|apply labels| IF
    WF3 -->|create| REL
    WF4 -->|bulk label| IF
    WF5 -->|creates| LBL
    WF5 -->|creates| PJ
    WF5 -->|creates| MS
```

---

## First-Time Setup Sequence

```mermaid
sequenceDiagram
    actor Owner as Platform Owner
    participant GH as GitHub Repository
    participant Actions as GitHub Actions
    participant API as GitHub GraphQL API
    participant Pages as GitHub Pages
    participant Envs as GitHub Environments

    Owner->>Actions: Run setup-repository.yml workflow
    Actions->>API: Create labels (25+) via REST
    Actions->>GH: Create milestones (5 phases)
    Actions->>API: Create Projects v2 board\n+ 6 scoring fields + 2 select fields
    API-->>Actions: PROJECT_NUMBER returned
    Actions-->>Owner: Workflow summary shows PROJECT_NUMBER

    Owner->>GH: Settings → Variables → Add PROJECT_NUMBER
    Owner->>Pages: Settings → Pages → main branch /docs
    Pages-->>Owner: Site live at github.io URL

    Owner->>GH: Settings → General → Enable Discussions
    Owner->>GH: Create 5 discussion categories\n(Announcements, Q&A, Ideas, Show & Tell, Appeals)

    Owner->>Envs: Settings → Environments\nCreate: Registration, Submission, Judging, Results
    Owner->>Envs: Results environment:\nAdd required reviewer (yourself + lead judges)

    Owner->>Actions: Run phase-gate.yml\ntarget_phase: Registration, confirm: CONFIRM
    Actions->>GH: Apply phase/registration label to all issues
    Actions-->>Owner: Announcement text in workflow summary
    Owner->>GH: Post announcement in Discussions → Announcements
```

---

## Hackathon Phase State Machine

```mermaid
stateDiagram-v2
    direction LR

    [*] --> Setup : Repository created

    Setup --> Registration : Run setup-repository.yml\nRun phase-gate(Registration)
    note right of Setup
        One-time actions:
        - Bootstrap labels
        - Create Projects v2
        - Enable Pages + Discussions
        - Create Environments
    end note

    Registration --> Submission : Run phase-gate(Submission)
    note right of Registration
        Organizer actions:
        - Post challenges via Issue Forms
        - Monitor Discussions Q&A
        - Review Idea submissions
    end note

    Submission --> Judging : Run phase-gate(Judging)\n(closes submission window)
    note right of Submission
        Automatic:
        - Evaluate pipeline fires on solutions
        - Leaderboard updates every 6h
        Organizer:
        - Review/approve submissions
        - Monitor evaluation queue
    end note

    Judging --> Results : Run phase-gate(Results)\nRun select-winners(dry_run=false)\n[requires environment approval]
    note right of Judging
        Organizer + Judges:
        - Review AI scores on Projects board
        - Mark status/finalist on top entries
        - Inspect repos manually
        - Run select-winners(dry_run=true) preview
    end note

    Results --> [*]
    note right of Results
        Auto:
        - GitHub Release created
        - Winner labels applied
        Organizer:
        - Post Release link in Discussions
        - Archive closed issues
    end note
```

---

## Challenge Posting Workflow

```mermaid
flowchart LR
    A([Organizer has a\nreal problem to solve]) --> B[Open New Issue\n→ Challenge Submission template]
    B --> C[Fill in:\n• Problem statement\n• Success criteria\n• Resources & datasets\n• Prize description]
    C --> D[Submit issue\nAuto-labels: type/challenge\nstatus/pending-review]
    D --> E[Add category/* label\ne.g. category/sustainability]
    E --> F[Assign to milestone\ne.g. Registration Phase]
    F --> G[Share in Discussions\n→ Announcements]
    G --> H([Challenge visible\nto all participants])
```

---

## Submission Review Decision Tree

```mermaid
flowchart TD
    A([New solution issue\ncreated with\nstatus/pending-evaluation]) --> B{Check: Is repo\npublic?}
    B -- No --> C[Add status/needs-more-info\nComment: 'Please make repo public']
    C --> D[Wait for participant\nto fix and notify]
    D --> B
    B -- Yes --> E{Check: Does README\nhave setup docs?}
    E -- No --> F[Add status/needs-more-info\nComment: 'README needs setup instructions']
    F --> D
    E -- Yes --> G{Check: Was code\nwritten during hackathon?}
    G -- Suspicious --> H[Investigate commits\nvia GitHub commit history]
    H --> I{Plagiarism\nor prior code?}
    I -- Yes --> J[Add status/disqualified\nClose issue with explanation]
    I -- No --> K[Approve]
    G -- Yes --> K
    K --> L[status/pending-evaluation label\nalready triggers pipeline automatically]
    L --> M([AI evaluation\nstarts automatically])
```

---

## Evaluation Pipeline Monitoring

```mermaid
flowchart LR
    subgraph Monitor["How to Monitor Evaluations"]
        A[Actions tab\nSee all workflow runs] --> B{Status?}
        B -- Running --> C[Watch live logs\nin Actions UI]
        B -- Failed --> D[Open failed run\nCheck error step]
        D --> E{Fix needed?}
        E -- Re-evaluate --> F[Remove status/evaluated\nRe-add status/pending-evaluation]
        E -- Bug in script --> G[Fix scripts/evaluate.js\nPush to main]
        B -- Succeeded --> H[Score comment on issue\nProjects v2 fields updated]
    end

    subgraph Board["Projects v2 Board Views"]
        P1[Board view\nKanban by Evaluation Status]
        P2[Table view\nAll scores sortable]
        P3[Filter: type/solution\nOnly judging-eligible]
    end

    H --> Board
```

---

## Winner Selection Workflow

```mermaid
sequenceDiagram
    actor Owner as Platform Owner
    actor Judge as Lead Judge
    participant Actions as GitHub Actions
    participant AI as GitHub Models API
    participant Env as Results Environment
    participant GH as GitHub Issues + Releases

    Owner->>Actions: Run select-winners.yml\ndry_run=true, top_n=3

    Actions->>Actions: Read docs/_data/leaderboard.json
    Actions->>Actions: Sort entries by total score desc
    Actions->>Actions: Select top 3 entries
    loop For each winner
        Actions->>AI: Generate winner citation\n(2-sentence announcement text)
        AI-->>Actions: Citation text
    end
    Actions-->>Owner: Dry run summary:\n• Names, scores, citations\n• No labels or Release created

    Owner->>Judge: Share dry run output\nfor review
    Judge-->>Owner: Approved / request changes

    Owner->>Actions: Run select-winners.yml\ndry_run=false, top_n=3
    Actions->>Env: Request deployment approval\n(Results environment gate)
    Env->>Judge: 🔔 Notification: pending approval
    Judge->>Env: Click Approve and deploy

    Actions->>GH: Apply status/winner label\n+ status/finalist to top 3 issues
    Actions->>GH: Create GitHub Release\nwith Markdown announcement + citations
    GH-->>Owner: 🔔 Release published

    Owner->>GH: Post Release link in Discussions\n→ Announcements
    Owner->>Actions: Run phase-gate(Results)
    Owner->>Actions: Run update-leaderboard\nfor final snapshot
```

---

## Leaderboard Data Flow

```mermaid
flowchart TD
    subgraph Triggers["Workflow Triggers"]
        T1[Manual dispatch]
        T2[Schedule: every 6h]
        T3[Issue labeled bot/evaluated]
    end

    T1 & T2 & T3 --> WF[update-leaderboard.yml\nGitHub Actions]

    WF --> API[GitHub REST API\nFetch issues: type/solution + bot/evaluated]
    API --> ISSUES[List of evaluated\nsolution issues]
    ISSUES --> PARSE[Parse AI evaluation comment\nExtract scores via regex]
    PARSE --> JSON[Write docs/_data/leaderboard.json\n{rank, title, team, scores, is_winner...}]
    JSON --> COMMIT[Git commit + push to main\n'chore: update leaderboard data skip ci']
    COMMIT --> PAGES[GitHub Actions deploy-pages\nPublish docs/ to GitHub Pages]
    PAGES --> SITE[Live site updated\nleaderboard.html reads JSON via fetch]

    style SITE fill:#2ea44f,color:#fff
```

---

## Repository Variables & Secrets Reference

```mermaid
graph LR
    subgraph Auto["Auto-Injected"]
        GT[GITHUB_TOKEN\nAll workflows]
    end
    subgraph Vars["Repository Variables\nSettings → Variables"]
        PN[PROJECT_NUMBER\nRequired after setup]
        EM[EVAL_MODEL\nDefault: gpt-4o]
    end
    subgraph Secrets["Repository Secrets\nSettings → Secrets"]
        PT[PROJECT_TOKEN\nOptional: PAT with project scope\nOnly needed for personal repos]
    end

    GT -->|Issues write\nModels API\nGraphQL| WF1[evaluate-submission]
    GT -->|Issues read\nPages deploy| WF2[update-leaderboard]
    GT -->|Issues write\nRelease create| WF3[select-winners]
    PN -->|GraphQL project lookup\nField updates| WF1
    EM -->|Model selection| WF1
    EM -->|Model selection| WF3
    PT -.->|Override GITHUB_TOKEN\nfor project mutations| WF1
```

---

## Operational Checklist

```mermaid
graph TD
    subgraph PreLaunch["Pre-Launch ✅"]
        C1[Run setup-repository.yml]
        C2[Set PROJECT_NUMBER variable]
        C3[Enable GitHub Pages]
        C4[Enable Discussions]
        C5[Create 5 Discussion categories]
        C6[Create 4 Environments]
        C7[Add reviewer gate on Results env]
        C8[Post at least 1 challenge]
        C9[Run phase-gate Registration]
        C10[Test evaluation with a dummy issue]
    end

    subgraph DuringHackathon["During Hackathon 🔄"]
        D1[Review incoming submissions daily]
        D2[Monitor Actions for failed evaluations]
        D3[Respond to Discussions Q&A]
        D4[Watch leaderboard for anomalies]
    end

    subgraph PostHackathon["Post-Hackathon 🏁"]
        P1[Run phase-gate Judging]
        P2[Coordinate judge review of finalists]
        P3[Run select-winners dry_run=true]
        P4[Get judge approval on dry run output]
        P5[Run select-winners dry_run=false]
        P6[Judge approves Results environment]
        P7[Post Release link in Discussions]
        P8[Run phase-gate Results]
        P9[Final leaderboard update]
    end

    C1-->C2-->C3-->C4-->C5-->C6-->C7-->C8-->C9-->C10
    C10 --> D1
    D1-->D2-->D3-->D4
    D4 --> P1
    P1-->P2-->P3-->P4-->P5-->P6-->P7-->P8-->P9
```
