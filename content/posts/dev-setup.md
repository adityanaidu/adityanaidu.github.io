+++ 
draft = false
date = 2025-08-19T17:00:59-04:00
title = "My Software Development Setup"
description = ""
slug = "dev-setup"
authors = ["Aditya Naidu"]
tags = ["development", "tools", "mac"]
categories = []
externalLink = ""
series = []
+++

The basic tools I use in my development setup and how to install and configure them. I believe they make me productive! This is on a Mac.

1. homebrew [install](https://brew.sh/) is a mac package manager. Its needed to install many other tools.
1. ctags [install](https://formulae.brew.sh/formula/ctags). It enables jumping to (method or function) definitions from the calling line and vice versa.
1. vim should already be installed. This is my editor of choice. Copy [vimrc](https://github.com/adityanaidu/util/blob/master/vimrc) to ~/.vimrc file
1. Install python linters like flake8/pylint. These are needed by ALE below
   ```bash
    pip install flake8
    pip install pylint
    ```
1. Install ALE (Asynchronous Lint Engine). It is a vim plugin that runs realtime syntax and sematic error checking while you edit files in vim. Under the covers it uses installed linters. The install section [here](https://github.com/dense-analysis/ale) is pretty straightforward. The vimrc file has some configuration to customize this plugin.
1. If you have a GitHub Copilot license you can integrate it with vim easily. Short and complete instructions are [here](https://github.com/github/copilot.vim)
1. Copy [bashrc](https://github.com/adityanaidu/util/blob/master/bashrc) to ~/.bashrc.
1. I also use Visual Studio Code for testing/debuging when eyeballing and print statements are not enough to figure things out!
