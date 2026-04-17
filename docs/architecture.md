# Technical Architecture — GitHub Hackathon Platform

End-to-end technical reference for the GitHub-native hackathon platform. Every component is a GitHub built-in feature — no external servers, no databases.

---

## System Overview

```mermaid
graph TB
    subgraph Participants["👥 Participants"]
        P1[Web browser\ngithub.io site]
        P2[GitHub Issues UI\nnative forms]
    end

    subgraph GitHubPlatform["GitHub Platform"]
        subgraph Storage["Storage Layer"]
            ISS[Issues\nSubmissions as structured text]
            LBL[Labels\n25+ lifecycle + category tags]
            MS[Milestones\n5 hackathon phases]
            PJ[Projects v2\nJudging board + scoring fields]
            DISC[Discussions\nCommunity Q&A + announcements]
            PAGES[GitHub Pages\nStatic site + leaderboard]
            REL[Releases\nWinner announcements]
        end

        subgraph Automation["Automation Layer — GitHub Actions"]
            WF1[evaluate-submission.yml]
            WF2[update-leaderboard.yml]
            WF3[select-winners.yml]
            WF4[phase-gate.yml]
            WF5[setup-repository.yml]
        end

        subgraph Scripts["Node.js Scripts"]
            S1[evaluate.js]
            S2[update-project-fields.js]
            S3[update-leaderboard.js]
            S4[select-winners.js]
            S5[setup-labels.sh]
            S6[setup-project.sh]
        end

        subgraph Auth["Auth & Secrets"]
            GT[GITHUB_TOKEN\nauto-injected]
            ENV[Environments\nRegistration/Submission\nJudging/Results]
        end
    end

    subgraph AI["AI Layer"]
        MODELS[GitHub Models API\nhttps://models.inference.ai.azure.com\nGPT-4o]
    end

    subgraph Judges["⚖️ Judges + Owners"]
        J1[GitHub Projects board]
        J2[Actions workflow dispatch]
    end

    P1 -->|fetch POST issues API| ISS
    P2 -->|submit issue form| ISS
    ISS -->|issues.labeled event| WF1
    WF1 -->|runs| S1
    S1 -->|POST /chat/completions| MODELS
    MODELS -->|scores JSON| S1
    S1 -->|evaluation_json output| WF1
    WF1 -->|createComment| ISS
    WF1 -->|runs| S2
    S2 -->|GraphQL mutation| PJ
    WF1 -->|dispatch| WF2
    WF2 -->|runs| S3
    S3 -->|REST list issues| ISS
    S3 -->|write JSON| PAGES
    WF2 -->|deploy-pages| PAGES
    WF3 -->|runs| S4
    S4 -->|read| PAGES
    S4 -->|POST /chat/completions| MODELS
    S4 -->|apply labels| ISS
    S4 -->|create release| REL
    WF4 -->|bulk label| ISS
    WF5 -->|runs| S5
    WF5 -->|runs| S6
    S5 -->|REST create labels| LBL
    S6 -->|GraphQL createProjectV2| PJ
    J1 -->|review scores| PJ
    J2 -->|approve| ENV
    ENV -->|gates| WF3
    GT -->|auth| WF1 & WF2 & WF3 & WF4
```

---

## Data Model

```mermaid
erDiagram
    ISSUE {
        int number PK
        string title
        string body
        string state
        string user_login
        datetime created_at
        datetime updated_at
    }
    LABEL {
        string name PK
        string color
        string description
    }
    MILESTONE {
        int number PK
        string title
        string state
    }
    PROJECT_ITEM {
        string id PK
        int innovation_score
        int technical_score
        int documentation_score
        int feasibility_score
        int impact_score
        int total_score
        string evaluation_status
        string submission_type
    }
    COMMENT {
        int id PK
        string body
        string user_login
        datetime created_at
    }
    RELEASE {
        string tag_name PK
        string name
        string body
        bool latest
    }
    LEADERBOARD_JSON {
        string generated_at
        int total_submissions
        array entries
    }

    ISSUE ||--o{ LABEL : "has many"
    ISSUE ||--o| MILESTONE : "belongs to"
    ISSUE ||--|| PROJECT_ITEM : "maps to"
    ISSUE ||--o{ COMMENT : "has many"
    COMMENT ||--o| LEADERBOARD_JSON : "parsed into"
    PROJECT_ITEM ||--o| LEADERBOARD_JSON : "feeds"
    RELEASE ||--o| LEADERBOARD_JSON : "references"
```

---

## Evaluation Pipeline — Detailed Sequence

```mermaid
sequenceDiagram
    actor Participant
    participant GH as GitHub Issues
    participant Actions as GitHub Actions Runner
    participant Script as scripts/evaluate.js
    participant Models as GitHub Models API
    participant ProjScript as scripts/update-project-fields.js
    participant GraphQL as GitHub GraphQL API
    participant LBScript as scripts/update-leaderboard.js
    participant Pages as GitHub Pages

    Participant->>GH: Submit solution via Issue Form
    GH->>GH: Auto-apply labels:\ntype/solution\nstatus/pending-evaluation

    GH->>Actions: Webhook: issues.labeled\n{label: status/pending-evaluation}

    Actions->>Actions: Check conditions:\nlabel == pending-evaluation AND\nhas type/solution label

    Actions->>GH: Add label: status/evaluating
    Actions->>GH: Remove label: status/pending-evaluation

    Actions->>Script: node scripts/evaluate.js
    Note over Script: Env vars:\nGITHUB_TOKEN, ISSUE_BODY,\nISSUE_TITLE, ISSUE_NUMBER

    Script->>Script: Validate issue body\nnot empty

    loop Retry up to 3x with backoff
        Script->>Models: POST /chat/completions\n{model: gpt-4o,\nsystem: rubric,\nuser: issue body,\nresponse_format: json_object}
        Models-->>Script: {innovation, technical_quality,\ndocumentation, feasibility,\nimpact, rationale, summary}
    end

    Script->>Script: Validate scores 0-10\nRound to integers
    Script->>Actions: Write evaluation_json\nto GITHUB_OUTPUT

    Actions->>GH: POST /issues/{n}/comments\nFormatted score table + rationale

    Actions->>GH: Add labels:\nstatus/evaluated\nbot/evaluated
    Actions->>GH: Remove label: status/evaluating

    Actions->>ProjScript: node scripts/update-project-fields.js
    ProjScript->>GraphQL: query: find project by number
    GraphQL-->>ProjScript: project.id + field IDs
    ProjScript->>GraphQL: mutation: addProjectV2ItemById
    GraphQL-->>ProjScript: item.id

    loop For each score field (6 total)
        ProjScript->>GraphQL: mutation: updateProjectV2ItemFieldValue\n{number: score}
        GraphQL-->>ProjScript: updated
    end

    Actions->>Actions: workflow_dispatch\nupdate-leaderboard.yml

    Actions->>LBScript: node scripts/update-leaderboard.js
    LBScript->>GH: GET /issues?labels=type/solution,bot/evaluated
    GH-->>LBScript: All evaluated issues

    loop For each issue
        LBScript->>GH: GET /issues/{n}/comments
        GH-->>LBScript: Comments list
        LBScript->>LBScript: Find bot comment\nParse score table via regex
    end

    LBScript->>LBScript: Sort by total desc\nAssign ranks
    LBScript->>Pages: Write docs/_data/leaderboard.json
    Actions->>Pages: deploy-pages action\nPublish docs/ to github.io
    Pages-->>Participant: 🔔 Leaderboard updated
```

---

## Label State Machine

```mermaid
stateDiagram-v2
    direction TB

    state "Type Labels (set on create)" as TypeGroup {
        TC: type/challenge
        TI: type/idea
        TS: type/solution
    }

    state "Status Lifecycle" as StatusGroup {
        [*] --> pending_review : Issue created\n(form auto-applies)
        pending_review --> pending_evaluation : Organizer approves
        pending_review --> needs_more_info : Organizer requests info
        needs_more_info --> pending_review : Participant updates issue

        pending_evaluation --> evaluating : Actions workflow starts
        evaluating --> evaluated : AI scores posted
        evaluated --> finalist : Judge applies label
        finalist --> winner : select-winners workflow

        pending_review --> disqualified : Rules violation
        evaluated --> disqualified : Rules violation
    }

    state "Phase Labels (bulk-applied)" as PhaseGroup {
        phase_reg: phase/registration
        phase_sub: phase/submission
        phase_jud: phase/judging
        phase_res: phase/results
    }

    state "Bot Markers (auto-applied)" as BotGroup {
        bot_eval: bot/evaluated
        bot_lb: bot/leaderboard-updated
    }
```

---

## GitHub Actions Workflow Dependency Graph

```mermaid
graph LR
    subgraph Triggers["External Triggers"]
        T1[issues.labeled\nstatus/pending-evaluation]
        T2[issues.labeled\nbot/evaluated]
        T3[workflow_dispatch\nmanual]
        T4[schedule\nevery 6h]
        T5[workflow_dispatch\none-time]
    end

    subgraph Workflows["Workflows"]
        WF1[evaluate-submission.yml]
        WF2[update-leaderboard.yml]
        WF3[select-winners.yml]
        WF4[phase-gate.yml]
        WF5[setup-repository.yml]
    end

    subgraph Scripts["Scripts Called"]
        S1[evaluate.js]
        S2[update-project-fields.js]
        S3[update-leaderboard.js]
        S4[select-winners.js]
        S5[setup-labels.sh]
        S6[setup-project.sh]
    end

    subgraph Outputs["Side Effects"]
        O1[Issue comment]
        O2[Label changes]
        O3[GraphQL field updates]
        O4[leaderboard.json commit]
        O5[Pages deployment]
        O6[GitHub Release]
        O7[Labels + milestones created]
        O8[Project v2 created]
    end

    T1 --> WF1
    WF1 --> S1 --> O1
    WF1 --> S2 --> O3
    WF1 --> O2
    WF1 -->|dispatch| WF2

    T2 --> WF2
    T3 --> WF2
    T4 --> WF2
    WF2 --> S3 --> O4
    WF2 --> O5

    T3 --> WF3
    WF3 --> S4 --> O6
    WF3 --> O2

    T3 --> WF4
    WF4 --> O2

    T5 --> WF5
    WF5 --> S5 --> O7
    WF5 --> S6 --> O8
```

---

## GitHub Projects v2 Schema

```mermaid
graph TD
    subgraph Project["Projects v2 Board — Hackathon Judging Board"]
        subgraph BuiltIn["Built-in Fields"]
            F0[Title]
            F1[Assignees]
            F2[Status\nbuilt-in single-select]
            F3[Labels]
            F4[Milestone]
        end

        subgraph Custom["Custom Fields — created by setup-project.sh"]
            subgraph Numbers["Number Fields"]
                N1[Innovation Score\n0–10]
                N2[Technical Score\n0–10]
                N3[Documentation Score\n0–10]
                N4[Feasibility Score\n0–10]
                N5[Impact Score\n0–10]
                N6[Total Score\n0–50]
            end
            subgraph Selects["Single-Select Fields"]
                S1[Evaluation Status\nPending / Evaluating /\nEvaluated / Finalist / Winner]
                S2[Submission Type\nChallenge / Idea / Solution]
            end
        end
    end

    N1 & N2 & N3 & N4 & N5 --> N6
    S1 -->|mirrors| LBL[status/* labels\non the issue]
```

---

## GitHub Environments — Phase Gate Architecture

```mermaid
graph TB
    subgraph Environments["GitHub Environments"]
        E1[Registration\nNo protection rules]
        E2[Submission\nNo protection rules]
        E3[Judging\nNo protection rules]
        E4[Results\n⚠️ Required reviewers:\nLead judge must approve]
    end

    subgraph Workflows["Workflows using environments"]
        WF4[phase-gate.yml\nruns in target environment]
        WF3[select-winners.yml\nalways runs in Results env]
    end

    subgraph Flow["Phase Progression"]
        P1([Organizer: dispatch\nphase-gate Registration]) --> E1
        E1 --> P2([Organizer: dispatch\nphase-gate Submission])
        P2 --> E2
        E2 --> P3([Organizer: dispatch\nphase-gate Judging])
        P3 --> E3
        E3 --> P4([Organizer: dispatch\nselect-winners dry_run=false])
        P4 -->|requires approval| E4
        E4 -->|judge approves| P5([Winners announced\nRelease created])
    end

    WF4 -.->|deploys to| E1 & E2 & E3 & E4
    WF3 -.->|always deploys to| E4
```

---

## Leaderboard Data Pipeline

```mermaid
flowchart LR
    subgraph Source["Data Sources"]
        A[GitHub Issues API\nGET /issues?labels=\ntype/solution,bot/evaluated]
        B[GitHub Comments API\nGET /issues/n/comments]
    end

    subgraph Transform["Transform — update-leaderboard.js"]
        C[For each issue:\nfind bot evaluation comment]
        D[Regex parse score table\n5 dimension scores + total]
        E[Extract fields from body:\nteam, repo URL, challenge ref]
        F[Sort by total score desc\nAssign rank 1..N]
        G[Flag is_winner + is_finalist\nfrom label presence]
    end

    subgraph Output["Output"]
        H[docs/_data/leaderboard.json\n{generated_at, total, entries}]
        I[Git commit + push\nskip ci]
        J[GitHub Pages deploy\ndeploy-pages action]
        K[leaderboard.html\nfetch JSON client-side\nrender table + filter/sort]
    end

    A --> C
    B --> D
    C --> D
    D --> E --> F --> G --> H
    H --> I --> J --> K
```

---

## Security Model

```mermaid
graph TB
    subgraph Secrets["Secrets & Tokens"]
        GT[GITHUB_TOKEN\nauto-injected per workflow run\nexpires after run]
        PT[PROJECT_TOKEN\noptional PAT\nclassic, project scope only\nstored as repo secret]
    end

    subgraph Permissions["Workflow Permission Declarations"]
        P1[evaluate-submission.yml\nissues: write\nmodels: read\ncontents: read]
        P2[update-leaderboard.yml\nissues: read\ncontents: write\npages: write\nid-token: write]
        P3[select-winners.yml\nissues: write\ncontents: write\nmodels: read]
        P4[phase-gate.yml\nissues: write\ncontents: read]
        P5[setup-repository.yml\nissues: write\ncontents: read]
    end

    subgraph Exposure["Attack Surface"]
        E1[models: read scope\nenables Models API calls\nno data exfiltration risk]
        E2[issues: write\ncan create/edit issues\ncannot access other repos]
        E3[pages: write\ncan update docs/ only]
        E4[Results environment\nrequires human approval\nbefore winner labels applied]
    end

    GT --> P1 & P2 & P3 & P4 & P5
    PT -.->|fallback| P1
    P1 --> E1 & E2
    P2 --> E3
    P3 --> E4
```

---

## Component Inventory

| Component | Type | GitHub Feature | File Path |
|-----------|------|---------------|-----------|
| Challenge form | Input | Issue Form YAML | `.github/ISSUE_TEMPLATE/challenge-submission.yml` |
| Idea form | Input | Issue Form YAML | `.github/ISSUE_TEMPLATE/idea-submission.yml` |
| Solution form | Input | Issue Form YAML | `.github/ISSUE_TEMPLATE/solution-submission.yml` |
| Label definitions | Config | GitHub Labels | `.github/labels.yml` |
| AI evaluation | Workflow | GitHub Actions | `.github/workflows/evaluate-submission.yml` |
| Leaderboard refresh | Workflow | GitHub Actions | `.github/workflows/update-leaderboard.yml` |
| Winner selection | Workflow | GitHub Actions | `.github/workflows/select-winners.yml` |
| Phase transitions | Workflow | GitHub Actions | `.github/workflows/phase-gate.yml` |
| Bootstrap setup | Workflow | GitHub Actions | `.github/workflows/setup-repository.yml` |
| AI scoring logic | Script | Node.js | `scripts/evaluate.js` |
| Project field updater | Script | Node.js | `scripts/update-project-fields.js` |
| Leaderboard generator | Script | Node.js | `scripts/update-leaderboard.js` |
| Winner ranker | Script | Node.js | `scripts/select-winners.js` |
| Label bootstrap | Script | Bash + gh CLI | `scripts/setup-labels.sh` |
| Project bootstrap | Script | Bash + gh CLI | `scripts/setup-project.sh` |
| Landing page | Frontend | GitHub Pages | `docs/index.html` |
| Leaderboard page | Frontend | GitHub Pages | `docs/leaderboard.html` |
| Rules page | Frontend | GitHub Pages | `docs/rules.md` |
| Leaderboard data | Data | Jekyll `_data` | `docs/_data/leaderboard.json` |
| Styles | Frontend | GitHub Pages | `docs/assets/css/main.css` |
