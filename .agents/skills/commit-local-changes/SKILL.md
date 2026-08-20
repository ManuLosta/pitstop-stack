---
name: commit-local-changes
description: "Review the current Git branch's local changes, synchronize project documentation through the installed sync-project-docs skill, and create a concise, well-documented commit in English. Use when the user asks to commit current work or create a commit describing recent local changes."
---

# Commit Local Changes

Review the changes currently present on the **active Git branch**, understand what was actually implemented, synchronize project documentation when needed, and create a single useful Git commit documenting the work.

## Workflow

1. **Check the repository state**
   - Run `git branch --show-current`.
   - Run `git status --short`.
   - Inspect both staged and unstaged changes:
     - `git diff`
     - `git diff --cached`
   - Inspect relevant untracked files when they are part of the work.
   - Do not switch branches.

2. **Understand the changes**
   - Read the actual diff rather than relying only on filenames.
   - Identify the main purpose of the changes and the meaningful implementation details.
   - Ignore generated files, build artifacts, dependency caches, and unrelated modifications unless they are intentionally part of the work.
   - Before staging, watch for secrets or sensitive files such as `.env`, credentials, private keys, tokens, or local configuration containing secrets. Do not commit them.

3. **Stage the intended changes**
   - Stage the changes that belong to the current piece of work.
   - Prefer explicit paths when unrelated changes exist.
   - Do not discard, reset, stash, or overwrite changes that are not part of the commit.
   - If the repository already has staged changes, preserve them unless they are clearly unrelated or unsafe to commit.

4. **Synchronize project documentation**
   - Before creating the commit, invoke the separately installed skill named `sync-project-docs` using the current host/agent's native skill invocation mechanism.
   - The invocation is mandatory. Wait for it to finish before continuing.
   - `sync-project-docs` reviews only the staged commit candidate and owns the decision about whether documentation needs updating.
   - Handle its result exactly as follows:
     - `DOCS_NOT_NEEDED` → continue without documentation changes.
     - `DOCS_UPDATED: <paths>` → stage exactly those documentation paths, then inspect `git diff --cached` again so the commit message reflects both code and documentation changes.
     - `DOCS_BLOCKED: <reason>` → do not create the commit. Report the blocker.
   - If the host cannot find or invoke the installed `sync-project-docs` skill, do not silently skip this step. Report that the required skill is unavailable and do not commit.
   - Do not ask `sync-project-docs` to commit, push, reset, stash, or alter unrelated files; the commit skill remains responsible for Git staging and commit creation.

5. **Create the commit**
   - Use a **Conventional Commits-style** subject:
     `type(scope): short description`
   - Choose the type based on the actual work, for example:
     - `feat` — new functionality
     - `fix` — bug fix
     - `refactor` — code restructuring without behavior change
     - `docs` — documentation
     - `chore` — maintenance/tooling
     - `test` — tests
     - `perf` — performance improvement
   - Keep the subject concise and in English.
   - Add a short body with enough context to document what changed. Do not write a long changelog.

## Commit Message Format

Use this structure:

```text
<type>(<scope>): <short description>

Summary:
- <meaningful change>
- <meaningful change>

Notes:
- <important implementation detail, behavior change, or relevant context>
```

The body should normally contain **2–4 bullets total**. Omit `Notes` when there is nothing meaningful to add.

Example:

```text
feat(auth): add session refresh handling

Summary:
- Added automatic token refresh before session expiration.
- Updated the API client to retry requests after a successful refresh.

Notes:
- Prevents users from being unexpectedly logged out during active sessions.
```

## Rules

- The commit message must be based on the **actual staged diff**, never guessed from the task description alone.
- Keep the commit informative but compact: it should document the work without becoming a detailed report.
- Do not include a generic message such as `update code`, `fix stuff`, or `changes`.
- Do not amend an existing commit unless explicitly requested.
- Do not push to a remote.
- Do not run destructive Git commands such as `git reset --hard` or `git clean`.
- If there are no changes to commit, report that clearly and do not create an empty commit.
- If changes appear to contain multiple unrelated pieces of work, create the commit only for the coherent current work and leave unrelated changes untouched.
- Documentation changes produced by `sync-project-docs` belong to the same commit that triggered them.
- After committing, run `git status --short` and report the created commit hash and commit message.
