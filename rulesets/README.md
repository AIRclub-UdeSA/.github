# Branch rulesets

`protect-main.json` is the baseline ruleset applied to `main` in every repository of the organization. It lives here so the configuration is reviewed in a pull request instead of only existing in each repository's settings UI, where it drifts quietly.

## What it enforces

- `main` cannot be deleted or force-pushed.
- Changes go through a pull request with one approving review, and approvals are dismissed when new commits are pushed.
- The `check-commit-messages / check-commit-messages` status check must pass.

Repository admins can bypass all of it. That is deliberate: it is a speed bump for everyday work, not a gate against the people who own the repositories.

## Why the check name looks doubled

The automations run as reusable workflows, and GitHub names such a check `<caller job> / <called job>`. The context is therefore `check-commit-messages / check-commit-messages`, not `check-commit-messages`. Requiring the short name silently blocks every pull request forever: the check that would satisfy it is never reported, so it sits at "Expected — waiting for status to be reported".

The same trap applies to a branch that predates the migration to reusable workflows. It still reports the short name, so it will not satisfy the requirement until it is rebased onto `main`.

## Per-repository additions

Some repositories require extra checks on top of the baseline:

| Repository | Extra required check |
| --- | --- |
| `physical_rosmaster` | `ROS 2 Humble build and tests` |

`yahboom_rosmaster` deliberately does **not** require `ROS 2 Humble / Gazebo Fortress`. That job fails intermittently for reasons unrelated to the change under test (see yahboom_rosmaster#31), and requiring a flaky check trains everyone to reach for the bypass, which costs more than it protects. Add it once the flakiness is fixed.

## Applying it

```bash
gh api -X POST repos/AIRclub-UdeSA/<repo>/rulesets --input rulesets/protect-main.json
```

Use `-X PUT repos/AIRclub-UdeSA/<repo>/rulesets/<id>` to update a ruleset that already exists.
