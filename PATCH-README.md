# PATCH: `--path/-p` option for nested skill installation

Fork of [vercel-labs/skills](https://github.com/vercel-labs/skills) — adds a `--path` flag to control the install subdirectory instead of always flattening skills to the top-level skills directory.

## Problem

`npx skills add` always installs skills as flat subdirectories:

```
~/.claude/skills/skill-a/
~/.claude/skills/skill-b/
~/.claude/skills/skill-c/
```

No way to group skills from the same source repo into a subdirectory (e.g., all `obra/superpowers` skills under `superpowers/`).

## Solution

Added `--path/-p <path>` option that installs skills into a nested directory:

```
~/.claude/skills/<path>/skill-a/
~/.claude/skills/<path>/skill-b/
```

## Usage

```bash
# Install all superpowers skills under ~/.claude/skills/superpowers/
skills add obra/superpowers -g -a claude-code --skill '*' --path superpowers -y

# Short form
skills add obra/superpowers -g -a claude-code -s '*' -p superpowers -y

# Multi-level path
skills add <source> -g -p my-tools/git -y

# Omitting --path keeps the original flat behavior
skills add <source> -g -a claude-code --skill '*' -y
```

## Changed files

| File | Change |
|------|--------|
| `src/installer.ts` | Added `sanitizePath()` — sanitizes each path segment individually. Modified `installSkillForAgent`, `installWellKnownSkillForAgent`, `installBlobSkillForAgent`, and `getCanonicalPath` to accept `path` option and construct nested directory paths. |
| `src/add.ts` | Added `path` field to `AddOptions` interface. Added `-p`/`--path` parsing in `parseAddOptions`. Passed `path: options.path` to all install function calls. |

## Install (local fork)

```bash
git clone https://github.com/EmonCodingAILLM/skills.git ~/SkillsProjects/skills
cd ~/SkillsProjects/skills
pnpm install && pnpm build
npm uninstall -g skills   # remove official version
npm link                  # register fork as global `skills` command
```

## Sync upstream

```bash
cd ~/SkillsProjects/skills
git fetch upstream
git merge upstream/main
pnpm build
git push origin main
```

## Base commit

`c5ad3a8` — v1.5.7 (upstream)
