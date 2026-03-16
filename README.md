# PoracleNG Documentation

Documentation for [PoracleNG](https://github.com/jfberry/PoracleNG). View the docs at [jfberry.github.io/PoracleNG-docs](https://jfberry.github.io/PoracleNG-docs/). Built with [MkDocs Material](https://squidfunk.github.io/mkdocs-material/).

## Local Development

```sh
pip install -r requirements.txt
mkdocs serve
```

Then open http://localhost:8000 to preview.

## Building

```sh
mkdocs build
```

Output is in the `site/` directory.

## Deployment

Pushes to `main` automatically build and deploy to GitHub Pages via GitHub Actions.
