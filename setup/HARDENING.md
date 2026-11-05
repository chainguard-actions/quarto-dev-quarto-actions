<!-- markdownlint-disable -->

# Hardening Report: quarto-dev--quarto-actions--setup/v2.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **quarto-dev--quarto-actions--setup/v2.2.0** was hardened automatically. 14 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Step 'Download Quarto' breaks rule (a): expressions are pasted straight into the run script. Examples: `if [ ${{ runner.os }} != "Windows" ]; then`, `if [ "${{inputs.version}}" == "release" ]`, `wget https://github.com/quarto-dev/quarto-cli/releases/download/v${{inputs.version}}/quarto-${{inputs.version}}-${{env.BUNDLE_EXT}}` and `echo "installer=$(ls quarto*${{ env.BUNDLE_EXT }})"`. It also breaks rule (b): `$version` is used unquoted in the wget URLs.

Locations:

- `action.yml:51`
- `action.yml:53`
- `action.yml:65`
- `action.yml:68`

### script-injection (severity: high)

Step 'Install Quarto' breaks rule (a): expressions are pasted straight into the run script. Examples: `[ ${{ runner.os }} != "Windows" ] && installer=${{ steps.download-quarto.outputs.installer }}`, `if [ "${{inputs.version}}" == "release" ]` and `powershell -File $GITHUB_ACTION_PATH/install-quarto-windows.ps1 ${{ inputs.version }}`. It also breaks rule (b): `case $RUNNER_OS in`, `./$installer`, `rm $installer` and `$GITHUB_ACTION_PATH` are expanded without quotes.

Locations:

- `action.yml:79`
- `action.yml:89`
- `action.yml:93`
- `action.yml:101`

### script-injection (severity: high)

Step 'Choose which binary to use' breaks rule (b): the env vars it inherits are expanded without quotes, in `case $RUNNER_OS in` and `case $RUNNER_ARCH in`. This is a composite action, so the calling workflow can control these variables.

Locations:

- `action.yml:22`

### script-injection (severity: high)

Step 'Install TinyTeX' breaks rule (b): the env vars it inherits are expanded without quotes, in `case $RUNNER_OS in` and in `$(dirname $(find $APPDATA/TinyTeX -name tlmgr.bat))`. The command substitutions are also unquoted.

Locations:

- `action.yml:111`
- `action.yml:119`

### github-env-injection (severity: high)

Step 'Download Quarto' writes untrusted values to $GITHUB_OUTPUT without first stripping newlines with `printf '%s' ... | tr -d '\n\r'`. The writes are `echo "version=${{inputs.version}}" >> $GITHUB_OUTPUT` and `echo "installer=$(ls quarto*${{ env.BUNDLE_EXT }})" >> $GITHUB_OUTPUT`. The `version` value fetched from a remote server is also written unsanitized, in `echo "version=${version}" >> $GITHUB_OUTPUT`.

Locations:

- `action.yml:66`
- `action.yml:68`
- `action.yml:57`
- `action.yml:62`

### github-env-injection (severity: high)

Step 'Install TinyTeX' writes paths built from the inherited env vars $HOME and $APPDATA to $GITHUB_PATH without sanitizing them first. The writes are `echo "$HOME/bin" >> $GITHUB_PATH` and `echo "$(dirname $(find $APPDATA/TinyTeX -name tlmgr.bat))" >> $GITHUB_PATH`. In a composite action, the calling workflow controls inherited env vars.

Locations:

- `action.yml:113`
- `action.yml:116`
- `action.yml:119`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.version}}" appears directly in run: block of step "Download Quarto"; move to env: map

Locations:

- `action.yml:53`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.version}}" appears directly in run: block of step "Download Quarto"; move to env: map

Locations:

- `action.yml:58`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.version}}" appears directly in run: block of step "Download Quarto"; move to env: map

Locations:

- `action.yml:58`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.version}}" appears directly in run: block of step "Download Quarto"; move to env: map

Locations:

- `action.yml:65`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.version}}" appears directly in run: block of step "Download Quarto"; move to env: map

Locations:

- `action.yml:65`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.version}}" appears directly in run: block of step "Download Quarto"; move to env: map

Locations:

- `action.yml:66`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.version}}" appears directly in run: block of step "Install Quarto"; move to env: map

Locations:

- `action.yml:89`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.version }}" appears directly in run: block of step "Install Quarto"; move to env: map

Locations:

- `action.yml:93`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, github-env-injection, static-inline-injection

**Notes:**

Moved runner.os, inputs.version, env.BUNDLE_EXT and steps.download-quarto.outputs.installer into step env vars. Quoted $RUNNER_OS, $RUNNER_ARCH, $installer, $GITHUB_ACTION_PATH and the wget URLs. Before writing version, installer and the TinyTeX path to GITHUB_OUTPUT/GITHUB_PATH, newlines are now stripped with printf | tr -d '\n\r'. Upstream ran the ls/find/dirname commands inside echo, so a failure there did not stop the step. They are now captured with '|| true' to keep that behaviour.

