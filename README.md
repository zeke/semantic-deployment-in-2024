# Semantic Deployment, 2024 Edition

This is an opinionated workflow for automating away some of the tedium of releasing versioned software.

The idea is to add a bit of semantic information to each pull request as your write them, then let automation take care of 
writing changelogs, determining version numbers, and publishing release artifacts to registries like GitHub and npm.

## The ideal workflow

The contribution process works like this:

1. Contributors open pull requests, giving them titles with [semantic prefixes](https://www.conventionalcommits.org) like `fix:` and `feat:`.
1. When you merge a pull request, it's automatically added to a long-running Release pull request that accumulates changes.
1. When you're ready to cut a release, merge the Release pull request and a release is created automatically.

That's it! You never think about version numbers again.

## Tools

This workflow combines the following ideologies, specifications, and tools:

- [Semantic Versioning](https://semver.org) (aka SemVer), a methodology for applying version numbers to software.
- [Conventional Commits](https://www.conventionalcommits.org), a specification for adding human and machine readable meaning 
to commit messages
- [GitHub Actions](https://github.com/features/actions), a GitHub feature for automating your work.
- [Release Please](https://github.com/googleapis/release-please), a tool that automates CHANGELOG generation, the creation of GitHub releases, and version bumps for your projects.
- [Semantic Pull Request](https://github.com/amannn/action-semantic-pull-request), a GitHub action that validates pull request titles have semantic prefixes.

## Setting it up

🐢🚀 This guide caters to a Node.js crowd, but you can use this setup for [dozens of languages](https://github.com/googleapis/release-please?tab=readme-ov-file#strategy-language-types-supported)

1. Create a **Release** actions workflow that uses [release-please](https://github.com/google-github-actions/release-please-action). ([Example](https://github.com/replicate/create-replicate/blob/531e04e7a911eaa4f360e163d984777377707b72/.github/workflows/release.yml)).
1. Create a **Semantic Pull Request** actions workflow that uses [semantic-pull-request](https://github.com/amannn/action-semantic-pull-request) to lint your pull request titles. ([Example](https://github.com/replicate/create-replicate/blob/531e04e7a911eaa4f360e163d984777377707b72/.github/workflows/semantic-pull-request.yml))
1. Create a [GitHub Personal Access Token (PAT)](https://github.com/settings/tokens/new?scopes=repo,workflow) with _Repo_ and _Workflow_ scopes.
1. Add the GitHub PAT to your repo as a [Repository Secret](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions)
1. Create an npm token.
1. Add the npm token to your repo as a Repository Secret.
1. Configure your repo to allow _Read and write permssions_ for Actions workflows in Settings > Actions > General
1. Configure your repo to only allow squash merging, and set _Pull request title_ as the default commit message.

## Gotchas

- **Educating contributors** may be the hardest part. Some users have never used semantic prefixes before, so they may surprised at first when they open a pull request and see a failing check. Fortunately you as a maintainer have the power to change the title of a PR on behalf of a user, to make it pass the semantic check.
- **You still gotta push a few buttons**. The way release-please works is by accumulating changes in a Release PR. It's up to you to decide when to merge this PR and trigger a releas. This is arguably a feature and one of the fundamental ideas behind release-please, but it does mean you have to remember to go merge that Release PR to actually create a release.
- **Configure setup-node properly**. Make sure you set `registry-url` for the [setup-node](https://github.com/actions/setup-node) action in your release workflow, or your package will fail to publish.