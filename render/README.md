# Usage

```yaml
- name: Render Quarto Project
  uses: quarto-dev/quarto-actions/render@v2
  with:
    to: html # If set, it will be equivalent to `quarto render --to html`
    path: source-folder # By default, the current working dir is used i.e `quarto render .`
```

`path:` can be a path to a subdirectory containing the quarto project to be rendered or a path to a single .qmd file

## Rendering with a Quarto profile

You can render with a specific profile by setting the `QUARTO_PROFILE` environment variable. For example, to render with the `preview` profile (i.e. you have a `_quarto-preview.yml` file in your project), you can do:

```yaml
- name: Render Quarto Project
  uses: quarto-dev/quarto-actions/render@v2
  env:
    QUARTO_PROFILE: preview
  with:
    to: html
```

## Privacy

This Action contacts Chainguard's licensing server to verify authorization. Connection metadata (IP address, GitHub repository identifier, timestamp, and any metadata encoded in the auth token) is transmitted to Chainguard, Inc. even if authorization is denied in accordance with our [Privacy Notice](https://www.chainguard.dev/legal/privacy-notice)
