# CLAUDE.md - GSD Fork Maintenance Guide

## Overview

This is a fork of [glittercowboy/get-shit-done](https://github.com/glittercowboy/get-shit-done) with **multi-repo workspace support**.

**Fork:** https://github.com/skoduri-epic/get-shit-done
**Base Version:** 1.9.4
**Fork Version:** 1.9.4-multirepo

## Multi-Repo Support

When working with a multi-repo workspace (separate git repos for backend, frontend, etc.), configure `sub_repos` in `.planning/config.json`:

```json
{
  "planning": {
    "commit_docs": false,
    "sub_repos": ["backend", "frontend", "shared"]
  }
}
```

### How It Works

1. **During `/gsd:new-project`:**
   - GSD auto-detects directories with their own `.git` folders
   - Prompts you to select which directories should receive code commits
   - Automatically sets `commit_docs: false` (planning stays local)

2. **During code execution:**
   - Files are grouped by their sub-repo prefix
   - `backend/src/api/users.ts` → commits to `backend/` repo
   - `frontend/src/components/Header.tsx` → commits to `frontend/` repo
   - Each sub-repo gets atomic commits with the same message format

3. **Planning docs:**
   - `.planning/` folder stays local (not committed)
   - Acts as cross-repo coordination

### Config Example

```json
{
  "mode": "interactive",
  "planning": {
    "commit_docs": false,
    "sub_repos": ["backend", "frontend", "mobile", "shared"]
  },
  "workflow": {
    "research": true,
    "plan_check": true,
    "verifier": true
  }
}
```

### Files Modified for Multi-Repo Support

**Commands (`commands/gsd/`):**
- `new-project.md` - Sub-repo detection + configuration prompt

**Workflows (`get-shit-done/workflows/`):**
- `execute-plan.md` - Added `<sub_repos_commit_flow>` section

**Agents (`agents/`):**
- `gsd-executor.md` - Added sub_repos handling in task_commit_protocol

**Templates:**
- `get-shit-done/templates/config.json` - Added `sub_repos: []` in planning section

**References:**
- `get-shit-done/references/git-integration.md` - Documented sub_repos mode

## Installation

Install from this fork using npx:

```bash
npx github:skoduri-epic/get-shit-done --global
```

This installs to `~/.claude/` with version tracking.

## Update Workflow

### When Upstream Releases New Versions

```bash
# 1. Navigate to your local fork
cd ~/Documents/Projects/get-shit-done

# 2. Fetch upstream changes
git fetch upstream

# 3. Merge upstream into your fork
git merge upstream/main

# 4. Resolve any conflicts
# - Multi-repo changes are in specific files listed above
# - Keep the sub_repos logic when resolving conflicts
# - Update version in package.json: "1.X.Y-multirepo"

# 5. Push to your fork
git push origin main

# 6. Reinstall from your updated fork
npx github:skoduri-epic/get-shit-done --global

# 7. Restart Claude Code to load new commands
```

### Checking for Upstream Updates

```bash
# Compare your fork with upstream
cd ~/Documents/Projects/get-shit-done
git fetch upstream
git log HEAD..upstream/main --oneline
```

Or check the upstream changelog:
https://github.com/glittercowboy/get-shit-done/blob/main/CHANGELOG.md

### Version Naming Convention

- Upstream: `1.9.1`
- Fork: `1.9.1-multirepo`

When merging upstream `1.10.0`, update to `1.10.0-multirepo`.

## Troubleshooting

### npx Uses Old Version

Clear npm cache and reinstall:
```bash
npm cache clean --force
npx github:skoduri-epic/get-shit-done@latest --global
```

### Commands Not Loading After Install

Restart Claude Code to reload commands from `~/.claude/commands/gsd/`.

### Merge Conflicts During Update

The multi-repo modifications are in the `<sub_repos_commit_flow>` section of execute-plan.md and similar places. When resolving:
1. Keep the sub_repos parsing and commit routing logic
2. Update any changed upstream patterns around it
3. Ensure the config.json template includes `sub_repos: []`

### Verify Installation

```bash
# Check version
cat ~/.claude/get-shit-done/VERSION

# Check commands installed
ls ~/.claude/commands/gsd/ | wc -l
```

## Git Remotes Setup

```bash
# Your fork (origin)
git remote -v
# origin    https://github.com/skoduri-epic/get-shit-done.git

# Add upstream if not already added
git remote add upstream https://github.com/glittercowboy/get-shit-done.git
```
