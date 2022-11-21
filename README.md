<div align=center>

# My Website

[aidlran.github.io](https://aidlran.github.io)

</div>

## About

My  website is primarily powered by Jekyll and is hosted with GitHub Pages.

It also uses my [web-wishlist](https://github.com/aidlran/web-wishlist) project, which uses Node.js.

## Installation

See Jekyll's [website](https://jekyllrb.com/docs/installation/) for information about its requirements.

[Node.js](https://nodejs.org/en/download/) is required to build the Node powered components.

With the requirements met:

```sh
# Install the bundler gem 
gem install bundler

# Use this to always install dependencies to the project's vendor directory
bundle config --global path vendor/bundler

# Install Gemfile gems
bundle install

# Build Jekyll website
bundle exec jekyll build
```

To build the Node components on top of the generated site:

```sh
# Clean install package.json dependencies
npm ci

# Run the build script
npm run build
```

> **Note:** Jekyll will clear the `_site` directory when it builds, so building the additional components should be done after Jekyll does its thing.

## TODO

- [ ] Look into plugins especially for SEO.
- [ ] Migrate to another framework, maybe Hugo, because Jekyll kinda annoys me. Eventually, move to [my own](https://github.com/aidlran/static-site-generator).
