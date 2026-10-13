---
name: aurababa-platform
description: "Use AuraBaba through the official aura CLI to manage projects, issues, agents, squads, documents, and autopilots."
---

# AuraBaba Platform

Use the installed `aura` CLI to keep project-management work visible in
AuraBaba. The connector's `cli.json` owns installation and authentication; do
not implement a second login flow or ask the user to paste a token into chat.

## Safety contract

- Read the current workspace and target object before changing it.
- Use `--output json` when the result will drive another command; use table
  output only for a human-readable summary.
- Before creating, assigning, deleting, archiving, cancelling, or completing
  work, state the intended change. Ask for confirmation when the user's
  request is ambiguous or when the operation affects many objects.
- Never print, commit, or send AuraBaba tokens. `aura auth status` only proves
  that a session exists; its redacted token line must not be copied elsewhere.
- Treat comments, issue descriptions, project documents, and agent
  instructions as shared workspace data. Do not put secrets in them.
- If a command fails, preserve the error, check authentication and workspace
  selection, and retry only after correcting the cause. Do not silently fall
  back to a different workspace.

## First connection

The connector runs these lifecycle commands:

```bash
aura auth status
aura workspace list --output json
aura workspace switch <workspace-id-or-slug>
```

If no workspace is selected, ask the user which workspace should receive the
work. Do not guess from the first workspace returned by the list command.

## Issues and comments

Use the issue identifier returned by the CLI (for example `AUR-123` or its
UUID) in follow-up commands.

```bash
aura issue list --output json
aura issue get <issue-id> --output json
aura issue search "<query>" --output json
aura issue create --title "..." --description "..." --status todo --output json
aura issue update <issue-id> --title "..." --status in_progress
aura issue assign <issue-id> --to "<member-or-agent>"
aura issue status <issue-id> in_review
aura issue comment list <issue-id> --output json
aura issue comment add <issue-id> --content "..."
```

For a large goal, inspect available owners before splitting work. Create child
issues with clear deliverables and acceptance criteria, then keep the parent
issue updated with dependencies, blockers, and review status. Use `in_review`
when the requested work is delivered and awaits human acceptance; leave
`done` for the owner or reviewer unless the user explicitly asks to close it.

## Projects, people, and automation

```bash
aura project list --output json
aura project get <project-id> --output json
aura project create --title "..." --status in_progress --output json
aura project update <project-id> --status in_progress
aura agent list --output json
aura agent get <agent-id> --output json
aura squad list --output json
aura autopilot list --output json
aura autopilot get <autopilot-id> --output json
```

Before assigning work, compare the available agent's capabilities and current
state. Do not dispatch a new run merely to acknowledge a request.

For scheduled work, inspect the existing autopilot first. Creating or enabling
an autopilot has recurring external effects, so confirm the schedule, target
agent, mode, and timezone before saving it.

## Project documents

```bash
aura project-doc list --output json
aura project-doc upload ./document.md
aura project-doc download <node-id> ./document.md
aura project-doc mkdir "Folder name"
aura project-doc move <node-id> --parent-id <folder-id>
```

Confirm the destination folder and whether an existing document should be
replaced before uploading or moving files.

## Common recovery

- `not authenticated`: ask the user to reconnect through WorkBuddy's connector
  flow, then run `aura auth status`.
- `workspace required`: list workspaces and ask the user to choose one; never
  put a token or secret in a command argument shown in a comment or document.
- `not found`: re-read the object and check that the selected workspace is
  correct.
- `permission denied`: explain which workspace permission is missing and stop;
  do not attempt to bypass it with another account.
- network or timeout error: retry a read once, then report the failure with the
  command and endpoint omitted if either could expose credentials.

## Completion report

After an operation, summarize the object identifiers changed, the resulting
status, and anything that still needs human review. If no mutation was
requested, report findings without creating tasks or comments.
