# ckck12.github.io

Personal academic homepage of Chan Park, built on the [Academic Pages](https://github.com/academicpages/academicpages.github.io) Jekyll template (v0.9.x) and hosted on GitHub Pages at https://ckck12.github.io.

## Structure

- `_pages/about.md` — landing page
- `_pages/publications.html` + `_publications/` — publication list (one file per paper; thumbnails in `images/pubs/`)
- `_pages/portfolio.html` + `_portfolio/` — projects (images in `images/projects/`)
- `_pages/cv.md` + `files/Chan_Park_CV.pdf` — CV
- `_config.yml`, `_data/navigation.yml` — site settings and top navigation
- `_sass/layout/_publications.scss` — styling for the publication list

## Local build (optional)

```
bundle install
bundle exec jekyll serve
```

The template is MIT-licensed (see `LICENSE`).
