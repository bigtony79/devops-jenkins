# Multibranch Pipeline

- In modern Git workflows, teams work on **feature**, **bugfix**, and **hotfix** branches instead of committing directly to `main`.
- You want **fast feedback** (build + test) on every branch, and **deployment** to happen only from trusted branches like `main`.
- Jenkins **Multibranch Pipeline** automates this by **discovering branches/PRs** and creating a **dedicated pipeline per branch** based on the branch’s own `Jenkinsfile`.

## What Multibranch Pipelines Solve

1. **Branch Isolation**
    - Every branch gets its **own pipeline** run with its **own Jenkinsfile** (so behavior can vary by branch).
    - Ensures features are validated early without affecting `main`.
2. **Automatic Discovery**
    - Jenkins scans the repo (via webhook or polling), **detects new branches/PRs**, and instantly creates pipelines—no manual job creation.
3. **Policy Enforcement**
    - You can enforce rules like **“test on all branches, deploy only from main”** via `when { branch 'main' }` or naming conventions (`release/*`, `hotfix/*`).
4. **Event-Driven CI**
    - On **push**, the branch pipeline builds and tests.
    - On **merge to main**, Jenkins runs **test → build → deploy**.
 
