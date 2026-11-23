---
name: "pretty-commit"
description: "Triggered when the user requests a commit or partial staging. This skill isolates specific lines of code using interactive Git staging (hunks/line staging) and creates highly standardized commits."
---

# Rules

- [IMPORTANT] This commit skill is lightweight and performant. It is recommended to run it using faster models (e.g. Flash) to save tokens. If the current model is High/Pro, the agent should suggest in chat that the user switch to Flash for simple operations.
- Whenever the user asks for a commit, they must provide a `context/summary` of what was done to guide the agent.
- If the user does not provide additional `context/summary`, the agent must ask: "What changes did you make that you'd like to include in this commit?"
- It is important to inspect the `user's context/summary` FIRST, then run `git diff` to locate matching hunks/line stagings — isolating those chunks wherever possible before staging them with `git add`.
- Use the `conventional commits` format for commit messages:
  - `<operation>: <message>`
    Operations:
  - feat -> A new feature added to the system.
  - fix -> A bug fix or resolution of unexpected behavior.
  - refactor -> Structural change that neither fixes a bug nor adds a feature (code cleanup).
  - style -> Formatting, whitespace, or semicolon changes only (no logic change).
  - docs -> Changes exclusively to documentation files (e.g. README.md).
  - perf -> Code changes strictly focused on performance improvements.
  - chore -> Updates to build tasks, packages, infrastructure, or config files.
  - ci -> Changes to CI/CD integration and deployment scripts (e.g. GitHub Actions).
    Message:
  - Use the imperative mood: write the message as a command or present instruction (e.g. "add", "fix").
  - Lowercase: the first letter following `<operation>: ` should not be capitalized.
  - No trailing period: do not end the line with a period or special punctuation.
  - Character limit: keep the subject line between 50 and 72 characters max.
  - Commit message language: English.
- Emojis are allowed and encouraged.

# Steps

1. If the user did not provide additional context/summary, ASK THEM. THIS IS MANDATORY.
2. Run `git status` and `git diff` to inspect all changes related to the user's request.
3. If there are changes outside the user's requested scope, identify the target files/lines and run `git add -p <file_path>` to interactively pick lines. Otherwise, stage the files directly with `git add` and proceed to step 9.
4. When the interactive Git prompt opens, send `s` to split the hunk into smaller chunks if needed.
5. Enter `y` only on chunks matching the current task, and `n` on others.
6. Enter `q` to quit interactive staging early if finished before end of file.
7. Verify the staged changes using `git diff --cached` before creating the commit.
8. If anything looks incorrect, unstage/re-stage based on previous steps.
9. Generate the commit message following conventional commits (as detailed in RULES).
10. Output the proposed commit message and ask the user for confirmation.

# Template

[operation]: message

features on this commit:

- <detailed change 1 based on staged lines>
- <detailed change 2 based on staged lines>
