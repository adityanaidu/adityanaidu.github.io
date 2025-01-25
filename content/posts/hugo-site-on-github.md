+++ 
draft = true
date = 2025-01-25T10:35:48-05:00
title = "Create a Hugo Site on Github"
description = ""
slug = "hugo-site-on-github"
authors = ["Aditya Naidu"]
tags = ["hugo", "github", "website"]
categories = []
externalLink = ""
series = []
+++

# Introduction
This article explains how to create a static site using hugo and host it on Github. If you don't have a github.io repo, create one first. Check my article [here]({{< ref "/posts/custom-domain-github" >}}) for some help.

I tried [jekyll](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/adding-content-to-your-github-pages-site-using-jekyll#about-content-in-jekyll-sites) mentioned on github pages documentation itself. But I found it cumbersome and didn't want to work with a completely new (for me) tool chain to get my website going. I found hugo to be much easier and quick to get going with minimal install and setup. Their documentation is short and crisp.

# Hugo Quick Start
The instructions on Hugo [here](https://gohugo.io/getting-started/quick-start/) are short and self explainatory. Once you have a simple post ready you need to commit and push your work to github. Note that you can git add and commit the themes directory. This will not commit each file in that directory but just a link to the theme repo. Also, make sure that the `public/` and `resources/` are added to .gitignore and not commited to the repo. Long story short, only your raw content in `contents/` (namely the markdown files) and the site configurations need to go to the github repo.

# Github Actions
Now that all the raw content and site config is in the repo we need to create the final product (html, javascript, etc) that will be deployed to your website.

This will be done for us by Github actions. In your repo `<username>.github.io` create a file `.github/workflows/hugo.yaml`. You can copy paste my file (`https://github.com/adityanaidu/adityanaidu.github.io/blob/main/.github/workflows/hugo.yaml`) into your repo. Now go to `<username>.github.io` > Settings > Pages > Build and deployment > Source. Change Source to GitHub Actions. Whenever a commit is pushed to the repo, this will start building and then deploying your website.
