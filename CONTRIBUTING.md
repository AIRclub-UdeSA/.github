# Contributing to AIR Club UdeSA

Thank you for helping improve an AIR Club UdeSA project. Issues and pull requests are welcome in either Spanish or English.

## Before you start

1. Read the target repository's `README.md` and existing contribution guide, if present.
2. Search open issues to avoid duplicate work.
3. Open an issue before starting a large change, changing a public interface, or proposing a new project direction.

## Prepare the change

- Keep each pull request focused on one objective.
- Follow the tools, versions, and conventions documented by the repository.
- Match the repository's primary language for user-facing documentation: JAR participant material is written in Spanish, while reusable robotics tooling is generally written in English.
- Add or update tests when behavior changes.
- Update documentation when commands, setup steps, or public interfaces change.
- Do not include secrets, credentials, personal data, or challenge details that have not been publicly announced.

## Submit the pull request

Explain what changed, why it is needed, and how you tested it. Include screenshots, short logs, or reproduction steps when they make the result easier to review.

## Setting up a new repository

Repositories tracked on the [JAR Challenge 2026 project board](https://github.com/orgs/AIRclub-UdeSA/projects/3) share a set of automations: new issues and pull requests are added to the board automatically, and their Status follows what happens to them (assigned, review requested, closed).

The logic lives once in this repository as reusable workflows, so each repo only needs a small caller file. **Copying the files is not enough** — steps 2 and 3 are organization settings that cannot be seen from inside the repository, and they are the ones people forget. If you skip them, the workflows fail when they try to authenticate.

### 1. Add the caller workflows

Copy the four files from [`workflow-templates/`](https://github.com/AIRclub-UdeSA/.github/tree/main/workflow-templates) into the new repository's `.github/workflows/`. Each one is a handful of lines that points at the shared workflow here.

### 2. Install the GitHub App on the repository

The automations authenticate as the `airclub-projects-bot` GitHub App, which is installed on selected repositories only.

Organization Settings → GitHub Apps → `airclub-projects-bot` → Configure → add the new repository under "Repository access".

Requires organization owner permissions.

### 3. Grant the repository access to the secrets

The App's credentials are stored as organization secrets scoped to selected repositories.

Organization Settings → Secrets and variables → Actions → for both `APP_ID` and `APP_PRIVATE_KEY`, add the new repository to the list.

Requires organization owner permissions.

### Why three of them use `pull_request_target`

`add-to-project`, `pr-review-status` and `assignee-status` trigger on `pull_request_target` rather than `pull_request`. This is deliberate: GitHub withholds repository secrets from `pull_request` runs whose head is a fork, so the App token step fails for every external contribution and the pull request never reaches the board.

`pull_request_target` is safe for these three because none of them checks out the contributor's code — they only read the event payload and call the Projects API. Do not apply the same change to `block-ai-coauthor`: that one does check out the pull request's code and needs no secrets, so running it with the base repository's permissions would be the well-known security hole.

### 4. Check that it works

Open a test issue in the new repository. It should appear on the project board within a few seconds. If it does not, look at the Actions tab: a missing secret fails with an explicit error naming it, and a missing App installation fails while generating the token.

By participating, you agree to follow our [Code of Conduct](https://github.com/AIRclub-UdeSA/.github/blob/main/CODE_OF_CONDUCT.md).
