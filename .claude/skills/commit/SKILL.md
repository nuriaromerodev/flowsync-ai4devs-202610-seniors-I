---
name: commit
description: Create a conventional commit (type(scope): description) from the currently staged changes.
disable-model-invocation: true
allowed-tools: Bash(git status:*), Bash(git diff:*), Bash(git log:*), Bash(git commit:*)
---

# Commit

Create one commit from the **staged** changes only.

## Steps

1. Run `git diff --staged` and `git status`. If nothing is staged, say so and stop. Do not stage anything yourself.
2. Run `git log -5 --oneline` to match the repository's existing style.
3. Write the message in Conventional Commits format: `type(scope): description`.
   - `type`: one of `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `build`, `ci`.
   - `scope`: the area touched, for example `frontend`, `backend` or `harness`. Omit it if the change spans unrelated areas.
   - `description`: imperative mood, lowercase, no trailing period, at most 72 characters in the first line.
   - Add a short body only when the reason for the change is not obvious from the diff.
4. Commit with `git commit -m "<message>"`.
5. Never add a `Co-Authored-By` trailer or any AI attribution.
6. Never use `--no-verify` or amend an existing commit.
7. Report the commit hash and the first line of the message.
