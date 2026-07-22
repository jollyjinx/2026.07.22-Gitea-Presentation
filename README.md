# Gitea in Practice presentation

The editable presentation source is `gitea-practical-presentation.md`. It is a 16:9 Marp deck with the theme embedded in the Markdown front matter, so the content and styling live together.

Build the HTML presentation with:

```sh
marp --html gitea-practical-presentation.md \
  --output gitea-practical-presentation.html
```

Use `--watch` while editing:

```sh
marp --html --watch gitea-practical-presentation.md
```

The `--html` option enables the small amount of structural HTML used for two-column layouts. Slide boundaries are Markdown horizontal rules (`---`), and speaker notes are HTML comments immediately before a slide boundary.

The local logo must remain at `images/gitea-logo.svg` so Marp can resolve it from the Markdown source.

## Release builds

The committed Gitea Actions workflow at
`.gitea/workflows/presentation-release.yml` builds a PDF and an HTML bundle
when a tag beginning with `v` is pushed. It publishes both files as assets on
the corresponding Gitea release.

Repository Actions must be enabled, an `ubuntu-latest` runner must be online,
and the Actions job token must be allowed to write releases. Create a release
with:

```sh
git tag -a v2026.07.22 -m "Presentation release"
git push jnxpublic v2026.07.22
```
