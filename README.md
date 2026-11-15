# renovate-config

The shared [Renovate](https://docs.renovatebot.com/) preset for my app repos.
A repo uses it with a `renovate.json` of:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>ionut-maxim/renovate-config"]
}
```

and adds its own rules (custom managers, its own releases) below that.

What [`default.json`](default.json) does:

- **Weekly, grouped:** before 06:00 UTC on Mondays, after a 3-day release age.
  Go modules, cargo crates, npm packages, mise tools and GitHub Actions each
  come as one PR (minor and patch updates).
- **Toolchains move together:** Go, Node and Rust in `mise.toml` and the
  Dockerfile's builder image share a PR.
- **Patches merge themselves** once the repo's PR checks pass. A repo with no
  PR checks never automerges. Minors wait for review, and majors come as one
  PR each, also reviewed.
- **Actions pinned to commit SHAs** (`helpers:pinGitHubActionDigests`): a
  workflow runs exactly the code that was reviewed, even if an action's tag is
  moved later. The version stays as a comment, and updates bump both.
- **mise.lock:** a repo that commits `mise.lock` gets it rewritten in the same
  PR as the `mise.toml` bump, with the new versions' URLs and checksums.
- **Versions pinned in workflows:** a line such as `version: 2026.11.8` with
  `# renovate: datasource=github-releases depName=jdx/mise` above it is
  bumped too. mise itself joins the mise tools PR.
- **Security fixes** (GitHub alerts and OSV) skip the schedule and are
  labelled `security`.

The repo is private, so the Renovate app needs access to it as well as to the
repos that extend it. `homelab` doesn't use this preset; its rules are its own.
