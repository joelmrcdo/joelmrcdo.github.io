# Trading Blog

This project hosts a simple trading blog built with [Jekyll](https://jekyllrb.com/) and served through GitHub Pages. It aims to share trading ideas and Python examples through notebooks and posts.

## Project Goals

- Document and experiment with trading strategies.
- Publish the blog with GitHub Pages.

## Theme

The site currently uses the `jekyll-theme-minimal` theme configured in
`_config.yml`. To try a different look, change the `theme:` value in that file
and update the `Gemfile` if required. After editing either file, run:

```bash
bundle install
```

to install any new dependencies.

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

   Run `bundle install` again whenever you modify `_config.yml` or the
   `Gemfile` to make sure any new gems are installed. These commands
   should be executed without `sudo` so Bundler can manage gems in your
   user environment.

3. Serve the site locally:


   ```bash
   bundle exec jekyll serve
   ```

   Run the serve command as a normal user as well—no root privileges are
   required.

   Then open `http://127.0.0.1:4000/` in your browser.

4. Explore the notebooks in the `notebooks/` directory with Jupyter.

### Changing the Theme

To use a theme gem such as [`minima`](https://github.com/jekyll/minima),
add it to your `Gemfile` and update `_config.yml`:

```ruby
# Gemfile
gem "minima"
```

```yaml
# _config.yml
theme: minima
```

After modifying the `Gemfile`, run `bundle update` so `Gemfile.lock`
includes the theme version.

## Deployment

Build the static site with:

```bash
bundle exec jekyll build
```

The generated HTML will be available in the `_site/` directory and can be deployed to GitHub Pages.


## Interactive Notebooks

This site now loads a lightweight Python environment in the browser using thebe and JupyterLite. Posts such as the moving average crossover example include a **Run** button. Click it to execute code cells directly on the page. If network access is blocked, the notebook falls back to built-in sample data. When network access is available, packages like `yfinance` will fetch real market prices automatically.

The required thebe scripts and stylesheets are bundled locally under `assets/thebe/` so the site works without an internet connection.

=======
### Disclaimer
This material is for educational purposes only and is not financial advice.

