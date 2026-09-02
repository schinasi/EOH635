# EOH 635: Environmental Epidemiology Methods — Interactive R Tutorials

**Live site: <https://schinasi.github.io/EOH635/>**

Asynchronous, browser-based R tutorials for EOH 635 at Drexel. Students open a link and run
real R code directly in the browser — via [webR](https://docs.r-wasm.org/webr/latest/) — with
no R or RStudio installation required. Built with [Quarto](https://quarto.org) and
[quarto-webr](https://github.com/coatless/quarto-webr).

## What's here

- **[Lesson 1: Intro to R and Handling Data](https://schinasi.github.io/EOH635/lesson1.html)**
  — R basics (assignment, data frames, indexing), project organization and file paths, loading
  packages, reading in data, exploratory functions (`head`, `str`, `summary`, `dim`), recoding
  variables, and merging in a second dataset by a shared key.
- **[Lesson 2: Exploratory Data Analysis & Data Cleaning](https://schinasi.github.io/EOH635/lesson2.html)**
  — identifying and recoding implausible values (age outliers), visualizing distributions
  (histograms, boxplots), and a first look at the course's primary exposure–outcome
  relationship — tree canopy cover and high blood pressure — including how to check whether a
  third variable (like gender or education) might be confounding that relationship.
- **[Data Codebook](https://schinasi.github.io/EOH635/codebook.html)** — full variable
  reference for the class dataset: what each column means, how it's coded, known missingness,
  and known data quirks to watch for.

Every "🧠 Your Turn" exercise gives students a task and a blank code editor, with the solution
hidden in a collapsed box underneath — the point is to try it yourself before checking.

## About the data

The class dataset is a **simulated** cross-sectional survey of 2,000 adults living in
Philadelphia in 2015, modeled on the Southeast Pennsylvania Household Survey — fictitious data
designed to resemble a real study so students can practice realistic epidemiologic analysis
without using identifiable data. It combines individual-level health outcomes and behaviors
(blood pressure, diabetes, asthma, mental health, stress, smoking, physical activity, etc.) and
demographics with real Philadelphia census-tract-level environmental and sociodemographic data
(tree canopy cover, traffic density, violent crime, poverty, education, race/ethnicity
composition). A separate tract-level greenness (NDVI) file is linked in during Lesson 1.

The health/behavior outcomes carry real, moderate, and deliberately calibrated associations with
plausible predictors (e.g., tree canopy cover is protective for blood pressure; traffic density
and smoking predict asthma) — including genuine confounding of the tree-canopy → blood-pressure
relationship by education, while gender is deliberately left unrelated to tree canopy as a clean
"not a confounder" contrast. Full variable definitions are in the
[codebook](https://schinasi.github.io/EOH635/codebook.html).

---

## For maintainers

<details>
<summary>Repo structure, updating the site, and other technical notes</summary>

### Structure

```
quarto_tutorials/
├── _quarto.yml                 # Project + site config (output-dir: docs, for GitHub Pages)
├── index.qmd                   # Landing page listing lessons
├── lesson1.qmd / lesson2.qmd   # The two lessons
├── codebook.qmd                # Data codebook
├── data/                       # Class data files, fetched by students' browsers at runtime
│   ├── class_data_raw.csv
│   ├── class_data_processed.csv  # class_data_raw.csv + NDVI merged — the Lesson 1 output
│   ├── PHL_NDVI_summer_annual.csv
│   └── codebook.xlsx
├── _extensions/coatless/webr/  # quarto-webr extension, vendored (pinned to 0.4.3)
└── docs/                       # Rendered site (what GitHub Pages actually serves)
```

### Updating the site

After editing any `.qmd` file:

```
quarto render
git add -A
git commit -m "describe the change"
git push
```

GitHub Pages (Settings → Pages → Deploy from branch → `main` / `/docs`) picks up the new
`docs/` output automatically within a minute or two. Nothing else needs to change — Pages
config, the data-fetch URL in `lesson1.qmd`, and the extension are already set up.

### Adding a future lesson

1. Duplicate `lesson1.qmd` (or `lesson2.qmd`) as a starting point, keeping the same YAML header
   (`webr:`, `filters: [webr]`, etc.).
2. Add it to the navbar in `_quarto.yml` and link it from `index.qmd`.
3. If it needs its own data file, drop it in `data/` and fetch it the same way existing lessons
   do: `download.file(paste0(data_base_url, "yourfile.csv"), file.path("data_raw", "yourfile.csv"))`.

### Design choices worth knowing before you edit

- **Hidden answers:** every "🧠 Your Turn" exercise uses a collapsed callout
  (`::: {.callout-note collapse="true"} ... :::`) for the solution.
- **All code chunks are `{webr-r}`, not `{r}`** — that's what quarto-webr turns into live,
  editable, runnable browser cells. A shared R session persists across chunks on the same page.
- **No local files needed by students:** webR can't see anyone's hard drive, so data loading
  goes through `download.file()` from this repo's raw GitHub URL rather than assuming a local
  working directory. Immediately after each fetch chunk, a green "In your RStudio script" box
  shows the simpler line students actually use in their own project.
- **Matches the course's `file.path()` convention, not `setwd()`** — paths use
  `file.path("data_raw", ...)` / `file.path("data_processed", ...)` throughout, matching the
  `EOH635_project` RStudio Project structure from the Week 1 setup assignment.

### On the simulated data

The data-generating scripts (original generation + a later remediation that fixed some
association/confounding bugs) are kept in a **separate, private** instructor-only repo, not
here — they document the dataset's exact ground-truth model, which would give away the answer
to the confounding exercise in Lesson 2 if published alongside it.

</details>
