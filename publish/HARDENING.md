<!-- markdownlint-disable -->

# Hardening Report: quarto-dev--quarto-actions--publish/v2.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **quarto-dev--quarto-actions--publish/v2.2.0** was hardened automatically. 8 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rules (a) and (b) are both broken in the 'Publish' composite step. (a) inputs.* expressions are placed directly inside the run: script: `git config --global user.email "${{ inputs.GITHUB_EMAIL }}"` (line 51), `git config --global user.name "${{ inputs.GITHUB_USERNAME }}"` (line 52), `TARGET="${{ inputs.to }}"` (line 60), `TARGET="${{ inputs.target }}"` (line 62), `if [ "${{ inputs.render }}" != "true" ]` (line 64), and `quarto publish $TARGET ${{ inputs.path }} ...` (lines 65 and 67). (b) $TARGET holds data from inputs.to/inputs.target and is expanded without quotes in `quarto publish $TARGET ...` (lines 65 and 67). Fix: move these inputs into env: variables and double-quote every expansion. Because to/target are optional positional arguments, use the guarded form ${TARGET:+"$TARGET"} for them.

Locations:

- `action.yml:51`
- `action.yml:52`
- `action.yml:60`
- `action.yml:62`
- `action.yml:64`
- `action.yml:65`
- `action.yml:67`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.GITHUB_EMAIL }}" appears directly in run: block of step "Publish"; move to env: map

Locations:

- `action.yml:51`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.GITHUB_USERNAME }}" appears directly in run: block of step "Publish"; move to env: map

Locations:

- `action.yml:52`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.to }}" appears directly in run: block of step "Publish"; move to env: map

Locations:

- `action.yml:60`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.target }}" appears directly in run: block of step "Publish"; move to env: map

Locations:

- `action.yml:62`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.render }}" appears directly in run: block of step "Publish"; move to env: map

Locations:

- `action.yml:64`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.path }}" appears directly in run: block of step "Publish"; move to env: map

Locations:

- `action.yml:65`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.path }}" appears directly in run: block of step "Publish"; move to env: map

Locations:

- `action.yml:67`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection

**Notes:**

Moved inputs.GITHUB_EMAIL, GITHUB_USERNAME, to, target, render, path into the Publish step env: block; script references them as double-quoted env vars. Optional positional args TARGET and INPUT_PATH use ${VAR:+"$VAR"} so empty values drop out as upstream's unquoted expansion did.

### Iteration 2

**Fixes applied:** script-injection, static-inline-injection

**Notes:**

action.yml, Publish step:
- Removed the leftover unhardened lines. They put inputs.target, inputs.render and inputs.path straight into the shell script and expanded $TARGET without quotes.
- Only the env-var block is left. It calls `quarto publish ${TARGET:+"$TARGET"} ${INPUT_PATH:+"$INPUT_PATH"}`, so empty values drop out instead of becoming empty arguments.
- Repaired the cut-off `if git config get includeIf...` line, which copies git credentials into worktrees for actions/checkout@v6 (upstream issue #133). Upstream's original text for this block isn't anywhere in the repo, so I rewrote it from the comment and the surviving start of the line. My version looks up checkout's includeIf.gitdir:<abs git-dir>.path. If it's set, it adds the same include path for `<git-dir>/worktrees/` in the repo's own git config. Before relying on this, compare it with upstream's v2.2.0 action.yml. Two things can differ: the exact git command, and whether upstream wrote to global or repo-level config.
- Checking which version of the file was saved was confusing. Two reads of the end of the file still showed the removed lines, and a second removal attempt also reported success. The final read of the whole file shows it clean.

