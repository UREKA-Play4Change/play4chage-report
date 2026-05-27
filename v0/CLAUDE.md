# 🏗️ The Strict Engineering Workflow (SOP)

CRITICAL: Whenever I ask you to build a feature, fix a bug, or execute a task, you MUST follow this exact 6-step lifecycle using the terminal, Git, and the GitHub CLI (`gh`). Do not skip steps. Do not bulk-commit.

## Step 1: Deep Analysis & Issue Creation
Before writing any code, analyze the request. Use `gh issue create` to create a new GitHub Issue. The issue body MUST use this strict template:
*   **Context:** What is being asked.
*   **Analysis & Edge Cases:** What could go wrong? What dependencies are affected?
*   **Alternatives Considered:** At least two different approaches with Pros & Cons.
*   **Ideal Solution:** The chosen approach and why it is the most robust, "top-notch engineering" choice.
*   **Implementation Plan:** A checklist of files to change.

## Step 2: Branch Creation
Fetch the issue number you just created.
Create and checkout a new branch following this naming convention:
`<type>/<issue-number>-<short-kebab-case-description>` (e.g., `feat/42-auth-middleware`).

## Step 3: Granular Coding & Commits (ONE FILE PER COMMIT)
Write the code. **CRITICAL RESTRICTION:** You must commit files individually. NEVER run `git add .` or stage multiple files at once.
For EVERY modified file, execute a separate commit using this Conventional Commits standard:
*   **Title:** `<type>(<scope>): <clear description of what changed in this specific file>`
*   **Valid Types:** `feat`, `fix`, `chore`, `docs`, `refactor`, `test`.
*   **Message Body:** A detailed explanation of *why* this specific file was changed and how it works. Reference the issue number (e.g., `Addresses #42`).
* Always branch off `main` or `develop` before writing code. Never commit directly to the main branch.

## Step 4: Issue Update
Once the code is written, use `gh issue comment <issue-number>` to add a comment detailing the reality of the implementation:
*   **What was actually done:** Did it deviate from the original plan?
*   **Testing:** What manual or automated checks were run?

## Step 5: Pre-Push & PR Creation
1. Run the project's local test suite and linter (e.g., `npm run test`, `make test`, etc.) to ensure CI will likely pass. Fix any failures.
2. Push the branch to origin.
3. Open a Pull Request using `gh pr create`. 
   * The PR title must reflect the overall feature.
   * The PR body must include: `Closes #<issue-number>` to link them, a summary of the architecture, and a checklist of changes.

## Step 6: CI/CD & Auto-Merge
Do not merge blindly. 
1. Use `gh pr checks` to verify the CI/CD pipeline status.
2. If tests fail, fetch the logs, fix the code, commit (one file at a time), and push again.
3. Once CI passes (or to queue it up), use `gh pr merge --auto --squash` (or `--merge` depending on repo settings) so GitHub safely merges it into `main` ONLY when all checks go green.