# Le Monde Web Scraper

A web-scraping project that pulls the latest headlines from the
[Le Monde (English edition)](https://www.lemonde.fr/en/) homepage and saves them
to a CSV file. The scrape runs automatically every hour via GitHub Actions.

## What it does

- **[Scraper.ipynb](Scraper.ipynb)** — Jupyter notebook that does the scraping. It uses
  `requests` to download the Le Monde English homepage and `BeautifulSoup` to parse
  the HTML, pulling out each article's:
  - title
  - link
  - premium / subscriber flag
  - subhead
  - byline
  - image URL
  - article type

  The results are loaded into a `pandas` DataFrame and written to `leMonde.csv`.

- **[leMonde.csv](leMonde.csv)** — the output data. One row per article, refreshed on
  every run.

- **[index.html](index.html)** — a simple page that fetches `leMonde.csv` and renders it
  as an HTML table in the browser.

## Running it locally

Install the dependencies:

```bash
pip install jupyter lxml pandas requests beautifulsoup4 html5lib
```

Then either open `Scraper.ipynb` in Jupyter and run all cells, or execute it from the
command line:

```bash
jupyter nbconvert --to notebook --execute Scraper.ipynb --stdout
```

This regenerates `leMonde.csv`.

## Automated scraping with GitHub Actions

The scrape runs on a schedule in the cloud — no need to run it on your own machine. The
workflow is defined in [.github/workflows/main.yml](.github/workflows/main.yml).

What the workflow does:

1. **Triggers** — runs once an hour on a cron schedule (`0 */1 * * *`), and can also be
   started manually from the **Actions** tab using the *Run workflow* button
   (`workflow_dispatch`).
2. **Checks out** the repository.
3. **Sets up Python** (3.13) and installs the required packages.
4. **Executes** `Scraper.ipynb`, which refreshes `leMonde.csv`.
5. **Commits and pushes** any changes back to the repo with a timestamped message
   (`Latest data: <timestamp>`). If nothing changed, the run exits cleanly without an
   empty commit.

> **Note:** the workflow needs `permissions: contents: write` so the Action is allowed to
> push the updated CSV back to the repository. Without it the commit step fails.

### References

This setup is based on:
- Sample scraper workflow: <https://github.com/jsoma/scraper-sample/blob/7d3bd39ea3ef5b1b531e1db9f2acec3a685ea903/.github/workflows/scraper.yml>
- Tutorial video: <https://www.youtube.com/watch?v=QNKxzkNpsko>
