# Git Flow – Talking Points

---

## Slide 1 · Title

- Introduce yourself and the topic: *"Today we're looking at Git Flow — a branching model that brings structure and predictability to how teams collaborate on code."*
- Set expectations: this isn't about Git basics, it's about **how to organize branches** across a team.
- Mention who it's most relevant to: developers, tech leads, anyone involved in the release process.
- Optional hook: *"Raise your hand if you've ever had a last-minute bug on release day that you weren't sure how to fix without breaking ongoing work — Git Flow solves exactly that."*

---

## Slide 2 · What is Git Flow?

- Git Flow was published in 2010 by **Vincent Driessen** in a now-famous blog post titled *"A successful Git branching model."*
- The key insight: Git itself is flexible — it doesn't tell you *how* to branch. Git Flow is an **opinionated convention** on top of Git.
- There are really only **two rules** at the heart of it:
  1. `main` is sacred — it only ever holds code that is live in production.
  2. Everything else flows through `develop`, which acts as a staging area.
- **When to recommend it:** teams shipping versioned software (mobile apps, packaged libraries, APIs with versioned contracts). If your team ships multiple times a day, simpler workflows like GitHub Flow may be a better fit.

---

## Slide 3 · The Branch Map

- Walk through the diagram **left to right** — it represents time.
- Point out the two horizontal "highways": `main` (green) and `develop` (blue).
- Feature branches (purple) **dip down** from develop and come back up — they never touch main.
- Release branches (yellow) go from develop → gets finalized → merges into **both** main and develop.
- Hotfix branches (red) are the only ones that branch *directly off main* — this is the emergency lane.
- Emphasize the **version tags** on main (`v1.0`, `v1.0.1`) — every production deployment should be traceable to a tag.
- Key message: *"At any point in time, you know exactly what's in production and what's in progress."*

---

## Slide 4 · Core Branches

- These two branches **live forever** for the life of the project — they are never deleted.
- **main:**
  - Think of it as a mirror of what's running in production right now.
  - A CI/CD pipeline can watch `main` and auto-deploy on every new commit.
  - The `--no-ff` merge flag is important — it preserves the history of where a change came from.
- **develop:**
  - This is where the team's daily work accumulates.
  - It can be thought of as "tomorrow's production."
  - Running automated tests against `develop` continuously is a best practice.
- The `git flow init` command sets all of this up interactively — it just creates the two branches and saves naming conventions to the repo config.

---

## Slide 5 · Feature Branches

- A feature branch represents **one unit of work** — one story, one ticket, one feature.
- The naming convention (`feature/`) makes it immediately clear what a branch is for just by looking at it in the branch list.
- **Never merge features directly into main** — they always go through develop first.
- The `--no-ff` flag (no fast-forward) creates a merge commit even when it's not strictly needed. This is intentional — it keeps the history readable so you can see "this is where feature X was integrated."
- **Publish / pull:** if multiple developers are working on the same feature, `git flow feature publish` pushes it to the remote so teammates can collaborate on it.
- Encourage short-lived feature branches — the longer they live, the more painful the eventual merge.
- Pair with **Pull Requests** for code review before the `finish` step.

---

## Slide 6 · Release Branches

- A release branch is created when `develop` has enough features for the next release and you want to **freeze** new feature additions.
- While the release branch is open, the team can continue working on `develop` for the *next* release — no blocking.
- Only **release-related** work goes here: bug fixes found during QA, updating changelogs, bumping version numbers in config files.
- When finished, it merges into **both** main (to deploy) and develop (to keep the fixes in sync).
- The tag (`v1.0.0`) on main is the permanent record — it ties the commit to a release artifact.
- This pattern enables parallel tracks: QA is hardening the current release while developers are already building the next one.

---

## Slide 7 · Hotfix Branches

- This is the **emergency procedure** — a critical bug is in production and needs to go out *now*.
- Because hotfix branches cut from `main`, you bypass all in-progress work on `develop` and feature branches entirely.
- After the fix is done, it merges into **main** (to deploy the fix) and **develop** (so the fix doesn't get lost in the next release).
- The patch version is bumped: `v1.0.0` → `v1.0.1`.
- Edge case to mention: *"What if there's an active release branch? The hotfix should also be merged into that release branch instead of (or in addition to) develop."*
- Key message: *"Hotfix branches let you ship an urgent fix in minutes, without risk of deploying half-finished features."*

---

## Slide 8 · Lifecycle Cheatsheet

- This is a quick-reference slide — great to bookmark or print.
- The `git flow` CLI tool is a wrapper that automates the branch creation, merging, tagging, and deletion steps.
- Without the CLI, all of this is just plain Git — the tool just saves you from typing multiple commands each time.
- **Installation options:**
  - macOS: `brew install git-flow-avh`
  - Ubuntu/Debian: `apt install git-flow`
  - Windows: `choco install gitflow-avh`
- `publish` pushes a branch to the remote origin — useful for collaboration or triggering remote CI on a branch.
- After `finish`, the CLI automatically deletes the local branch and checks out the target branch — one less thing to forget.

---

## Slide 9 · Benefits & Trade-offs

- Be honest with the audience — Git Flow is not a silver bullet.
- **Benefits to emphasize:**
  - The `--no-ff` merge policy keeps the log readable — you can `git log --graph` and visually see where each feature and release was integrated.
  - Parallel development is the biggest day-to-day win — multiple developers can work independently without stepping on each other.
  - Release branches give QA a stable target — once the release branch is cut, developers can keep committing to `develop` without affecting the release under test.
- **Trade-offs to be transparent about:**
  - For small teams (1–3 devs) or teams doing continuous delivery (many deployments per day), Git Flow adds overhead without much benefit — **GitHub Flow** (just main + feature branches + PRs) is simpler.
  - Long-lived feature branches accumulate merge debt. Encourage developers to merge small and often, or use feature flags to merge incomplete work safely.
- Good discussion question: *"Does your team do scheduled releases or continuous delivery? That answer usually determines which workflow fits best."*

---

## Slide 10 · Summary & Q&A

- Recap the five branch types in one sentence each — bring it back to the mental model.
- Reinforce the key principle: **main is sacred, develop is the hub, and everything else is temporary.**
- Practical next steps for the audience:
  1. Install `git-flow-avh` and run `git flow init` on your next project.
  2. Define your branch naming conventions as a team and document them.
  3. Set up branch protection rules on `main` and `develop` in your Git hosting platform (GitHub, GitLab, Bitbucket).
  4. Consider requiring PRs and at least one reviewer before merging into `develop`.
- Point to the linked resources for deeper reading.
- Open the floor for questions.

---

## General Tips for Presenting

- **Audience check early:** ask if everyone is comfortable with basic Git (commits, branches, merges) before diving in. Adjust depth accordingly.
- **Use a concrete scenario:** walking through a story like *"we're building v1.0 of an app, a critical bug is found the day before release"* makes the flow tangible.
- **Keep the diagram (Slide 3) on screen** during branch-type slides if possible — referring back to it helps people anchor the concepts visually.
- **Total estimated time:** ~20–30 minutes with discussion, ~12–15 minutes if presenting straight through.
