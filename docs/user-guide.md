# GitHub Hackathon Platform — User Guide

This guide covers three roles: **Participants** (who submit ideas and solutions), **Platform Owners** (organizers who run the hackathon), and **Judges** (who review and finalize scores).

---

## Table of Contents

- [For Participants](#for-participants)
  - [Getting Started](#getting-started)
  - [Browsing Challenges](#browsing-challenges)
  - [Submitting an Idea](#submitting-an-idea)
  - [Submitting a Solution](#submitting-a-solution)
  - [Tracking Your Submission](#tracking-your-submission)
  - [Understanding Your Evaluation Score](#understanding-your-evaluation-score)
  - [Appealing a Score](#appealing-a-score)
  - [Community & Discussions](#community--discussions)
- [For Platform Owners](#for-platform-owners)
  - [First-Time Setup](#first-time-setup)
  - [Posting a Challenge](#posting-a-challenge)
  - [Managing the Submission Phase](#managing-the-submission-phase)
  - [Advancing Hackathon Phases](#advancing-hackathon-phases)
  - [Monitoring Submissions](#monitoring-submissions)
  - [Running Evaluations Manually](#running-evaluations-manually)
  - [Selecting and Announcing Winners](#selecting-and-announcing-winners)
  - [Managing the Leaderboard](#managing-the-leaderboard)
- [For Judges](#for-judges)
  - [Accessing the Judging Board](#accessing-the-judging-board)
  - [Reviewing AI Evaluation Scores](#reviewing-ai-evaluation-scores)
  - [Marking Finalists](#marking-finalists)
  - [Approving Winner Selection](#approving-winner-selection)
  - [Scoring Rubric Reference](#scoring-rubric-reference)

---

## For Participants

### Getting Started

You only need a **GitHub account** — no separate registration required.

1. Go to the hackathon repository: `https://github.com/akashtalole/gh-hackathon-platform`
2. Star/Watch the repository to receive notifications about announcements
3. Join the community in [GitHub Discussions](https://github.com/akashtalole/gh-hackathon-platform/discussions)
4. Read the [Rules & Guidelines](https://akashtalole.github.io/gh-hackathon-platform/rules.html)

> **Team size:** 1–5 members. One team member submits on behalf of the team; list everyone's GitHub username in the form.

---

### Browsing Challenges

Challenges are posted as GitHub Issues with the `type/challenge` label.

**Option A — GitHub UI:**
1. Go to the [Issues tab](https://github.com/akashtalole/gh-hackathon-platform/issues)
2. Filter by label `type/challenge`
3. Click any challenge to read the full problem statement, success criteria, and available resources

**Option B — Hackathon website:**
1. Visit the [landing page](https://akashtalole.github.io/gh-hackathon-platform/)
2. Scroll to the **Active Challenges** section
3. Click **View Challenge →** to open the full details on GitHub

---

### Submitting an Idea

An Idea submission describes your planned approach *before* you build. It is optional but encouraged — it helps organizers track teams and provides a record of your original thinking.

1. Go to [New Issue](https://github.com/akashtalole/gh-hackathon-platform/issues/new/choose)
2. Click **Get started** next to **Idea Submission**
3. Fill in the form:

   | Field | What to write |
   |-------|--------------|
   | **Idea Title** | A memorable project name, e.g. `EcoTrack — Gamified Carbon Tracker` |
   | **Related Challenge Issue Number** | The `#number` of the challenge you're addressing, e.g. `#5` |
   | **Elevator Pitch** | 2–3 sentences: what it does, for whom, and how |
   | **Proposed Solution** | Technical approach, key features, planned tech stack |
   | **What Makes This Innovative?** | Your novel insight vs. existing solutions |
   | **Team Size** | Select from dropdown |
   | **Team Members** | GitHub usernames of all members, e.g. `@alice, @bob` |
   | **Primary Technology Domain** | Select from dropdown |
   | **GitHub Repository** | Optional — link your repo if already created |

4. Check all boxes in the **Submission Agreement** section
5. Click **Submit new issue**

Your idea will be labeled `type/idea` and `status/pending-review` automatically.

---

### Submitting a Solution

A Solution is your **final competition entry** for judging. Submit only when your project is complete. You can edit the issue until the submission deadline.

> **Pre-requisite:** Your project must be in a **public GitHub repository** with a `README.md` that includes setup instructions.

1. Go to [New Issue](https://github.com/akashtalole/gh-hackathon-platform/issues/new/choose)
2. Click **Get started** next to **Solution Submission**
3. Fill in the form:

   | Field | What to write |
   |-------|--------------|
   | **Project Name** | Your project's name |
   | **Challenge Issue Number** | The `#number` of the challenge you solved |
   | **Idea Issue Number** | Optional — link your earlier Idea issue if you submitted one |
   | **Project Repository URL** | Full URL to your public GitHub repo |
   | **Live Demo URL** | Optional — link to a deployed app, video demo, or screenshots |
   | **Project Description** | What you built, for whom, and how it works |
   | **Technical Implementation** | Architecture, key decisions, tech stack |
   | **Innovation & Impact** | What's novel and the potential real-world benefit |
   | **Challenges Faced** | Main obstacles and how you overcame them |
   | **Team Members** | GitHub usernames of all members |

4. Complete the **Submission Checklist** (all items required)
5. Click **Submit new issue**

Your solution will be labeled `type/solution` and `status/pending-evaluation` automatically. The AI evaluation pipeline will trigger within minutes once an organizer reviews and approves it.

---

### Tracking Your Submission

After submitting, you can track your submission's status through its labels:

| Label | Meaning |
|-------|---------|
| `status/pending-review` | Organizer has not yet reviewed your submission |
| `status/pending-evaluation` | Approved and queued for AI evaluation |
| `status/evaluating` | AI evaluation pipeline is running right now |
| `status/evaluated` | Scores have been posted as a comment on your issue |
| `status/needs-more-info` | Organizer has asked a question — check issue comments |
| `status/finalist` | You've been shortlisted! Human judges are reviewing |
| `status/winner` | 🏆 You won! |
| `status/disqualified` | See issue comments for reason |

**To get notified:** Click the **Subscribe** button on your issue. You'll get an email when labels change or comments are posted.

---

### Understanding Your Evaluation Score

After evaluation, the AI model posts a structured comment on your issue with this format:

```
## AI Evaluation Report

> Model: gpt-4o | Date: YYYY-MM-DD

### Scores

| Dimension        | Score | Rationale                              |
|------------------|-------|----------------------------------------|
| Innovation       |  7/10 | Novel use of real-time data streaming  |
| Technical Quality|  8/10 | Clean architecture with solid testing  |
| Documentation    |  6/10 | README is clear but lacks API docs     |
| Feasibility      |  8/10 | Working MVP demonstrates viability     |
| Impact           |  7/10 | Solves a real problem for urban users  |
| **Total**        |**36/50**|                                      |

### Strengths
- Well-structured codebase with clear separation of concerns
- ...

### Areas for Improvement
- API documentation is missing
- ...

### Summary
EcoTrack demonstrates strong technical execution...
```

Scores are on a **0–10 scale per dimension**, with a **50-point maximum total**. The [live leaderboard](https://akashtalole.github.io/gh-hackathon-platform/leaderboard.html) updates automatically.

---

### Appealing a Score

If you believe a dimension was scored incorrectly:

1. Go to [GitHub Discussions → Appeals](https://github.com/akashtalole/gh-hackathon-platform/discussions)
2. Open a new discussion
3. Include: your issue number, which dimension you're appealing, and your reasoning
4. Appeals must be filed within **48 hours** of score publication
5. An organizer will review and may trigger a re-evaluation

---

### Community & Discussions

Use [GitHub Discussions](https://github.com/akashtalole/gh-hackathon-platform/discussions) to:

| Category | Purpose |
|----------|---------|
| **Announcements** | Official updates from organizers (read-only for participants) |
| **Q&A** | Ask technical questions about challenges or the platform |
| **Ideas** | Brainstorm informally before submitting a formal Idea issue |
| **Show & Tell** | Share progress updates, demos, or experiments |
| **Appeals** | Contest an evaluation score |

> **Tip:** Search existing discussions before posting — your question may already be answered.

---

---

## For Platform Owners

### First-Time Setup

Run through these steps **once** before the hackathon opens.

#### Step 1 — Bootstrap labels, milestones, and Projects v2

1. Go to **Actions** tab → **Repository Bootstrap Setup**
2. Click **Run workflow**
3. Fill in:
   - **GitHub Projects v2 board name** — e.g. `Hackathon 2026 Judging Board`
   - **Display name** — e.g. `GitHub Hackathon 2026`
4. Click **Run workflow**
5. Wait for it to complete, then open the workflow summary
6. Copy the **PROJECT_NUMBER** shown in the output

#### Step 2 — Save the Project Number

1. Go to **Settings → Secrets and variables → Actions → Variables**
2. Click **New repository variable**
3. Name: `PROJECT_NUMBER`, Value: *(the number from Step 1)*
4. Optionally add `EVAL_MODEL` = `gpt-4o` (or another model from GitHub Models)

#### Step 3 — Enable GitHub Pages

1. Go to **Settings → Pages**
2. Source: **Deploy from a branch**
3. Branch: `main`, Folder: `/docs`
4. Save. Your site will be live at `https://akashtalole.github.io/gh-hackathon-platform/`

#### Step 4 — Enable Discussions

1. Go to **Settings → General → Features**
2. Check **Discussions**
3. Go to the **Discussions** tab and create these categories:

   | Category name | Type | Description |
   |--------------|------|-------------|
   | Announcements | Announcement | Official updates (only organizers can post) |
   | Q&A | Q&A | Participant questions |
   | Ideas | Open-ended | Informal idea sharing |
   | Show & Tell | Open-ended | Progress updates and demos |
   | Appeals | Open-ended | Score appeal requests |

#### Step 5 — Create Environments

1. Go to **Settings → Environments**
2. Create four environments: `Registration`, `Submission`, `Judging`, `Results`
3. For the **Results** environment only:
   - Click **Required reviewers**
   - Add yourself (and any lead judges)
   - This creates an approval gate before winners are announced

#### Step 6 — Open the Hackathon

Run the **Phase Transition Gate** workflow with `target_phase: Registration` (see [Advancing Hackathon Phases](#advancing-hackathon-phases)).

Post the suggested announcement text in **Discussions → Announcements**.

---

### Posting a Challenge

Challenges are created using the same Issue Form flow that participants use.

1. Go to [New Issue](https://github.com/akashtalole/gh-hackathon-platform/issues/new/choose)
2. Click **Get started** next to **Challenge Submission**
3. Fill in:
   - A clear problem statement with real-world context
   - 3–5 measurable success criteria
   - Available resources / APIs / datasets
   - Prize description
4. After submitting, add the appropriate `category/*` label (e.g. `category/sustainability`)
5. Add the challenge to the active **Milestone** (e.g. `Registration Phase`)

---

### Managing the Submission Phase

When a participant submits a Solution, it is automatically labeled `status/pending-evaluation`. The evaluation pipeline only fires after the label is confirmed — **no action is needed from you** unless you want to review before evaluation.

**To reject / request more info:**
1. Open the submission issue
2. Remove `status/pending-evaluation`
3. Add `status/needs-more-info`
4. Leave a comment explaining what's needed

**To approve and trigger evaluation:**
- The label `status/pending-evaluation` triggers evaluation automatically
- If you removed it, re-add it to re-trigger

**To disqualify a submission:**
1. Add label `status/disqualified`
2. Leave a comment with the reason
3. Close the issue

---

### Advancing Hackathon Phases

The platform has four phases: **Registration → Submission → Judging → Results**. Advance them using the **Phase Transition Gate** workflow.

1. Go to **Actions → Phase Transition Gate**
2. Click **Run workflow**
3. Select `target_phase` from the dropdown
4. Type `CONFIRM` in the confirmation field
5. Click **Run workflow**

The workflow will:
- Apply the `phase/<name>` label to all open submission issues
- Remove the previous phase label
- Output an announcement template in the workflow summary — copy it to **Discussions → Announcements**

| Phase | What it means |
|-------|--------------|
| `Registration` | Challenges are live; teams browse and register |
| `Submission` | Solution submissions are open |
| `Judging` | Submissions closed; evaluation and review underway |
| `Results` | Winners announced; hackathon complete |

> **Note:** The `Results` phase requires approval from environment reviewers before the workflow can complete (configured in Step 5 of setup).

---

### Monitoring Submissions

**GitHub Projects v2 board:**
1. Go to the **Projects** tab
2. Open the hackathon judging board
3. Switch between views:
   - **Board view** — Kanban with columns by Evaluation Status
   - **Table view** — All submissions with scores in sortable columns
   - Filter by `type/solution` to focus on entries eligible for judging

**Labels view:**
- Go to **Issues** and filter by label to see all submissions by status

**Leaderboard:**
- Visit `https://akashtalole.github.io/gh-hackathon-platform/leaderboard.html`
- Updates automatically every 6 hours and after each evaluation

---

### Running Evaluations Manually

The evaluation pipeline triggers automatically when a solution is labeled `status/pending-evaluation`. To manually re-trigger:

1. Open the submission issue
2. Remove label `status/evaluated` (if present)
3. Remove label `status/pending-evaluation` (if present), then re-add it

To force-run the leaderboard update:
1. Go to **Actions → Update Leaderboard**
2. Click **Run workflow → Run workflow**

---

### Selecting and Announcing Winners

> **Always run a dry run first.**

#### Dry Run (preview)

1. Go to **Actions → Winner Selection**
2. Click **Run workflow**
3. Set `top_n` = number of winners (e.g. `3`)
4. Set `dry_run` = `true`
5. Click **Run workflow**
6. Review the output in the workflow summary — check names, scores, and AI-generated citations

#### Live Run (apply labels + create Release)

1. Repeat the above with `dry_run` = `false`
2. The workflow requires **approval** from the `Results` environment reviewer
3. Go to the pending workflow run and click **Review deployments → Approve**
4. The workflow will:
   - Apply `status/winner` and `status/finalist` labels to winning issues
   - Create a **GitHub Release** with the full winner announcement in Markdown

After the Release is created:
- Post the release link in **Discussions → Announcements**
- Run **Phase Transition Gate** with `target_phase: Results`
- Trigger a final **Update Leaderboard** run to capture winner badges

---

### Managing the Leaderboard

The leaderboard is a static HTML page at `docs/leaderboard.html` that reads from `docs/_data/leaderboard.json`. It is rebuilt automatically:
- After every AI evaluation (via workflow dispatch)
- Every 6 hours via cron schedule

To force a rebuild: **Actions → Update Leaderboard → Run workflow**.

The leaderboard supports live filtering and sorting in the browser — no rebuild needed for those.

---

---

## For Judges

### Accessing the Judging Board

1. You must have **read access** (or higher) to the repository
2. Go to the **Projects** tab → open the hackathon judging board
3. Switch to **Table view** for a full spreadsheet-style overview of all submissions with their AI scores

You can also browse submissions directly via Issues:
- Filter: `is:open label:type/solution label:bot/evaluated`

---

### Reviewing AI Evaluation Scores

Each evaluated submission has an **AI Evaluation Report** comment on its issue. To review:

1. Open any issue with label `status/evaluated`
2. Scroll to the comment from `github-actions[bot]`
3. Review the score table and rationale for each of the 5 dimensions

**What to look for when reviewing AI scores:**

| Dimension | Common AI blind spots |
|-----------|----------------------|
| **Innovation** | AI may under-score niche domains it knows less about |
| **Technical Quality** | AI only reads the description — it cannot run or inspect the actual code |
| **Documentation** | AI scores based on the submission text, not the linked repo README |
| **Feasibility** | AI may over-score if the description sounds confident but lacks evidence |
| **Impact** | AI may over-score broad claims without real evidence of reach |

> **Important:** AI scores are a starting point, not a final decision. Human judgment is required, especially for Technical Quality where the actual repository should be inspected.

---

### Marking Finalists

Once you have reviewed AI scores and inspected submission repositories:

1. Open the submission issue
2. Apply the `status/finalist` label to shortlisted entries
3. Optionally leave a private note in a [draft comment](https://docs.github.com/en/issues/tracking-your-work-with-issues/creating-an-issue) or coordinate with other judges via a private Discussion

**Suggested shortlisting process:**
1. Start with submissions scoring **35+/50** from the AI
2. Manually review the actual GitHub repository for each
3. Apply `status/finalist` to your top picks (aim for 3–5× the number of final winners)
4. Coordinate with other judges on the final shortlist before proceeding to winner selection

---

### Approving Winner Selection

The `select-winners.yml` workflow runs in the **Results** environment, which requires a reviewer approval before executing in live mode.

When the organizer runs the Winner Selection workflow with `dry_run: false`:

1. You will receive a **GitHub notification** (email + in-app) for a pending environment deployment
2. Go to **Actions** → find the pending **Winner Selection** run
3. Click the run → Click **Review deployments**
4. Review the dry-run output (shown in the previous run's summary)
5. Click **Approve and deploy** to confirm, or **Reject** to send back for revision

After approval, the workflow applies winner labels and creates the official GitHub Release.

---

### Scoring Rubric Reference

Use this rubric when manually reviewing or overriding AI scores.

#### Innovation (0–10)

| Score | Meaning |
|-------|---------|
| 0–2 | Trivially replicates existing solutions with no novel elements |
| 3–4 | Minor improvement over existing approaches |
| 5–6 | Meaningful new combination of technologies or approaches |
| 7–8 | Novel technical approach or genuinely new user experience |
| 9–10 | Breakthrough approach that fundamentally changes how the problem is solved |

#### Technical Quality (0–10)

| Score | Meaning |
|-------|---------|
| 0–2 | No working code, broken links, or trivial implementation |
| 3–4 | Basic implementation with significant gaps or bugs |
| 5–6 | Functional with reasonable architecture |
| 7–8 | Well-architected, clean code, appropriate technology choices |
| 9–10 | Production-quality decisions with exceptional engineering depth |

#### Documentation (0–10)

| Score | Meaning |
|-------|---------|
| 0–2 | No documentation or completely unclear submission |
| 3–4 | Minimal description, missing key information |
| 5–6 | Adequate description of the project and how to use it |
| 7–8 | Thorough README, clear setup, architecture explained |
| 9–10 | Exemplary docs with architecture diagrams, API docs, deployment guides |

#### Feasibility (0–10)

| Score | Meaning |
|-------|---------|
| 0–2 | Not realistically achievable, no credible path forward |
| 3–4 | High technical risk with unclear implementation path |
| 5–6 | Achievable with significant effort, realistic timeline |
| 7–8 | Well-scoped, achievable within reasonable constraints |
| 9–10 | Clear execution path with evidence of a working prototype or MVP |

#### Impact (0–10)

| Score | Meaning |
|-------|---------|
| 0–2 | Solves a trivial problem or affects very few people |
| 3–4 | Limited scope, marginal improvement |
| 5–6 | Meaningful impact for a defined audience |
| 7–8 | Significant potential impact for a broad audience |
| 9–10 | Transformative potential, scalable to millions |

---

*For questions about the platform itself, open a Discussion in the [Q&A category](https://github.com/akashtalole/gh-hackathon-platform/discussions).*
