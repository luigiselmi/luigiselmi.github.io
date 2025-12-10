---
layout: post
title:  "How to build your static website with Jekyll and GitHub Pages"
categories: self-publishing, GitHub, Jekyll
permalink: /how_to_build_your_jekyll_website.html
---
## Why bother ?
There are many good reasons to have your own website(s) online. For example to communicate with friends, let your enemies know you are alive and kicking, write about your last trip. Nowadays a website is not just for fun it's somewhat similar to a bank account, or better a reputation account. If you write something interesting and useful it might represent you best investment of time: low risks and high ~~return~~ visibility. A website is like a Human Capital Accumulation Plan.

## Options
You can easily build and publish a website with [WordPress](https://wordpress.com/) or [Google Sites](https://sites.google.com/). My opinion is that [GitHub Pages](https://docs.github.com/en/pages) is better suited for scientists and software developers. 

## GitHub Pages

## Jekyll
[Jekyll](https://jekyllrb.com/) is a framework for creating static websites. GitHub Pages accepts sites created with Jekyll, primarily using Markdown as the markup language, so you don't need to know HTML, CSS, or JavaScript. Jekyll is a framework based on the Ruby programming language, and its interpreter must be installed locally to verify that the site works before deploying to GitHub. A Jekyll-based site has a defined structure and contains files for the site's pages in the root folder or the _pages folder (home, about, posts, or other) and other folders for content: _posts, _site, _drafts, and assets. The post draft files go in _draft, the post files go in _posts, and the assets folder contains images and other files to be inserted into the pages. The _site folder is automatically created by Jekyll based on the contents of the other folders and the _config.yml configuration file. This last file specifically defines the site title and the theme, or presentation (rendering) of the pages. Each theme is based on one or more Ruby gems. The Gemfile contains the Ruby packages, called gems, used to build the site, with Jekyll being the default. The Jekyll gem contains a web server that can be used to test the site during development. The Jekyll gem can be launched with the Ruby gem command or with Bundler, a software for managing Ruby gem packages. Jekyll uses Sass to build CSS stylesheets.

## Themes
There are several themes available that can be used with GitHub Pages. We will use two of the most used Minima and Minimal Mistakes

### Minima
This theme is supported by GitHub Pages and is very simple to configure and manage. It is the default theme installed when you create a site with the command
```
$ jekyll new my-awesome-site
```

### Minimal Mistake
