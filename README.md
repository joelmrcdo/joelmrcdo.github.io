# Trading Blog

This project hosts a simple trading blog built with [Jekyll](https://jekyllrb.com/) and served through GitHub Pages. It aims to share trading ideas and Python examples through notebooks and posts.

## Project Goals

- Document and experiment with trading strategies.
- Provide example notebooks and reusable Python modules in `src/`.
- Publish the blog with GitHub Pages.

## Getting Started

1. Install Ruby and Bundler. On Ubuntu you can run:

   ```bash
   sudo apt-get install ruby-full build-essential
   gem install bundler
   ```

2. Install the project dependencies:

   ```bash
   bundle install
   ```

3. Serve the site locally:

   ```bash
   bundle exec jekyll serve
   ```

   Then open `http://127.0.0.1:4000/` in your browser.

4. Explore the notebooks in the `notebooks/` directory with Jupyter.

## Deployment

Build the static site with:

```bash
bundle exec jekyll build
```

The generated HTML will be available in the `_site/` directory and can be deployed to GitHub Pages.
