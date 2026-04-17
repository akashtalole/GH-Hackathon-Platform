# Judge Guide — GitHub Hackathon Platform

> **Who this is for:** Human judges who review AI evaluation scores and finalize winner selections.

---

## Judge Role Overview

```mermaid
graph LR
    subgraph AI["AI Layer (Automated)"]
        A1[GitHub Models API\nGPT-4o]
        A2[5-dimension scoring\n0–10 per dimension]
        A3[Score comment\non issue]
    end

    subgraph Human["Human Layer (You)"]
        H1[Review AI scores\non Projects board]
        H2[Inspect actual\nGitHub repos]
        H3[Mark finalists\nadd status/finalist label]
        H4[Approve winner\nselection workflow]
    end

    subgraph Output["Output"]
        O1[GitHub Release\nwinner announcement]
        O2[Leaderboard badges\n🏆 winner markers]
    end

    A1-->A2-->A3-->H1
    H1-->H2-->H3-->H4
    H4-->O1
    H4-->O2

    style AI fill:#0550ae,color:#fff
    style Human fill:#2ea44f,color:#fff
    style Output fill:#bf8700,color:#fff
```

---

## End-to-End Judging Workflow

```mermaid
flowchart TD
    START([Judging phase begins\nOrganizer runs phase-gate Judging]) --> A

    A[Access Projects v2 board\nTable view sorted by Total Score] --> B

    B[Review top submissions\nStart with score ≥ 35/50] --> C

    C{AI score\nreliable?} -->|Review carefully| D[Open submission issue\nRead AI rationale per dimension]

    D --> E[Open linked GitHub repo\nInspect actual code quality]

    E --> F{Agree with\nAI score?}

    F -- Significantly off --> G[Note discrepancy\nfor manual override]
    F -- Roughly agree --> H[Continue to next]

    G --> H

    H --> I{Reviewed enough\nsubmissions?}
    I -- No --> B
    I -- Yes --> J[Shortlist top candidates\nApply status/finalist label\nto 3–5× final winner count]

    J --> K[Coordinate with\nother judges via\nDiscussions or private channel]

    K --> L[Organizer runs\nselect-winners dry_run=true\nShares output with judges]

    L --> M{Satisfied with\nproposed winners?}

    M -- Request changes --> N[Discuss adjustments\nwith organizer\nModify finalist labels if needed]
    N --> L

    M -- Approved --> O[Organizer runs\nselect-winners dry_run=false]

    O --> P[🔔 GitHub notification:\nResults environment pending approval]

    P --> Q[Go to Actions tab\nFind pending workflow run\nClick Review deployments]

    Q --> R{Final check:\ndry run output correct?}

    R -- Yes --> S[Click Approve and deploy]
    R -- No --> T[Click Reject\nNotify organizer of issue]

    S --> U([Winners announced\nGitHub Release created\nLabels applied])
```

---

## Accessing the Judging Board

```mermaid
sequenceDiagram
    actor Judge
    participant GH as GitHub Repository
    participant Proj as Projects v2 Board
    participant Issue as Submission Issue

    Judge->>GH: Navigate to repository
    Judge->>GH: Click Projects tab
    Judge->>Proj: Open Hackathon Judging Board
    Judge->>Proj: Switch to Table view
    Judge->>Proj: Filter: type = Solution
    Judge->>Proj: Sort: Total Score descending
    Proj-->>Judge: All submissions ranked by AI score

    loop For each submission to review
        Judge->>Proj: Click submission title
        Proj-->>Issue: Opens GitHub Issue
        Judge->>Issue: Read submission description
        Judge->>Issue: Scroll to AI Evaluation Report comment
        Judge->>Issue: Note scores and rationale
        Judge->>Issue: Click repo URL link
        Issue-->>Judge: Opens participant's GitHub repo
        Judge->>Judge: Review code, README, commits
    end
```

---

## AI Score Reliability by Dimension

```mermaid
quadrantChart
    title AI Evaluation Reliability vs. Human Effort Required
    x-axis Low Reliability --> High Reliability
    y-axis Low Human Effort --> High Human Effort

    Impact: [0.65, 0.35]
    Documentation: [0.80, 0.20]
    Feasibility: [0.60, 0.55]
    Innovation: [0.55, 0.65]
    Technical Quality: [0.25, 0.90]
```

| Dimension | AI Reliability | Why | Your Action |
|-----------|---------------|-----|-------------|
| **Documentation** | High | AI reads submission text directly | Spot-check only |
| **Impact** | Medium-High | AI evaluates claims in description | Sanity-check scale of claim |
| **Feasibility** | Medium | AI can't run the code | Check for working demo |
| **Innovation** | Medium-Low | AI may not know niche domains | Research comparables |
| **Technical Quality** | Low | AI cannot inspect the actual repo | Always review code manually |

---

## Scoring Rubric — Quick Reference Card

```mermaid
graph TD
    subgraph Innovation["💡 Innovation (0–10)"]
        I0["0–2: Copy of existing solution"]
        I3["3–4: Minor improvement"]
        I5["5–6: New combination of tech"]
        I7["7–8: Novel UX or approach"]
        I9["9–10: Breakthrough concept"]
    end

    subgraph Technical["⚙️ Technical Quality (0–10)"]
        T0["0–2: Broken / no working code"]
        T3["3–4: Major gaps or bugs"]
        T5["5–6: Functional, reasonable arch"]
        T7["7–8: Clean, well-architected"]
        T9["9–10: Production-quality"]
    end

    subgraph Docs["📄 Documentation (0–10)"]
        D0["0–2: No documentation"]
        D3["3–4: Minimal info"]
        D5["5–6: Adequate README"]
        D7["7–8: Thorough setup + arch notes"]
        D9["9–10: Diagrams, API docs, guides"]
    end

    subgraph Feas["🎯 Feasibility (0–10)"]
        F0["0–2: Unrealistic / vaporware"]
        F3["3–4: High risk, unclear path"]
        F5["5–6: Achievable with effort"]
        F7["7–8: Well-scoped, good evidence"]
        F9["9–10: Working MVP demonstrated"]
    end

    subgraph Impact["🌍 Impact (0–10)"]
        M0["0–2: Trivial problem / tiny audience"]
        M3["3–4: Limited scope"]
        M5["5–6: Meaningful for a niche"]
        M7["7–8: Broad potential audience"]
        M9["9–10: Transformative, scalable"]
    end
```

---

## Manual Score Override Process

When you disagree with an AI score, use this process:

```mermaid
flowchart LR
    A[AI score posted\non issue] --> B{Do you disagree\nby 2+ points?}
    B -- No --> C[Accept AI score\nmove on]
    B -- Yes --> D[Open Projects v2 board\nTable view]
    D --> E[Find the submission row]
    E --> F[Click the score field\nyou want to override]
    F --> G[Enter your score\n0–10]
    G --> H[Leave a judge comment\non the issue explaining\nyour override rationale]
    H --> I[The Total Score field\nwill NOT auto-recalculate\nUpdate it manually too]
    I --> J[Other judges can\nview your override\nin Projects board]

    style C fill:#2ea44f,color:#fff
    style J fill:#0550ae,color:#fff
```

> **Note:** Manual score overrides are made directly in the Projects v2 board fields. They do not change the AI evaluation comment on the issue — the comment is a record of the AI's assessment. Add your own comment on the issue to document your rationale.

---

## Finalist Selection Strategy

```mermaid
flowchart TD
    A[All evaluated submissions\nin leaderboard] --> B[Tier 1: Score ≥ 40/50\nAutomatic shortlist candidates]
    B --> C[Tier 2: Score 30–39\nReview manually — AI may have\nunderscored niche innovations]
    C --> D[Tier 3: Score < 30\nOnly review if submission\nlooks promising despite low score]

    B & C & D --> E{Apply status/finalist\nto shortlist}

    E --> F[Aim for 3× the number\nof final winners\ne.g. 9 finalists for 3 winner slots]

    F --> G[Coordinate with\nother judges]
    G --> H[Reach consensus on\nfinal finalist list]
    H --> I[Notify organizer:\nReady for winner selection run]
```

---

## Approving the Winner Announcement

```mermaid
sequenceDiagram
    actor Organizer
    actor Judge
    participant Actions as GitHub Actions
    participant Env as Results Environment
    participant GH as GitHub

    Organizer->>Actions: Run select-winners dry_run=false

    Actions->>Env: Request environment protection approval
    Env->>Judge: 🔔 Email + in-app notification:\n"Deployment to Results waiting for approval"

    Judge->>Actions: Navigate to Actions tab
    Judge->>Actions: Find pending Winner Selection run
    Judge->>Actions: Click the run → Review deployments button

    Note over Judge,Actions: You see: environment name,\nwhich branch, who requested it

    Judge->>Judge: Cross-check against\ndry run summary you approved earlier

    alt Judge approves
        Judge->>Env: Click Approve and deploy
        Env-->>Actions: Approval granted
        Actions->>GH: Apply status/winner labels
        Actions->>GH: Apply status/finalist labels
        Actions->>GH: Create GitHub Release\nwith winner announcement
        GH-->>Organizer: 🔔 Release published
    else Judge rejects
        Judge->>Env: Click Reject\nAdd reason in comment
        Env-->>Organizer: 🔔 Deployment rejected
        Organizer->>Judge: Discuss changes needed
    end
```

---

## Common Issues & How to Handle Them

```mermaid
flowchart TD
    subgraph Issues["Common Situations"]
        A1[Two submissions\nhave identical ideas]
        A2[AI gave 9/10 Technical\nbut repo has no code]
        A3[Submission describes\na brilliant niche idea\nbut got 4/10 Innovation]
        A4[Winner candidate's\nrepo is private]
        A5[Score looks right but\nteam violated CoC]
    end

    subgraph Actions["Recommended Actions"]
        B1[Check submission dates\nEarlier submission takes precedence\nNotify organizer of duplicate]
        B2[Override Technical score manually\nLeave judge comment explaining\nthat repo was inspected]
        B3[Research the domain\nIf genuinely novel override to 7–8\nLeave comment with reasoning]
        B4[Ask organizer to contact team\nSubmission must use public repo\nMay need to disqualify]
        B5[Notify organizer immediately\nDisqualification decision belongs\nto organizer not judges]
    end

    A1 --> B1
    A2 --> B2
    A3 --> B3
    A4 --> B4
    A5 --> B5
```
