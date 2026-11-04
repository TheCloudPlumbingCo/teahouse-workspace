# teahouse-workspace

Where the teahouse team researches, plans and decides. Code lives in the repos listed in each workspace's `workspace.json`. Nothing from those repos is copied here.

| Workspace | Repos |
|---|---|
| `workspaces/be` | teahouse-core, teahouse-water-service, teahouse-tealeaf-service, teahouse-tea-service |
| `workspaces/ops` | teahouse-env, teahouse-load-gen |

## How work flows

1. A task is an **issue** in this repo, labelled with its workspace (`ws:be`, `ws:ops`).
2. **Research** is a pull request adding `workspaces/<ws>/research/<KEY>.md`. Merge = approved.
3. **Plan** is a pull request adding `workspaces/<ws>/plans/<KEY>.md`. Merge = approved.
4. **Decisions** are pull requests adding `workspaces/<ws>/decisions/NNNN-title.md`.
5. After a plan merges, child issues are opened in each affected code repo.

Progress is tracked on the org board: https://github.com/orgs/TheCloudPlumbingCo/projects/1

Branch names: `<ws>/research/<KEY>`, `<ws>/plan/<KEY>`, `<ws>/decision/<slug>`. Put `Refs #<issue>` in the PR body, never `Closes`.
