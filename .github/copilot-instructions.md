# Agent rules for teahouse-workspace

- This repo holds research, plans and decisions only. Never add application code here.
- Work only inside the workspace folder named on the issue (`workspaces/be` or `workspaces/ops`).
- Read a workspace's `workspace.json` to know which repos exist. Research may read those repos; do not copy their code into this repo.
- Research file: findings, which repos and files are affected, options, open questions. Cite repo, path and line.
- Plan file: one section per affected repo with the exact change, in the order to merge, and how to verify it.
- Always open a pull request. Never push to main. Use `Refs #<issue>`, not `Closes`.
