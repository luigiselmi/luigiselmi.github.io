---
layout: post
title:  "How to build your static website with Jekyll and GitHub Pages"
categories: self-publishing, GitHub, Jekyll
permalink: /how_to_build_your_jekyll_website.html
---
<img src="https://upload.wikimedia.org/wikipedia/commons/5/57/Dr_Jekyll_and_Mr_Hyde_poster.png" alt="Dr. Jekyll and Mr. Hyde"/>
## Why bother ?
There are many good reasons to have your own website(s) online. For example to communicate with friends, let your enemies know you are alive and kicking, write about your last trip. Nowadays a website is not just for fun it's somewhat similar to a bank account, or better a reputation account. If you write something interesting and useful it might represent your best investment of time: low risks and high ~~returns~~ visibility. A website or a blog is like a (human) Capital Accumulation Plan. It is a fundamental piece of your business infrastructure, whatever your business is.

## Options to create and publish a website
You can easily build and publish a website with [WordPress](https://wordpress.com/), [Google Sites](https://sites.google.com/) or [Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html), this last one with some more technical skill. A static website simply means that it works without a database. My opinion is that [GitHub Pages](https://docs.github.com/en/pages) is better suited for scientists and software developers who know what a version control system is, in particular [git](https://git-scm.com/), and are comfortable using a command line interface.

## GitHub Pages
If you have an account on GitHub you can create a repository that will contain all the files you need for your website. The idea is to create or change the content of your website locally, commit the changes and push them on your GitHub repository. There is no need to learn HTML, CSS, JavaScript or to be a web architect to create and publish a website. You can write your content using the **Markdown** markup language, choose a theme among many available [online](https://jekyllthemes.io/) for rendering, and follow the conventions used by **Jekyll** for the structure of your content to transform it into a website. A GitHub repository used as a website must follow some rules. The name of the repository must have the structure **username.github.io** where you replace username with your GitHub user name. Other rules are explained in the GitHub Pages [documentation](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll).

## Jekyll
[Jekyll](https://jekyllrb.com/) is a framework for creating static websites and blogs. it is based on the *Convention over Configuration* design principle and on the Ruby programming language.

## Software to install
In order to get started you have to have installed the following software

* Git
* Ruby
* Bundler
* Jekyll  

Here I write a short description of the tools. For more details and information on how to install them follow the instructions on the tool's website.

### Git
Git is a version control system, based on a client-server architecture, that allows a team of people to share and work on documents in a consistent way. It is a fundamental tool for software developers. Git implements a workflow. You start by creating or editing one or more documents, then you use your local git client to add them to a list of documents that you want to commit in order to be shared and finally you push the commit to the server. Once a document is in the remote repository other people can clone or fork your documents, make changes and push the modified version, if they have been granted the rights to do so. The [Git website](https://git-scm.com/install/) provides information to install a Git client in any *NIX environments equipped with terminals such as bash by default. The [MS Windows version](https://gitforwindows.org/) comes with Git Bash, a bash terminal emulator for Windows. Once you are done installing Git you should be able to see its version by running the following command from the terminal, in this example from Git Bash for MS Windows.

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
[Bundler](https://bundler.io/) is a software packages management system for Ruby. It is used to install, update, and execute Ruby packages, aka gems. If a Ruby gem is not already installed in your local environment Bundler will download it from [RubyGems](https://rubygems.org/) and install in your local environment. Bundler can be used instead of gem, the default system. It is itself a Ruby package and can be installed using the command

```
$ gem install bundler

```

 Checking its version after the installation you should see something similar to this

```
$ bundle -v
4.0.1
```
### Jekyll
Jekyll is a Ruby gem that contains a web server that you can use to create and test your website during its development. It can be installed using the default Ruby gems management system

```
$ gem install jekyll

```
If you check its version after the installation you should see something similar to this

```
$ jekyll -v
jekyll 4.4.1

```
For more commands available with Jekyll see also [here](https://jekyllrb.com/docs/usage/)

## The text editor
You don't need a full fledged Integrated Development Environment such as Visual Studio Code to write your markdown files but a text editor with some features like syntax highlighting can help. I still use [Atom](https://atom-editor.cc/), originally developed for Git users and now replaced by VS Code. Any bash terminal provides *nano* that is also available in Git Bash for Windows.

## Let's get started
We are now ready to build the scaffold for a first version of our website. We only have to open a terminal, e.g. Git Bash for MS Windows, and run Jekyll with the name of your website repository. Let's say your user name on GitHub is *mrhyde*, the command to create your local repository is  

```
$ jekyll new mrhyde.github.io
```
If you are not Mr. Hyde you will replace the *mrhyde* part with your user name on GitHub. It can also be the name of an organization that you have created on GitHub. The default theme is *minima*. You have just created a directory mrhyde.github.io. If you move inside it

 ```
 $ cd mrhyde.github.io
 ```
you will see some files and folders but before that you can already start Jekyll and see the result with a browser

```
$ bundle exec jekyll serve
```
The default port number of the Jekyll local web server is 4000 . You can use a different port number, e.g. 4001

```
$ bundle exec jekyll serve -P 4001
```

If you open your web browser pointing to http://localhost:4000 you should see something similar to [this](https://jekyll.github.io/minima/).

## Themes
A theme is a collection of templates and stylesheets that control the look and layout of a website. There are several themes for Jekyll available online that can be used with GitHub Pages. Each theme is based on one or more Ruby gems. In this post we will only look at the default one, *minima*.

### Minima
This theme is supported by GitHub Pages and is very simple to configure and manage. It is the default theme of the web site you have just created with Jekyll.

### The structure of a Jekyll website
A Jekyll-based site must contains some mandatory files and folders. The two main types of resources are pages and posts. Posts are kept in a separate folder while pages can be found in the repository root folder. The structure of a Jekyll website depends on the theme. This is the structure of the website based on the *minima* theme, version 2.5 or newer, that you have just created with Jenkins:

* _posts/
* _site/
* _config.yml
* Gemfile
* index.md  
* about.md

#### The configuration file
**_config.yml** is the only configuration file and, depending on the chosen theme, contains information such as the title of the website, a short description, its base URL, the theme, and the plugins, aka Ruby gems used to build the website with that theme.

#### The plugins file
**Gemfile** contains a list of Ruby plugins, called gems, to be installed and run in order to build the website. The main gems are *jekyll* that implements the commands to be used to start a local web server for our web site, and *minima* that implements the default theme.

#### The index page
**index.md** is the landing page of your website. Written in simple markdown, is the page visitors will see when they follow the URL of your GitHub repository. You can add other pages, such as the default *About* page, by adding a markdown file in the root folder.

#### The _posts/ folder
A Jekyll website usually contains blog posts and the **_posts** folder is there exactly for that purpose. The name of a post file starts with a date in the form YYYY-MM-DD-.

#### The _site/ folder
This folder contains the HTML and CSS files created by Jekyll from the original markdown files. It is created locally from scratch every time you start the Jekyll's local web server so there is no need to edit or change anything in this folder. You do not need to push this folder to your GitHub repository. The GitHub Pages workflow will use Jekyll to create the HTML and CSS files for your website exactly in the same way you do it locally with your Jekyll gem.

### Configuring your website
The first thing you may want to change is the name shown on the upper left-hand of the website landing page. You only need to open the *_config.yml* file and update the title, e.g. to *Mr. Edward Hyde*.

```
title: Mr. Edward Hyde
email: your-email@example.com
description: >- # this means to ignore newlines until "baseurl:"
  Write an awesome description for your new site here. You can edit this
  line in _config.yml. It will appear in your document head meta (for
  Google search results) and in your feed.xml site description.
baseurl: "" # the subpath of your site, e.g. /blog
url: "" # the base hostname & protocol for your site, e.g. http://example.com
twitter_username: jekyllrb
github_username:  jekyll

# Build settings
theme: minima
plugins:
  - jekyll-feed
```
In order to see a change made in the configuration file to take effect you have to restart Jekyll.

### Adding a page
Adding a page to the *minima* scaffold is just about creating a new markdown file in the root folder. For example it can be your resume, e.g. resume.md, with a content like this

```
---
layout: page
title: Resume
permalink: /resume/
---
I am a part-time medical doctor based in Soho, in London's West End, United Kingdom.
```
You don't have to restart Jekyll to see the effect when you create or change a page or a post. You should see the link to the new *Resume* page on the website menu, next to the default *About* page.

### Adding a picture
You may want to add a picture to a page or post, for example in your website landing page, e.g. *mrhyde.jpg*. You have just to create an **assets/** folder, put the image there, and add a link to it in the *index.md* file.

```
---
layout: home
---
<img src="assets/mrhyde.jpg" alt="Mr. Hyde">
```

### Writing a post
As said before the file name for a post must begin with a date otherwise it will not be shown. Let's say you are Mr. Hyde and want to write your opinion about Dr. Jekyll and publish your post on December 25th 2025. You create a markdown file, e.g. *2025-12-15-dr.-jekyll.md*. The - sign is used as a space. Then you add the following content and save the file.

```
---
layout: post
title:  "The evil Dr. Jekyll!"
date:   2025-12-15
categories: jekyll website blogs
---
I really can’t stand Dr. Jekyll.
```
If you refresh your local website you should see a new post added to the list shown in the landing page. If you are not sure about when you will publish your post and just want to get started with a first draft you may create a **_drafts** folder and create your draft file there. In this case there is no need to put a date at the beginning of the file name. If you want to see your draft when you run Jekyll you have to start it using the *--drafts* option

```
$ bundle exec jekyll serve --drafts
```


### Pushing your website to GitHub
Once you are done with your local website you need to create a repository in you GitHub account. As said before the name of the repository must begin with your user name. For instance let's say your user name is *mrhyde*. In your GitHub account you click on the "New" button to open a page to create a new repository. You write *mrhyde.github.io* as the repository name and click on the "Create repository" button. At this point the repository is created and you can push your local website with a few git commands.

```
git remote add origin git@github.com:mrhyde/mrhyde.github.io.git
git branch -M main
git push -u origin main
```

Now you should be able to see your website on GitHub with the same pages and posts that you see locally using the URL *[https://mrhyde.github.io](https://mrhyde.github.io)*.

## Conclusion
There is a lot of information out there about how to build a website with Jekyll and GitHub pages, maybe too much with a lot of details that might be confusing. In this post I wrote a recipe with the ingredients that you have to ~~buy~~ install and what is their role in achieving the result. I hope it will also be useful to someone else. In one of the next post I will write about using other Jekyll themes, e.g. [Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/)
