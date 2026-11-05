<!-- markdownlint-disable -->

# Hardening Report: quarto-dev--quarto-actions--render/v2.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **quarto-dev--quarto-actions--render/v2.2.0** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a), direct expression interpolation, in the composite step 'Render'. The run: script places `${{ inputs.to }}` and `${{ inputs.path }}` straight into shell code. The offending lines are: `if [ "${{ inputs.to }}" == "" ]; then`, `quarto render ${{ inputs.path }}` and `quarto render ${{ inputs.path }} --to ${{ inputs.to }}`. A caller who sets these inputs can inject shell commands. Fix: pass the inputs through env: (for example INPUT_TO and INPUT_PATH) and reference them double-quoted, e.g. `quarto render "$INPUT_PATH" --to "$INPUT_TO"`.

Locations:

- `action.yml:34`
- `action.yml:35`
- `action.yml:37`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.to }}" appears directly in run: block of step "Render"; move to env: map

Locations:

- `action.yml:34`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.path }}" appears directly in run: block of step "Render"; move to env: map

Locations:

- `action.yml:35`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.path }}" appears directly in run: block of step "Render"; move to env: map

Locations:

- `action.yml:37`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.to }}" appears directly in run: block of step "Render"; move to env: map

Locations:

- `action.yml:37`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection

**Notes:**

Moved inputs.to and inputs.path into env (INPUT_TO, INPUT_PATH) in the Render step; referenced double-quoted, with path using ${INPUT_PATH:+"$INPUT_PATH"} so an empty path is omitted as upstream's unquoted expansion did.

