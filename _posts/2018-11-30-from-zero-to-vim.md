---
layout: post
title: From zero to vim
---

When I say _from zero_ I mean _from none configuration_, so I will not write
about how to use it or install it; I will leave that to you.

# ... but why?

I have been using ViM for a few years (8 aprox). But I am currently learning to
use some IDEs like [Intellij](https://www.jetbrains.com/idea/) and
[Visual Studio Code](https://code.visualstudio.com/) to expand my toolbox...
and always, I come back to ViM. Despite the simulator plugins available for
those IDEs, they do not fully simulates the behavior and that's quite a shame.

# What do I need

I have a non-fresh but unconfigured installation. I use ViM when I need to edit
text, so I will use it from programming to editing CSV or blog posts. Because
of it, my requirements are as follow:

**Visuals:**

* [Line numbers](#visuals)
* [Cursor position (row/col)](#visuals)
* Solarized color scheme.
* Nice status bar.

**Navigation:**

- File tree navigation.
- Search in files.

**Programming languages:**

* [Golang](#golang)
* Python
* Javascript

  - ES5/ES6
  - jQuery
  - reactJS

* Java

**Web:**

* HTML
* LESS
* SASS
* CSS3

**Documentation:**

* RestructuredText
* Markdown

**Other file support:**

* TODO

**Plugin source and management:**

* [VimAwesome](https://vimawesome.com/) as the plugin indexer.
* [Pathogen](#pathogen)

# Hands on!

This is what I have and see:

![raw vim](/assets/vim/raw-vim.png)

## Visuals

I create an empty `~/.vimrc` file to put the initial customizations.

```vim
" Visuals
set number  " show line numbers
set ruler  " cursor position
```
![visuals](/assets/vim/vim-visuals.png)

## Pathogen

I just follow the instructions described in its
[GitHub site](https://github.com/tpope/vim-pathogen) and you're done: this
creates the directories in `~/.vim/autoload` and `~/.vim/bundle`. All future
plugins will be placed on `~/.vim/bundle`. This is required on ~/.vimrc to
make it work:

```vim
" Pathogen configuration
execute pathogen#infect()
syntax on
filetype plugin indent on
```

Now I am ready to put some plugins in.

## Golang

[faith/vim-go](https://github.com/fatih/vim-go) is the mainstream plugin for
Golang. Since I have installed Pathogen for plugin management, I just need to
clone the project on its bundle directory:

```bash
$ git clone https://github.com/fatih/vim-go.git ~/.vim/bundle/vim-go
```

When I execute the command `vim a.go`, I see the following:

![vim-go](/assets/vim/vim-go.png)

It is because the vim-go plugin is placing some code snippet into our empty 
file, proof of correct installation. Now I can run some commands like
`:GoBuild`, `:GoInstall` or `:GoRun`. Have fun.

# More to come

And this is all for now too. I will keep posting on my progress.