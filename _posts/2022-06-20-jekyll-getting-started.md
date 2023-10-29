---
layout: post
title: Getting Started With Jekyll
author: Aidan Loughran
date: 2022-06-20
categories: jekyll
slug: getting-started
image: /img/jekyll-init.png
---
Every tech enthusiast needs their own blog, right? I decided I did, and I recently got around to playing with GitHub pages and Jekyll to see what I could get out of it. Here's how I got started deploying and customising my website and blog.

Organising your thoughts by turning them into semi-cohesive words on a page isn't for everyone, but it can be rather enjoyable to write about the stuff you're passionate about and find an excuse to play with a new framework and programming language while you're at it.

## About Jekyll

Of course, there are many choices out there for building your website or blog. From pure HTML, to generators like Jekyll, to full web stacks capable of serving dynamic content. As with most things in tech, there's no one-size-fits-all solution.

Static website generators are essentially pure HTML but on steroids. They offer powerful features to simplify your development. Each time you want to add or change content, it will need to be regenerated. This is different to a dynamic system which might process requests as they come in and retrieve data from a live database to construct the response dynamically. To build something like that, you'd need an engine like [Node.js][node].

Jekyll describes itself as "simple" and "blog aware". It is great for making simple static websites like a personal blog or a documentation site for one of your projects. It is the framework that [GitHub pages natively supports][gh-pages-jekyll].

It is written in Ruby, which might be a good or a bad thing depending on your perspective. You won't need to know much about Ruby to use Jekyll though, unless you decide to write a plugin.

### Features

- **Time saving -** It's very quick to get your website up and running so that you can focus on creating content. It supports themes, for example the default theme [Minima][minima], which serve as a good starting point and can be tweaked and added to as needed.


- **Low learning curve -** You don't need to learn complicated languages or server development. Likewise, there's no need to interact with a CMS. After the initial setup, you can crack on with writing pages and posts in HTML or Markdown.


- **Templating -** You can use templates and includes in your pages. Variables from config and data files and logic can also be used in your templates and pages using curly braces. Jekyll uses the [Liquid templating language][liquid] by Shopify.


- **SASS support -** It has [built in support for SASS][jekyll-assets] (Syntactically Awesome Style Sheets) and can support some other stuff too.


- **Perfect for devs -** You can build and host your site(s) for free on GitHub.

### Limitations

- **Static sites only -** You can't really implement features like handling submitted forms, dynamic APIs, user accounts and access control. For those, you'll need other tech.


- **Ruby -** It's slower than other languages, but you can always use another web server like Nginx to serve the static content that Jekyll generates.


- **Scalability -** While more scalable than a pure HTML site, static frameworks like Jekyll are still limited. There may come a time when you have so much content that it makes more sense to use a database and CMS, for instance.


- **GitHub pages limitations -** You probably want to use Jekyll to deploy a site to GitHub pages. Keep in mind you'll have no control over the back-end if you go this route, and you'll be bound to [GitHub's terms of service][gh-pages-tos] and any repo limits, but this shouldn't be an issue for most personal use cases.

## Getting Started 

### Install Ruby

The first thing to do is to install Ruby to your system. At the time of writing, the [Jekyll installation page][jekyll-install] lists **Ruby 2.5.0** or later as the official requirement. I won't go into detail here as the process depends on your system, but you can check the [Ruby installation page][ruby-install] for more info.

### Install Jekyll

With Ruby installed, you can install Jekyll and the bundler gem:

```sh
gem install jekyll bundler
```

### Create a Bundle

We're going to use a `Gemfile` to install and track dependency versions in the project, so start a new bundle in the root directory of your project:

```sh
bundle init
```

Now add Jekyll to the bundle. Specifically, add the version of Jekyll [that GitHub Pages is using][gh-pages-deps] (this may have changed, so click the link and find out):

```sh
bundle add jekyll --version "~>3.9.2"
```

Great! Now that Jekyll is included in the bundle, we can use it to generate a new project:

```sh
bundle exec jekyll new --force --skip-bundle .
```

**Don't forget the `.`!** It tells Jekyll to use the current directory.

`--force` tells Jekyll to not be shy about overwriting files in the directory.

`--skip-bundle` is here because Jekyll needs `webrick` which once shipped with Ruby but no longer does, so we need to add it to the bundle:

```sh
bundle add webrick
```

Now install and update everything:

```sh
bundle install && bundle update
```

### Test Jekyll

Jekyll should have generated a demo website with the default theme for you to work from. To test it out, you can have Jekyll serve over `localhost`:

```sh
bundle exec jekyll serve
```

![Jekyll demo site](/img/jekyll-init.png)

### Enabling GitHub Pages

Note that your repository name needs to be `username.github.io` to manage your personal site.

Before you push your Jekyll site to the GitHub repository, you'll need to enable GitHub Pages by going to your repository settings and opening the 'Pages' tab under 'Code and Automation'. Once enabled, whenever you push to the branch you've chosen, GitHub will rebuild your site.

![Enabling GitHub Pages](/img/enabling-gh-pages.png)

## Customising and Optimising Jekyll

I'm not going to go over development with Jekyll here; the [documentation][jekyll-docs] and countless online tutorials can handle that. However, I will go over some notable things that I did to customise Jekyll.

### `_config.yml`

Obviously the first thing you'll want to do is to optimise your site config. You'll want to choose your title and description while keeping in mind these values will be used on search engines.

Jekyll itself has [some configuration][jekyll-config]. Some good ones to add are `timezone`, `lang`, and `locale`.

```yaml
timezone: Europe/London
lang: en
locale: en_GB
```

Check your theme and plugin documentation for more values to add to your configuration. At the very least, make sure you have the `jekyll-seo-tag` plugin active and [RTFM][jekyll-seo-tag] if you care about people finding your site.

### Robots.txt, Sitemap, and Favicon

I haven't looked too deeply into SEO for this site so far, but it is always a good idea to set up your `robots.txt` and `sitemap.xml` for search engine crawlers. There's plenty of info about this process online.

You can also include a small icon file - your `favicon.ico` - to identify your website. There are plenty of free services online to generate a favicon package.

### Blog sub-URL

I wanted the website to be a sort of personal landing page and have all the blog stuff found at `{{ site.url }}/blog/` without creating a separate repository. To do this, I renamed the default `index.md` file to `blog.md` and added a title and permalink:

```yaml
---
layout: home
permalink: /blog/
title: Blog Home
---
```

Then I made a new `index.html`, with its own content and layout, that links to the blog section. What I found is that, in the Minima theme, pages with a title will show up nicely as links in the theme's header, which is useful.

To customise URLs for posts, change the `permalink` value in `_config.yml`. While I was at it, I also removed the date from URLs (it's ugly) and used `:slug` instead of `:title` for shorter URLs. You can define a custom slug in the front matter of each post you make.

```yaml
permalink: /blog/:categories/:slug
```

See [the docs][jekyll-permalinks] for more about permalinks.

### Up-to-date Theme

I rather like the Minima theme that Jekyll uses out of the box, but the latest version has more features like dark mode and an easy way to customise stylesheet variables.

To get the [latest version from GitHub][minima] during build, we need to enable the `jekyll-remote-theme` plugin which works with GitHub Pages.

In `Gemfile` I commented out the `minima` Gem and added the `jekyll-remote-theme` Gem. It looks something like this:

```gemfile
# Jekyll version for GitHub Pages
gem "jekyll", "~> 3.9.2"

# Jekyll theme
# gem "minima"

# Jekyll plugins
group :jekyll_plugins do
  gem "jekyll-feed", "~> 0.6"
  gem "jekyll-seo-tag"
  gem "jekyll-remote-theme"
end
```

In the `_config.yml` file, I added the plugin name to `plugins` and specified the repository for the theme (`jekyll/minima`) as the `remote_theme` value:

```yaml
plugins:
  - jekyll-feed
  - jekyll-seo-tag
  - jekyll-remote-theme

remote_theme: jekyll/minima
```

With the latest version of Minima, I could now change my `author` value from a name string to an object with a `name` and `email`, etc. I also added a `minima` value to add my theme choice and social links too.

```yaml
author:
  name: John Smith
  email: me@example.com

minima:
  skin: dark
  social_links:
    twitter: username
    github: username
```

I could also add `_sass/_custom-styles.scss` and `_sass/_custom-variables.scss` to my project to easily change bits of the theme such as the colour scheme.

## What's next for me?

Glad you asked. For now, I'm happy with Jekyll and its workflow, and I'm pretty satisfied with the blog theme. I may take a deeper look at optimising SEO and things of that nature, but my focus now is on writing articles and adding features to my personal home/landing page - I have a vision for a sort of interactive project & tech stack showcase in mind.

To reach my vision I may need to write a plugin or two which would give me a chance to learn a bit of Ruby. Unfortunately, GitHub Pages does not support unapproved plugins, but I have done some testing of [jekyll-deploy-action][jekyll-deploy-action] which lets you deploy using a GitHub Action instead of the built-in repo support. I managed to get this working, so I'll be ready to switch to it when the time comes.

I did recently begin developing [my own static site generator](https://github.com/aidlran/static-site-generator) for other projects. I can foresee my generator borrowing certain things that I like about Jekyll, but my plan is to make it more modular & lightweight, as is my philosophy on software. Maybe I'll eventually port this website and even create a GitHub Action for it. I certainly don't see me needing a serious back-end with a database anytime soon.

I'm considering containerising. I don't really use Ruby all that much, so it might be an idea to develop in a Docker container rather than have it and its dependencies installed to my core system. Docker containers are also great for ensuring the project is using *specific* dependency versions, so that the GitHub Pages build environment can be replicated accurately.

[node]: https://nodejs.org/en/about/

[gh-pages-jekyll]: https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/about-github-pages-and-jekyll

[gh-pages-tos]: https://docs.github.com/en/site-policy/github-terms/github-terms-for-additional-products-and-features#pages

[gh-pages-deps]: https://pages.github.com/versions/

[minima]: https://github.com/jekyll/minima

[liquid]: https://shopify.github.io/liquid/

[jekyll-docs]: https://jekyllrb.com/docs/

[jekyll-install]: https://jekyllrb.com/docs/installation/

[jekyll-assets]: https://jekyllrb.com/docs/assets/

[jekyll-config]: https://jekyllrb.com/docs/configuration/options/

[jekyll-permalinks]: https://jekyllrb.com/docs/permalinks/

[jekyll-seo-tag]: https://github.com/jekyll/jekyll-seo-tag

[jekyll-deploy-action]: https://github.com/jeffreytse/jekyll-deploy-action

[ruby-install]: https://www.ruby-lang.org/en/documentation/installation/
