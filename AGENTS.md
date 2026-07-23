---
name: gitea-practical-presentation
description: Project guidance for maintaining and rendering the Marp source deck.
---

# Project guidance

## Source of truth

Edit `gitea-practical-presentation.md`. It is the source presentation and contains both the slide content and the Marp theme. Treat generated HTML, PDF, and image files as build artifacts.

## Rendering

Render with Marp CLI and enable HTML so the two-column layout wrappers are preserved:

```sh
marp --html gitea-practical-presentation.md --output gitea-practical-presentation.html
```

Keep `images/gitea-logo.svg` at its current relative path. After content or CSS changes, render the deck and inspect every slide for overflow, clipped content, and unreadably small text.

## Release workflows

- `.gitea/workflows/presentation-release.yml` runs in the Gitea runner's Linux container environment.
- `.github/workflows/presentation-release.yml` runs on GitHub's hosted `ubuntu-latest` runner.
- Push the same annotated `v*` tag to `jnxpublic` and `github` when publishing on both services.

## Content conventions

- Separate slides with `---`.
- Put per-slide Marp directives immediately after a separator.
- Keep presenter-only notes in HTML comments so they become Marp speaker notes.
- Prefer Markdown for audience-facing copy; use structural HTML only where Marp needs layout hooks.
- Preserve links to primary Gitea documentation when changing sourced claims.
