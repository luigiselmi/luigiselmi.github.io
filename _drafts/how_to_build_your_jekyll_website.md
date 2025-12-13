---
layout: post
title:  "How to build your static website with Jekyll and GitHub Pages"
categories: self-publishing, GitHub, Jekyll
permalink: /how_to_build_your_jekyll_website.html
---
## Why bother ?
There are many good reasons to have your own website(s) online. For example to communicate with friends, let your enemies know you are alive and kicking, write about your last trip. Nowadays a website is not just for fun it's somewhat similar to a bank account, or better a reputation account. If you write something interesting and useful it might represent your best investment of time: low risks and high ~~returns~~ visibility. A website or a blog is like a (human) Capital Accumulation Plan.

## Options to create and publish a website
You can easily build and publish a website with [WordPress](https://wordpress.com/), [Google Sites](https://sites.google.com/) or [Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html), this last one with some more technical skill. My opinion is that [GitHub Pages](https://docs.github.com/en/pages) is better suited for scientists and software developers who know what a version control system is, in particular [git](https://git-scm.com/), and are comfortable using a command line interface.

## GitHub Pages
If you have an account on GitHub you can create a repository that will contain all the files you need for your website. The idea is to create or change the content of your website locally, commit the changes and push them on your GitHub repository. There is no need to learn HTML, CSS, JavaScript or to be a web architect to create and publish a website. You can write your content using the **Markdown** markup language, choose a theme among many available online for the rendering, and follow the conventions used by **Jekyll** for the structure of your content to transform it into a website. A GitHub repository used as a website must follow some rules. The name of the repository must have the structure **username.github.io** where you replace username with your GitHub user name. Other rules are explained in the GitHub Pages [documentation](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll).

## Jekyll
[Jekyll](https://jekyllrb.com/) is a framework for creating static websites and blogs. it is based on the *Convention over Configuration* design principle and on the Ruby programming language.

### The structure of a Jekyll website
A Jekyll-based site has a defined structure and must contains some mandatory files and folders. This is the *theoretical minimum*:

* _posts/
* _site/
* _config.yml
* Gemfile
* index.md

#### The configuration file
**_config.yml** is the only configuration file and, depending on the chosen theme, contains information such as the title of the website, a short description, its base url, the theme, and the plugins, aka Ruby gems, used to build the website.

#### The plugins file
**Gemfile** contains a list of Ruby plugins, called gems, to be installed and run in order to build the website. The main gems are *jekyll* that implements the commands to be used to start a local web server for our web site, and *minima* that implements the default theme.  The Gemfile contains the list of Ruby packages, called gems, used to build the site, with Jekyll being the default.

#### The index page
**index.md** is the landing page of your website. Written in simple markdown, is the page visitors will see when they follow the URL of your GitHub repository. You can add other pages, such as an about page to inform visitors what the site is about, by adding a markdown file in the root folder. For example you can create the *about.md* file adding the following details at the beginning
```
---
layout: page
title: About
permalink: /about/
---
```

#### The _posts folder
A Jekyll website usually contains blog posts and the **_posts** folder is there exactly for that purpose. The name of a post file starts with a date in the form YYYY-MM-DD-. So if you want to write your opinion about Jekyll you may create a file in the _posts/ folder naming it, e.g. *2025-12-11-welcome_to_jekyll.md*. The head of the file must contain some details like in the following example
```
---
layout: post
title:  "Welcome to Jekyll!"
date:   2025-12-11
categories: jekyll website blogs
---
```
#### The _site/ folder
This folder contains the HTML and CSS files created by Jekyll from the original markdown files. It is created locally from scratch every time you start the Jekyll's local web server so there is no need to edit or change anything in this folder. You do not need to push this folder to your GitHub repository. The GitHub Pages workflow will use Jekyll to create the HTML and CSS files for your website exactly in the same way you do it locally.


## Software to install
In order to get started you have to have installed the following software

* Git
* Ruby
* Bundler
* Jekyll  

Follow the instructions on the software websites.

### Git
Git is a version control system that allows a team of people to share and work on documents in a consistent way. It is a fundamental tool for software developers. Git implements a workflow. You start by creating or editing one or more documents, then you use your local git client to add them to a list of documents that you want to commit in order to be shared and finally you push the commit to the server. Once a document is in the remote repository other people can clone or fork your documents, make changes and push the modified version if they have the rights to do so. The [Git website](https://git-scm.com/install/) provides information to install a Git client in any *NIX environments equipped with terminals such as bash by default. The [MS Windows version](https://gitforwindows.org/) comes with Git Bash, a bash terminal emulator for Windows. Once you are done installing Git you should be able to see its version by running the following command from the terminal, in this example from Git Bash for MS Windows.

```
$ git -v
git version 2.49.0.windows.1
```

### Ruby
Ruby is an interpreted programming language and is the language used for all the software packages, or plugins, used by Jekyll, that is itself a Ruby package. A [Ruby interpreter](https://www.ruby-lang.org/it/documentation/installation/) must be installed locally in order to run Jekyll and all the plugins. After the installation you should see its version, something similar to the following

```
$ ruby -v
ruby 3.4.7 (2025-10-08 revision 7a5688e2a2) +PRISM [x64-mingw-ucrt]
```
With Ruby comes also a default gems management system. Check that it works properly running the command. In my case its 4.0.0, you might see a different version number

```
$ gem -v
4.0.0
```

### Bundler
[Bundler](https://bundler.io/) is a software packages management system for Ruby. It is used to install, update, and execute Ruby packages, aka gems. It can be used instead of gem, the default system. It is itself a Ruby package and can be installed using the command

```
$ gem install bundler

```

 Checking its version after the installation you should see something similar to this

```
$ bundle -v
4.0.1
```
### Jekyll
Jekyll is a Ruby gem that contains a web server that can be used to test your website during its development. It can be installed using the default Ruby gems management system

```
$ gem install jekyll

```
If you check its version after the installation you should see something similar to this

```
$ jekyll -v
jekyll 4.4.1

```
## The text editor
You don't need a full fledged Integrated Development Environment such as Visual Studio Code to write your markdown files but a text editor with some features like syntax highlighting can help. I still use [Atom](https://atom-editor.cc/), originally developed for Git users and now replaced by VS Code. Any bash terminal provides *nano* that is also available in Git Bash for Windows.

## Let's get started
We are now ready to build a first version of our website.

## Themes
There are several themes available that can be used with GitHub Pages. Each theme is based on one or more Ruby gems. The Jekyll gem can be launched with the Ruby gem command or with Bundler, a software for managing Ruby gem packages. Jekyll uses Sass to build CSS stylesheets. We will use two of the most used Minima and Minimal Mistakes

### Minima
This theme is supported by GitHub Pages and is very simple to configure and manage. It is the default theme installed when you create a site with the command
```
$ jekyll new my-awesome-site
```

### Minimal Mistake
