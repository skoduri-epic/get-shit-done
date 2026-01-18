# CLAUDE.md - GSD Fork Maintenance Guide

## Overview

This is a fork of [glittercowboy/get-shit-done](https://github.com/glittercowboy/get-shit-done) with **multi-repo workspace support**.

**Fork:** https://github.com/skoduri-epic/get-shit-done
**Base Version:** 1.6.4
**Fork Version:** 1.6.4-multirepo

## Multi-Repo Modifications

When `multiRepo: true` is set in `.planning/config.json`:

- `/gsd:new-project` will NOT run `git init`
- All commits to `.planning/` files are skipped
- Planning files are created locally but never committed
- Code commits in sub-repos (backend/, frontend/) still work normally
- The `.planning/` folder acts as cross-repo coordination

### Files Modified for Multi-Repo Support

**Commands (`commands/gsd/`):**
- `new-project.md` - Multi-repo detection + conditional git init
- `execute-phase.md` - Conditional commits for orchestrator/phase completion
- `new-milestone.md` - Conditional commits for PROJECT/REQUIREMENTS/ROADMAP
- `remove-phase.md` - Conditional commit for phase removal
- `pause-work.md` - Conditional commit for handoff files
- `plan-milestone-gaps.md` - Conditional commit for roadmap updates
- `add-todo.md` - Conditional commit for todo creation
- `check-todos.md` - Conditional commit for todo status changes

**Workflows (`get-shit-done/workflows/`):**
- `complete-milestone.md` - Conditional milestone archive commit
- `discuss-phase.md` - Conditional CONTEXT.md commit
- `execute-phase.md` - Conditional metadata commits + codebase map updates
- `map-codebase.md` - Conditional codebase map commit

**Templates:**
- `get-shit-done/templates/config.json` - Added `multiRepo: false` default

**References:**
- `get-shit-done/references/git-integration.md` - Documented multi-repo mode

### Key Pattern

All multi-repo checks use macOS-compatible regex:
```bash
if [ -f .planning/config.json ] && grep -q '"multiRepo":[[:space:]]*true' .planning/config.json; then
    echo "Multi-repo mode: skipping git commit"
else
    # normal git operations
fi
```

Note: Uses `[[:space:]]*` instead of `\s*` for BSD grep (macOS) compatibility.

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
# - Keep the conditional git checks when resolving conflicts
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

- Upstream: `1.6.4`
- Fork: `1.6.4-multirepo`

When merging upstream `1.7.0`, update to `1.7.0-multirepo`.

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

The multi-repo modifications touch specific sections (git commit blocks). When resolving:
1. Keep the `if [ -f .planning/config.json ] && grep -q '"multiRepo"...` pattern
2. Update the git commands inside the `else` block to match upstream
3. Ensure the `fi` closing bracket is present

### Verify Installation

```bash
# Check version
cat ~/.claude/get-shit-done/VERSION

# Check commands installed
ls ~/.claude/commands/gsd/ | wc -l

# Should show 24 commands
```

## Git Remotes Setup

```bash
# Your fork (origin)
git remote -v
# origin    https://github.com/skoduri-epic/get-shit-done.git

# Add upstream if not already added
git remote add upstream https://github.com/glittercowboy/get-shit-done.git
```
