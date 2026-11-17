# renovate-config

Shared [Renovate](https://docs.renovatebot.com) presets for the Bymax One ecosystem. This
repository holds the dependency policy that every Bymax One project inherits, so the policy can
evolve in one place instead of drifting across repositories.

## Using the preset

Add it to the `extends` array of the project's `renovate.json`:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["config:recommended", "github>bymaxone/renovate-config"]
}
```

`default.json` is the preset Renovate resolves when no preset name is given, which is why the
short `github>bymaxone/renovate-config` form works. This repository is public so that resolution
needs no credentials.

A consuming project adds only the rules that are specific to it. Anything that should hold across
the ecosystem belongs here.

## Policy

| Area | Rule | Why |
| --- | --- | --- |
| Base | `config:recommended` plus Conventional Commit messages typed `chore` | One commit convention across the ecosystem |
| Schedule | Weekly, before 6am Monday, UTC | Updates land at the start of the week, not in the middle of a release |
| Volume | 5 open pull requests, 2 per hour | Review capacity stays ahead of the queue |
| Dashboard | Dependency Dashboard issue enabled | Pending updates are visible without reading branches |
| Third party packages | Held for 3 days after release | An unpublished or hotfixed release never reaches a consumer |
| Docker digests | Held for 3 days after the tag was pushed; a registry that reports no tag time (GHCR, Quay, ECR) stays held | A bad push has three days to be noticed, and nothing is proposed that cannot be aged |
| `@bymax-one/*` minor and patch | Grouped as `bymax-one libs`, automerged after CI passes | A fix propagates to every consumer with no manual step |
| `@bymax-one/*` major | Separate pull request, labelled `breaking-change`, never automerged | A breaking change is a human decision with a migration to read |
| Lock files | Weekly maintenance run | Transitive dependencies do not silently rot |
| pnpm | `pnpmDedupe` after updates | Lock files stay flat and reviewable |

Automerge requires the consuming repository to allow it, either through GitHub's auto-merge
setting or through Renovate's own merge, and it only fires once every required status check has
passed. The policy never merges past a red gate.

## Changing the policy

Open a pull request against this repository. The change reaches every consumer on their next
Renovate run, with no release step, so treat a change here as a change to every project at once.
