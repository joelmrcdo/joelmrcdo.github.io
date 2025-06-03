# Trading Blog

This project hosts a simple trading blog built with [MkDocs](https://www.mkdocs.org). 
It aims to share trading ideas and Python examples through notebooks and posts.

## Project Goals

- Document and experiment with trading strategies.
- Provide example notebooks and reusable Python modules in `src/`.
- Publish the blog with GitHub Pages.

## Getting Started

1. Create a virtual environment and install dependencies:

   ```bash
   python3 -m venv .venv
   source .venv/bin/activate
   pip install mkdocs jupyter pandas yfinance matplotlib
   ```

2. Serve the site locally:

   ```bash
   mkdocs serve
   ```

   Then open `http://127.0.0.1:8000/` in your browser.

3. Explore the notebooks in the `notebooks/` directory with Jupyter.

## Deployment

The site is configured for GitHub Pages using MkDocs. Build and deploy with:

```bash
mkdocs gh-deploy
```

This command creates or updates the `gh-pages` branch containing the static site.

