# EOH 635 Quarto Tutorials — Setup & Publishing Guide

Interactive, in-browser R tutorials for the class, built with [Quarto](https://quarto.org) +
[quarto-webr](https://github.com/coatless/quarto-webr). Students open a link and run real R
code in their browser — no R/RStudio install needed.

## What's in this folder

```
quarto_tutorials/
├── _quarto.yml            # Project + site config
├── index.qmd               # Landing page listing lessons
├── lesson1.qmd              # Lesson 1 (converted from lessonone_2025.Rmd)
├── data/                   # Class data files (also need to be public on GitHub — see below)
│   ├── class_data.csv
│   └── PHL_NDVI_summer_annual.csv
├── _extensions/coatless/webr/  # The quarto-webr extension (already vendored — no install step needed)
└── .gitignore
```

The `quarto-webr` extension files are already included here (pinned to release `0.4.3`), so you
do **not** need to run `quarto add` yourself. If you ever want to update it to a newer version,
run `quarto add coatless/quarto-webr` from inside this folder and say yes to overwrite.

## One-time setup on your computer

1. **Install Quarto** (if you don't have it): <https://quarto.org/docs/get-started/>
   RStudio (current versions) also bundles Quarto, so you may already have it.
2. Open a terminal in this `quarto_tutorials` folder and preview the site:
   ```
   quarto preview
   ```
   This opens the site in your browser with live-reload. **Important:** the "Reading Data into R"
   chunks in `lesson1.qmd` fetch the class data from a public GitHub URL, so:
   - Data loading chunks will show a download error until you complete the GitHub steps below and
     update the placeholder URL.
   - Everything else (Sections 1–3, and any exercise that doesn't touch `data`/`data2`) will work
     immediately, with no setup.

## Publishing so students can use it (GitHub + GitHub Pages)

webR runs entirely in the student's browser, so it can't reach files on your OneDrive — it needs
to fetch the data files from a public URL on the web. The simplest way to do that is to host this
whole folder on GitHub and turn on GitHub Pages.

1. **Create a new GitHub repository** (e.g. `eoh635-tutorials`). It can be public — you confirmed
   `class_data.csv` and the NDVI file are de-identified and OK to host publicly for this purpose.
2. **Push this folder to it:**
   ```
   cd "quarto_tutorials"
   git init
   git add .
   git commit -m "Lesson 1: interactive R tutorial"
   git branch -M main
   git remote add origin https://github.com/<YOUR-GITHUB-USERNAME>/<YOUR-REPO-NAME>.git
   git push -u origin main
   ```
3. **Update the data URL placeholder.** In `lesson1.qmd`, find this line (it appears once, in the
   "Reading Data into R" section):
   ```r
   data_base_url <- "https://raw.githubusercontent.com/GITHUB-USERNAME/REPO-NAME/main/data/"
   ```
   Replace `GITHUB-USERNAME` and `REPO-NAME` with your actual username/repo, commit, and push again.
   (This works as soon as the repo is pushed and public — it does **not** require Pages to be live yet.)
4. **Render the site and publish it:**
   ```
   quarto render
   git add .
   git commit -m "Render site"
   git push
   ```
   Then in the GitHub repo: **Settings → Pages → Build and deployment → Deploy from a branch →
   Branch: `main`, folder: `/docs`** (this matches the `output-dir: docs` setting in `_quarto.yml`).
   After a minute or two, your site is live at `https://<username>.github.io/<repo-name>/`.
5. Share that link with students. Anytime you edit a lesson, just re-run `quarto render`, commit,
   and push — GitHub Pages updates automatically.

## Adding future lessons

1. Duplicate `lesson1.qmd` as `lesson2.qmd` (or create fresh), keeping the same YAML header
   (`webr:`, `filters: [webr]`, etc.).
2. Add it to the navbar in `_quarto.yml` and link it from `index.qmd`.
3. If a new lesson needs its own data file, drop it in `data/` and reference it the same way —
   `download.file(paste0(data_base_url, "yourfile.csv"), "yourfile.csv")`.

## Design choices, so you can adapt them later

- **Hidden answers:** every "🧠 Your Turn" exercise uses a collapsed callout
  (`::: {.callout-note collapse="true"} ... :::`) for the solution, so students see a blank editor
  first. Search `lesson1.qmd` for `Your Turn` to find/add more.
- **Interactivity:** all code chunks are `{webr-r}` instead of `{r}`, which quarto-webr turns into
  live, editable, runnable cells. A shared R session persists across chunks on the same page, so
  `data` created early in the page is still available later.
- **No local files needed by students:** because webR can't see anyone's hard drive, all data
  loading goes through `download.file()` from the public GitHub URL rather than assuming a local
  working directory — this is the one structural change from the original `.Rmd` you'll notice
  throughout.
