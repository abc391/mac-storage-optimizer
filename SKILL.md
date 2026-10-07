---
name: mac-storage-optimizer
description: Inspect macOS disk usage and propose storage cleanup, then remove only specifically approved, verified expendable items. Use for Mac storage audits or freeing disk space; auditing alone never authorizes deletion.
---

# Mac Storage Optimizer

Follow this workflow: scan → classify → assess risk → show a cleanup plan → obtain confirmation → clean → verify. Respond in the user's language. If the request is audit-only, finish after the plan without changing files.

## Protection boundaries

- Preserve Desktop, Documents, Downloads' real files, photos/photo libraries, videos, music, user-created projects, source code and Git repositories (including untracked work), databases, virtual machines, Docker volumes, chat/session history, mail, browser profiles, passwords, keychains, SSH/GPG/encryption keys, backups, and cloud-sync content by default. Size, age, filename, or an apparently cache-like extension never makes an item disposable.
- Report protected items as storage consumers, but exclude them from routine cleanup. Removing a particular protected item requires a separate review of that exact item, recovery/backup consequences, and explicit authorization; general approval to clean does not cover it.
- Treat `Application Support`, `Containers`, `Group Containers`, browser profile directories, and developer repositories as mixed data. Inspect relevant children without reading private content unnecessarily. Never delete these parents wholesale. A verified cache child may be considered separately; preserve the surrounding profile, project, and application state.
- Browser cookies, history, logins, extensions, IndexedDB, Local Storage, service-worker storage, offline downloads, and chat-app databases are not ordinary disposable caches. Default them to protected or needs-review, even if an earlier cleanup example removed similar data.
- Preserve anything whose purpose or regeneration path is uncertain. Do not modify `/System`, broadly clean `/Library` or system temporary directories, remove APFS/Time Machine snapshots, empty Trash, or run blanket Docker prune commands as part of routine cleanup.
- Do not automatically use `sudo`, change permissions, grant Full Disk Access, disable security controls, or retry a denied operation with broader access. Report inaccessible areas and continue independent permitted inspection. Never claim a complete audit when areas were inaccessible.
- Never use a broad `rm -rf`, wildcard sweep, or equivalent recursive deletion against a parent such as the home directory, Library, Caches, Downloads, a profile, repository, or `/private/var/folders`. Do not follow symlinks into other targets. Any mutation must have a verified, narrowly bounded target from the approved plan.

## 1. Scan without changing data

Confirm macOS and identify the relevant volume, normally the user's Data volume. Record capacity, used space, available space, timestamp, measurement tool, and units using a read-only measurement such as `df -k "$HOME"`. Use the same volume, tool, and units afterward. Label decimal GB and binary GiB accurately.

Start with directory-size summaries; inspect deeper only where useful. Use read-only tools such as `du`, bounded file listings, or a package manager's informational commands. Avoid launching installers, cleanup tools, or commands that mutate caches during the audit. Do not download cloud placeholders to measure them, traverse unrelated volumes, or dump secrets, private document names, or database contents into reports.

When present, consider `~/Library/Caches`, `~/.cache`, app/container cache children, package-manager caches, Xcode DerivedData, inactive update leftovers, and Downloads metadata. Inspect build output in a repository only to establish whether it is reproducible and contains tracked or user-created work. Do not assume `.next`, `dist`, `build`, `.venv`, or `node_modules` is disposable merely because of its name.

Record access failures. Avoid double-counting nested candidates. Distinguish disk allocation from apparent size; APFS clones, snapshots, compressed/sparse files, and cloud placeholders can make summed file sizes differ from recoverable space.

## 2. Classify and assess risk

Assign each relevant candidate one category and explain the evidence:

| Category | Criteria | Action |
|---|---|---|
| GREEN — verified expendable | A specific cache/output has an established regeneration path, no unique user data, no protected contents, and no active writer | May recommend; confirmation still required |
| YELLOW — needs review | Downloads, installers, logs, offline data, SDKs, models, environments, abandoned partial downloads, or mixed/uncertain contents | Preserve until exact purpose, usefulness, and recovery cost are reviewed |
| RED — protected | Personal data, profiles, histories, credentials, databases, repositories, backups, VMs, or synced content | Exclude from routine cleanup |

An active download, updater, browser, app, or build can invalidate a cleanup candidate. Ask the user to pause/close it when necessary; do not terminate apps or processes without authorization. Cache deletion may cause re-downloads, slower launches, or loss of offline availability; make these costs visible.

## 3. Show the concrete plan and obtain confirmation

Before any cleanup, present a table with an item ID, exact target (redact personal names in public reports), measured size, purpose, category/risk, evidence of disposability, proposed method, and recovery/consequences. Separate estimated GREEN space, optional YELLOW space, and preserved large items; avoid overlapping totals. State estimation limits.

Ask which item IDs the user approves. Explain whether each removal is reversible or permanent. General requests such as “free space,” “clean everything,” or “be aggressive” are not confirmation of unseen targets. If the user already explicitly approved the same concrete targets and method in this session, retain that approval; changes in scope, method, or risk require renewed confirmation. No answer is not approval.

Moving files to Trash on the same volume ordinarily does not release disk space; do not report it as recovered space or empty Trash afterward without separate authorization. Direct deletion may be necessary for real recovery, but only after its permanence is disclosed and exact targets are confirmed.

## 4. Clean only approved targets

Immediately re-check each approved target's resolved location, contents/type, size, and activity. Reject symlinks, unexpected paths, changed contents, or any target containing protected/uncertain data; return it for review. Keep operations within approved bounds, quote paths, handle option-like names safely, and never interpolate untrusted filenames into executable shell text.

Prefer an application's or package manager's documented cache-specific cleanup when limited to approved items. If it would touch additional items, preview that scope and obtain approval first. Otherwise use a narrowly scoped removal of the verified approved item. Preserve unrelated files, lockfiles, environments, and application/profile state.

Keep a per-item outcome: cleaned, skipped, or failed. Stop that item on access denial, unexpected data, or unsafe tool scope; do not broaden the command or repeat a destructive action without checking the result. Continue other independently approved safe items. Do not install cleanup software or add scheduled cleanup tasks unless requested.

## 5. Verify and report measured results

Re-measure available space on the original volume with the original tool and units. Compute after minus before; do not substitute the sum of candidate sizes. Check approved targets' outcomes with read-only inspection. Do not claim that all apps work normally without checking them.

Report before/after available space and observed change, cleaned items, skipped/failed items, protected items, inspection limits, and remaining review candidates. If space decreased, stayed unchanged, or could not be measured, say so. Background writes, open deleted files, snapshots, cache regeneration, and Trash may explain differences; distinguish hypotheses from verified causes. Never automatically reboot, kill processes, or remove snapshots to improve the number.

Use real measurements for the final report. The README's 51→70 case is historical context, never an expected or fabricated result.
