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

