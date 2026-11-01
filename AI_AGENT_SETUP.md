# AI Agent V1 — Setup and Operations

## Components

- `.github/workflows/ai-review.yml`: read-only Codex review on pull requests, manual dispatch, and weekly schedule.
- `.github/workflows/ai-autofix.yml`: safe auto-fix for non-draft pull requests from branches in this repository; creates a separate PR for review.
- `.github/prompts/ai-review.md`: review criteria and report format.
- `.github/prompts/ai-autofix.md`: restricted auto-fix authority.
- `ai-instruction.md`: repository-wide operating rules and project invariants.

## Required GitHub configuration

1. Create an OpenAI API key for the Codex GitHub Action.
2. In the repository, open **Settings → Secrets and variables → Actions → New repository secret**.
3. Add the secret named `OPENAI_API_KEY`. Never commit the key or put it in workflow variables or files.
4. Keep the repository's default workflow-token permission at **Read repository contents** where possible. The workflow declares write access only on the isolated PR-publication job.
5. Enable **Allow GitHub Actions to create and approve pull requests** under **Settings → Actions → General**. The workflow creates PRs but does not approve or merge them.

## Triggers

| Workflow | Trigger | Behavior |
| --- | --- | --- |
| AI Repository Review | Pull request opened/updated/reopened, manual dispatch, weekly Monday 09:00 Asia/Makassar | Reviews changes or the checked-out repository and publishes a report. |
| AI Safe Auto-Fix | Non-draft pull request opened/updated/reopened from a branch in this repository | Attempts only low-risk fixes and opens a separate PR if a patch is produced. |

Scheduled runs begin only after this workflow file is merged into the repository's default branch. Pull requests from forks are intentionally excluded from auto-fix because secrets and trusted write access must not be exposed to untrusted code.

## Security model

- Review job: repository read-only; Codex sandbox is `read-only`; no GitHub write token is persisted.
- Auto-fix generation job: repository read-only GitHub token; Codex sandbox is `workspace-write`; no GitHub write credential is available to Codex.
- PR publication job: receives GitHub write permissions to create a branch and PR; it does not receive `OPENAI_API_KEY`.
- No workflow has permission to merge a PR or change repository settings.
- Required reviews and branch protection remain in force.
- PR content and repository text are untrusted input; prompts explicitly guard against prompt injection.

## Current limitations

- The workflow requires a valid `OPENAI_API_KEY` secret and Actions permission to create pull requests. These settings must be completed by a repository administrator.
- The initial repository may not yet contain an application manifest or test suite. The agent must inspect the actual tree and must not invent or run unavailable checks.
- The GitHub connector can create workflow files but cannot execute GitHub Actions or confirm their runtime behavior. Verify the first runs in the repository's **Actions** tab.
- A generated auto-fix PR is never automatically approved or merged.

## Manual test

1. Add `OPENAI_API_KEY` and enable the required Actions PR-creation setting.
2. Merge this setup through the normal reviewed PR process.
3. Open a small test PR from a branch in this repository.
4. Confirm **AI Repository Review** posts a report.
5. Confirm **AI Safe Auto-Fix** either reports no safe change or opens a separate `codex/auto-fix-pr-*` PR.
6. Inspect the generated diff and verify all checks before merging anything.

## Official references

- [Codex GitHub Action](https://learn.chatgpt.com/docs/github-action)
- [Codex non-interactive mode and safe auto-fix pattern](https://learn.chatgpt.com/docs/non-interactive-mode)
- [OpenAI Codex Action source](https://github.com/openai/codex-action)
- [GitHub Actions documentation](https://docs.github.com/en/actions)
