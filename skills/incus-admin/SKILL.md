---
name: incus-admin
description: Safely plan and perform generic Incus administration through the incus CLI. Use when managing Incus instances, projects, profiles, networks, storage pools and volumes, snapshots, backups, restores, remotes, device passthrough, troubleshooting, or health checks.
license: MIT
compatibility: Requires the incus CLI. Environment-neutral; does not assume any host, remote, project, storage backend, network design, hardware device, or instance name.
---

# Incus Admin

Use this skill when helping manage Incus servers through the `incus` CLI.

Incus can manage system containers, application containers, and virtual machines. Treat every server as production-like unless the user explicitly says otherwise.

This skill is an environment-neutral operational guide. It must not contain, persist, or infer machine-specific information.

## Approved placeholders

Use placeholders instead of real environment details in examples, plans, saved notes, and shared output:

- `<REMOTE>`
- `<PROJECT>`
- `<INSTANCE>`
- `<PROFILE>`
- `<NETWORK>`
- `<POOL>`
- `<VOLUME>`
- `<SNAPSHOT>`
- `<IMAGE>`
- `<BACKUP_PATH>`
- `<DEVICE>`
- `<KEY>`
- `<VALUE>`

Do not invent additional angle-bracket placeholders. If another value is needed, describe it in prose or ask the user for it.

## Scope

This skill covers common Incus administration tasks:

- Instances, including containers and virtual machines
- Projects
- Profiles
- Networks
- Storage pools and volumes
- Snapshots
- Exports and backups
- Restore planning
- Remote configuration
- Device passthrough
- Troubleshooting and health checks

## Privacy and non-disclosure rules

Never include real environment details in this skill, committed files, examples, or saved outputs.

Do not store, hardcode, print, summarize, or commit:

- Hostnames
- Network addresses
- Hardware addresses
- PCI identifiers
- Disk identifiers or serial numbers
- Real instance names
- Real project names
- Real profile names
- Real network names
- Real storage pool names
- Real remote names
- Certificate fingerprints
- Trust tokens
- API tokens
- SSH keys
- Incus client or server private keys
- User-specific local paths
- API endpoints
- Private network topology
- Hardware model information
- Raw command output containing identifying data

Do not read, print, store, summarize, or copy sensitive local material such as:

- Incus server data directories
- Incus client or server configuration directories
- Local identity, certificate, or key material
- UID or GID mapping files
- SSH private keys or SSH configuration containing secrets
- API tokens or trust tokens
- Certificates containing private material

Never commit raw output from commands such as:

```bash
incus info
incus config show <INSTANCE>
incus profile show <PROFILE>
incus network show <NETWORK>
incus storage show <POOL>
incus storage volume show <POOL> <VOLUME>
```

If output is needed for discussion, redact it first and keep it outside the repository unless the user explicitly asks for a redacted artifact.

## Golden rules

1. Prefer read-only discovery before mutation.
2. Never run destructive commands directly.
3. Always identify the target remote, project, and object.
4. Do not assume the default remote or default project is safe for mutating operations.
5. Before L2 operations, show a plan and rollback strategy.
6. Before L3 operations, produce a `DANGER PLAN — DO NOT EXECUTE YET` and require exact confirmation.
7. Prefer creating a snapshot or export before disruptive changes.
8. Do not use elevated privileges unless necessary and explained.
9. Do not combine discovery and mutation in one command.
10. Do not run blind destructive loops.
11. Do not store secrets or runtime inventory in the repository.
12. Use `--format=json` for machine parsing where available.
13. Avoid parsing human-readable tables when JSON output is available.
14. Do not hide failures in shell pipelines.
15. When uncertain, produce a command plan instead of executing commands.

## Risk levels

### L0 — Read-only

Usually safe to run. Still treat output as sensitive.

Examples:

```bash
incus version
incus remote list
incus project list
incus project show <PROJECT>
incus --project <PROJECT> list <REMOTE>: --format=json
incus --project <PROJECT> info <REMOTE>:<INSTANCE>
incus --project <PROJECT> profile list <REMOTE>:
incus --project <PROJECT> profile show <REMOTE>:<PROFILE>
incus --project <PROJECT> network list <REMOTE>:
incus --project <PROJECT> network show <REMOTE>:<NETWORK>
incus --project <PROJECT> storage list <REMOTE>:
incus --project <PROJECT> storage show <REMOTE>:<POOL>
incus --project <PROJECT> storage volume list <REMOTE>:<POOL>
incus --project <PROJECT> storage volume show <REMOTE>:<POOL> <VOLUME>
incus operation list <REMOTE>:
incus warning list <REMOTE>:
```

Rules:

- Redact sensitive output before saving or sharing.
- Do not write full runtime inventory into the repository.
- Do not infer that read-only output is safe to publish.
- Prefer summaries over full dumps.

### L1 — Additive / usually safe

Requires a short plan before execution.

Examples:

```bash
incus --project <PROJECT> snapshot create <REMOTE>:<INSTANCE> <SNAPSHOT>
incus --project <PROJECT> profile create <REMOTE>:<PROFILE>
incus project create <REMOTE>:<PROJECT>
incus --project <PROJECT> launch <IMAGE> <REMOTE>:<INSTANCE>
incus --project <PROJECT> copy <REMOTE>:<INSTANCE> <REMOTE>:<INSTANCE>
incus --project <PROJECT> export <REMOTE>:<INSTANCE> <BACKUP_PATH>
```

Rules:

- Explain the target object.
- Use unique names or paths.
- Do not overwrite existing files.
- For exports, always check that the destination does not already exist.
- Confirm whether custom storage volumes need separate backup.

### L2 — Disruptive / reversible with preparation

Requires preflight, plan, expected effect, and rollback strategy.

Examples:

```bash
incus --project <PROJECT> stop <REMOTE>:<INSTANCE>
incus --project <PROJECT> start <REMOTE>:<INSTANCE>
incus --project <PROJECT> restart <REMOTE>:<INSTANCE>
incus --project <PROJECT> config set <REMOTE>:<INSTANCE> <KEY> <VALUE>
incus --project <PROJECT> config unset <REMOTE>:<INSTANCE> <KEY>
incus --project <PROJECT> profile edit <REMOTE>:<PROFILE>
incus --project <PROJECT> device add <REMOTE>:<INSTANCE> <DEVICE> disk source=<VALUE> path=<VALUE>
incus --project <PROJECT> device remove <REMOTE>:<INSTANCE> <DEVICE>
incus --project <PROJECT> network set <REMOTE>:<NETWORK> <KEY> <VALUE>
incus --project <PROJECT> network unset <REMOTE>:<NETWORK> <KEY>
incus --project <PROJECT> storage set <REMOTE>:<POOL> <KEY> <VALUE>
incus --project <PROJECT> storage unset <REMOTE>:<POOL> <KEY>
incus --project <PROJECT> snapshot restore <REMOTE>:<INSTANCE> <SNAPSHOT>
incus --project <PROJECT> move <REMOTE>:<INSTANCE> <REMOTE>:<INSTANCE>
incus --project <PROJECT> exec <REMOTE>:<INSTANCE> -- <VALUE>
```

Rules:

- Run read-only preflight commands first.
- Capture current configuration only in ephemeral notes.
- Do not commit raw configuration snapshots.
- Explain service impact.
- Propose rollback.
- Ask for confirmation if service interruption, data mutation, or network impact is possible.
- Treat snapshot restore as data-affecting because current state may be lost.

### L3 — Destructive / irreversible / overwrite

Never execute directly. First respond with a danger plan and require exact confirmation.

Examples:

```bash
incus --project <PROJECT> delete <REMOTE>:<INSTANCE>
incus --project <PROJECT> delete --force <REMOTE>:<INSTANCE>
incus --project <PROJECT> snapshot delete <REMOTE>:<INSTANCE> <SNAPSHOT>
incus --project <PROJECT> rebuild <REMOTE>:<INSTANCE> <IMAGE>
incus --project <PROJECT> storage volume delete <REMOTE>:<POOL> <VOLUME>
incus --project <PROJECT> storage volume snapshot delete <REMOTE>:<POOL> <VOLUME> <SNAPSHOT>
incus project delete <REMOTE>:<PROJECT>
incus --project <PROJECT> profile delete <REMOTE>:<PROFILE>
incus --project <PROJECT> network delete <REMOTE>:<NETWORK>
incus image delete <REMOTE>:<IMAGE>
incus remote remove <REMOTE>
incus admin init
incus query --request DELETE <VALUE>
```

For L3 operations, respond with:

```text
DANGER PLAN — DO NOT EXECUTE YET
```

Then require exact confirmation:

```text
CONFIRM <VALUE> <VALUE> <VALUE>
```

For deletion of an instance, require this exact phrase:

```text
CONFIRM DELETE INSTANCE <INSTANCE>
```

Ambiguous confirmation is not enough. Do not treat responses such as "yes", "go ahead", or "confirmed" as sufficient.

After exact confirmation, execute only the explicitly confirmed operation, only if the user also asks you to proceed in the current conversation and the surrounding harness permits execution. Otherwise, provide the final command plan without executing it.

## Mandatory workflow

### L2 workflow

For L2 operations:

1. State the intent.
2. Identify affected objects.
3. Identify remote and project explicitly.
4. Run read-only preflight commands.
5. Show the proposed command sequence.
6. Explain expected result.
7. Explain possible disruption.
8. Provide rollback plan.
9. Ask for confirmation if service interruption, data mutation, or network impact is possible.

### L3 workflow

For L3 operations:

1. Do not execute in the first response.
2. Produce `DANGER PLAN — DO NOT EXECUTE YET`.
3. Include exact target object.
4. Include remote and project.
5. Include irreversible effects.
6. Check snapshots and backups.
7. Prefer creating a fresh snapshot or export first.
8. Explain rollback limits.
9. Require an exact confirmation phrase.
10. If confirmation is ambiguous, do not proceed.

## Command construction rules

Prefer explicit target forms:

```bash
incus --project <PROJECT> list <REMOTE>: --format=json
incus --project <PROJECT> info <REMOTE>:<INSTANCE>
incus --project <PROJECT> config set <REMOTE>:<INSTANCE> <KEY> <VALUE>
```

When a command supports both remote and project targeting, state both in the response. When a command is global or remote-scoped rather than project-scoped, still state the project context separately if project impact matters.

Use shell safety for generated scripts or multi-command snippets:

```bash
set -euo pipefail
```

Rules:

- Quote variables.
- Use `--format=json` for machine parsing where supported.
- Avoid parsing human-readable tables if JSON output is available.
- Do not use elevated privileges unless necessary and explained.
- Do not combine discovery and mutation in a single one-liner.
- Do not run pipelines that hide failures.
- Never generate blind loops over all instances for destructive commands.
- Never use `--force` unless part of a danger plan and exact confirmation has been provided.
- Never use overwrite semantics unless explicitly confirmed.
- Do not create scripts that execute destructive operations.

## Standard response formats

For L0, L1, and L2:

```text
Summary:
Risk level:
Affected objects:
Preflight commands:
Proposed commands:
Expected result:
Rollback plan:
Confirmation required:
```

For L3:

```text
DANGER PLAN — DO NOT EXECUTE YET

Target:
Impact:
Preflight:
Backup/snapshot status:
Safer alternatives:
Proposed commands:
Rollback limits:
Required exact confirmation:
```

## Redaction rules

Redact before sharing or saving.

| Data type | Required handling |
|---|---|
| Network addresses | Remove the concrete value; do not preserve it in repository files. |
| Hardware addresses | Remove the concrete value; do not preserve it in repository files. |
| Hostnames and domains | Remove the concrete value; use `<REMOTE>` only when referring to an Incus remote placeholder. |
| Public endpoint URLs | Remove the concrete value; describe only the intended remote role. |
| Certificate fingerprints | Remove the concrete value; never store it in repository files. |
| Trust tokens and API tokens | Never print or store; ask the user to provide them interactively only when needed. |
| SSH keys or SSH configuration secrets | Never print, store, summarize, or copy. |
| User-specific local paths | Replace with `<BACKUP_PATH>` only when discussing backup destination behavior. |
| Block device identifiers and disk serials | Remove the concrete value; ask the user to provide the device at execution time. |
| PCI identifiers | Remove the concrete value; ask the user to provide the device at execution time. |
| Private topology | Summarize generically; do not preserve concrete details. |
| Real Incus object names | Replace with `<INSTANCE>`, `<PROJECT>`, `<PROFILE>`, `<NETWORK>`, `<POOL>`, `<VOLUME>`, `<SNAPSHOT>`, `<IMAGE>`, or `<DEVICE>`. |
| Configuration keys and values | Use `<KEY>` and `<VALUE>` unless the value is non-sensitive and explicitly provided for the current task. |

## Standard procedures

### A. Inventory

Use read-only commands:

```bash
incus remote list
incus project list
incus --project <PROJECT> list <REMOTE>: --format=json
incus --project <PROJECT> profile list <REMOTE>:
incus --project <PROJECT> network list <REMOTE>:
incus --project <PROJECT> storage list <REMOTE>:
incus warning list <REMOTE>:
incus operation list <REMOTE>:
```

Rules:

- Do not save raw output unless redacted.
- Do not commit inventory files.
- Prefer summary over full dump.
- Explicitly identify `<REMOTE>` and `<PROJECT>` before planning mutations.

### B. Health check

Suggested read-only checks:

```bash
incus version
incus info <REMOTE>:
incus warning list <REMOTE>:
incus operation list <REMOTE>:
incus --project <PROJECT> list <REMOTE>: --format=json
incus --project <PROJECT> storage list <REMOTE>:
incus --project <PROJECT> network list <REMOTE>:
```

Optional host-level checks may include service status and logs, but do not store machine-specific paths or raw logs in the repository. Redact before sharing.

### C. Instance lifecycle

Read status:

```bash
incus --project <PROJECT> list <REMOTE>: --format=json
incus --project <PROJECT> info <REMOTE>:<INSTANCE>
```

Launch:

```bash
incus --project <PROJECT> launch <IMAGE> <REMOTE>:<INSTANCE>
```

Start, stop, restart:

```bash
incus --project <PROJECT> start <REMOTE>:<INSTANCE>
incus --project <PROJECT> stop <REMOTE>:<INSTANCE>
incus --project <PROJECT> restart <REMOTE>:<INSTANCE>
```

Execute command inside an instance:

```bash
incus --project <PROJECT> exec <REMOTE>:<INSTANCE> -- <VALUE>
```

File transfer:

```bash
incus --project <PROJECT> file pull <REMOTE>:<INSTANCE>/<VALUE> <BACKUP_PATH>
incus --project <PROJECT> file push <BACKUP_PATH> <REMOTE>:<INSTANCE>/<VALUE>
```

Copy or move:

```bash
incus --project <PROJECT> copy <REMOTE>:<INSTANCE> <REMOTE>:<INSTANCE>
incus --project <PROJECT> move <REMOTE>:<INSTANCE> <REMOTE>:<INSTANCE>
```

Rules:

- Deleting an instance is L3 and must produce a danger plan only.
- Instance deletion also removes that instance's snapshots.
- Copy and move operations require explicit source and destination, even if both are represented by `<INSTANCE>` in examples.

### D. Backup

Before risky changes, create a timestamped snapshot:

```bash
set -euo pipefail
SNAPSHOT="prechange-$(date +%Y%m%d-%H%M%S)"
incus --project <PROJECT> snapshot create <REMOTE>:<INSTANCE> "$SNAPSHOT"
incus --project <PROJECT> info <REMOTE>:<INSTANCE>
```

For a stronger backup, export to a unique destination:

```bash
set -euo pipefail
BACKUP_FILE="<BACKUP_PATH>/<INSTANCE>-$(date +%Y%m%d-%H%M%S).tar.gz"
test ! -e "$BACKUP_FILE"
incus --project <PROJECT> export <REMOTE>:<INSTANCE> "$BACKUP_FILE"
```

Rules:

- Do not overwrite existing backup files.
- Verify the destination before export.
- Custom storage volumes may require separate backup.
- Do not assume instance export includes all external or custom volumes.
- For critical changes, prefer an external export over snapshot only.

### E. Restore

Snapshot restore can discard current state.

Before restore:

```bash
incus --project <PROJECT> info <REMOTE>:<INSTANCE>
incus --project <PROJECT> snapshot list <REMOTE>:<INSTANCE>
```

Restore command:

```bash
incus --project <PROJECT> snapshot restore <REMOTE>:<INSTANCE> <SNAPSHOT>
```

Rules:

- Treat restore as L2 or higher depending on impact.
- Explain that current state may be lost.
- Prefer copying a snapshot to a separate instance when validating old state.
- Require confirmation before restore.

### F. Storage

Discovery:

```bash
incus --project <PROJECT> storage list <REMOTE>:
incus --project <PROJECT> storage show <REMOTE>:<POOL>
incus --project <PROJECT> storage volume list <REMOTE>:<POOL>
incus --project <PROJECT> storage volume show <REMOTE>:<POOL> <VOLUME>
```

Rules:

- Deleting storage volumes is L3.
- Deleting volume snapshots is L3.
- Changing storage pool settings is L2.
- Export custom volumes separately when needed.
- Never run host-level destructive disk commands directly.
- Do not assume which storage backend is in use.

### G. Network

Discovery:

```bash
incus --project <PROJECT> network list <REMOTE>:
incus --project <PROJECT> network show <REMOTE>:<NETWORK>
```

Rules:

- Network changes can disconnect instances or the management session.
- Never assume which bridge, physical interface, overlay network, route, or firewall setting is safe.
- Require a rollback path before changing address assignment, bridge, firewall-related, uplink, or physical interface settings.
- Do not expose services publicly without security review.
- Do not modify remote host networking if loss of access is likely and no rollback path exists.

### H. Profiles

Profiles can affect multiple instances.

Before editing:

```bash
incus --project <PROJECT> profile show <REMOTE>:<PROFILE>
incus --project <PROJECT> list <REMOTE>: --format=json
```

Prefer copying a profile before risky edits:

```bash
incus --project <PROJECT> profile copy <REMOTE>:<PROFILE> <PROFILE>-backup
```

Rules:

- Profile edit is L2.
- Profile delete is L3.
- Explain blast radius before changing a profile.
- Do not assume a profile is unused without checking.
- Do not commit raw profile configuration.

### I. Projects

Discovery:

```bash
incus project list
incus project show <REMOTE>:<PROJECT>
incus --project <PROJECT> list <REMOTE>: --format=json
```

Rules:

- Use explicit `--project <PROJECT>` where supported.
- Do not assume the default project.
- Project delete is L3.
- Check whether a project contains instances, profiles, networks, images, or storage resources before planning deletion.

### J. Device passthrough

Device passthrough can weaken isolation or bind an instance to host-specific hardware.

Generic device categories:

- `disk`
- `unix-char`
- `unix-block`
- `gpu`
- `usb`
- `nic`

Before changing devices:

```bash
incus --project <PROJECT> config show <REMOTE>:<INSTANCE>
incus --project <PROJECT> device list <REMOTE>:<INSTANCE>
```

Example device-add pattern using placeholders only:

```bash
incus --project <PROJECT> device add <REMOTE>:<INSTANCE> <DEVICE> disk source=<VALUE> path=<VALUE>
```

Rules:

- Never hardcode host paths or hardware identifiers.
- Use placeholders and require user-provided target device details at execution time.
- Warn that passthrough may reduce isolation.
- Warn that passthrough may make migration or backup less portable.
- Removing devices is L2 unless it destroys data, then L3.

### K. Remote and API

Discovery:

```bash
incus remote list
```

Rules:

- Never print or store trust tokens.
- Never print or store client keys.
- Never print or store server keys.
- Never print or store private certificates.
- Do not alter remotes without confirmation.
- Do not remove remotes without a danger plan.
- Do not expose the Incus API publicly without separate security review.

## Safe examples

### Read-only inventory

```bash
incus remote list
incus project list
incus --project <PROJECT> list <REMOTE>: --format=json
incus --project <PROJECT> profile list <REMOTE>:
incus --project <PROJECT> network list <REMOTE>:
incus --project <PROJECT> storage list <REMOTE>:
incus warning list <REMOTE>:
incus operation list <REMOTE>:
```

### Create pre-change snapshot

```bash
set -euo pipefail
SNAPSHOT="prechange-$(date +%Y%m%d-%H%M%S)"
incus --project <PROJECT> snapshot create <REMOTE>:<INSTANCE> "$SNAPSHOT"
incus --project <PROJECT> info <REMOTE>:<INSTANCE>
```

### Export instance backup without overwriting

```bash
set -euo pipefail
BACKUP_FILE="<BACKUP_PATH>/<INSTANCE>-$(date +%Y%m%d-%H%M%S).tar.gz"
test ! -e "$BACKUP_FILE"
incus --project <PROJECT> export <REMOTE>:<INSTANCE> "$BACKUP_FILE"
```

### Safe delete plan example

```text
DANGER PLAN — DO NOT EXECUTE YET

Target:
- Remote: <REMOTE>
- Project: <PROJECT>
- Instance: <INSTANCE>

Impact:
- Permanently deletes the instance.
- Permanently deletes all snapshots of that instance.
- Existing data may not be recoverable unless an external backup exists.

Preflight:
- incus --project <PROJECT> info <REMOTE>:<INSTANCE>
- incus --project <PROJECT> config show <REMOTE>:<INSTANCE>
- incus --project <PROJECT> snapshot list <REMOTE>:<INSTANCE>
- verify external backup or export exists

Backup/snapshot status:
- Unknown until preflight is reviewed.
- Prefer creating a final snapshot and external export first.

Safer alternatives:
- Stop the instance instead of deleting it.
- Create a final snapshot.
- Export the instance to a unique backup destination.
- Enable deletion protection instead of deleting if the goal is to prevent accidental use.

Proposed commands:
- incus --project <PROJECT> snapshot create <REMOTE>:<INSTANCE> <SNAPSHOT>
- incus --project <PROJECT> export <REMOTE>:<INSTANCE> <BACKUP_PATH>
- incus --project <PROJECT> delete <REMOTE>:<INSTANCE>

Rollback limits:
- Deletion cannot be rolled back without a valid external backup or export.
- Snapshots are deleted with the instance.
- Custom storage volumes may require separate backups.

Required exact confirmation:
CONFIRM DELETE INSTANCE <INSTANCE>
```

## Acceptance checklist for using this skill

Before executing or proposing commands, confirm that:

- No real machine-specific information is being written to repository files.
- Examples and plans use approved placeholders.
- Risk level is identified as L0, L1, L2, or L3.
- L3 operations are not executed directly.
- L3 operations require exact confirmation.
- Backup, snapshot, or export guidance is included for risky changes.
- Instance deletion warns that snapshots are also removed.
- Custom storage volumes are considered separately for backup.
- Redaction rules are applied before sharing output.
- No destructive helper scripts are created.
- No real inventory files are added.
