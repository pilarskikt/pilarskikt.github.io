---
title: Clean Neovim IDE setup
description: How I set up my Neovim for everyday work with code
date: 2025-01-01
tags: [shell, terminal, IDE, neovim, devops]
categories: [Neovim]
---

During the day I spend around 70% of my time in the terminal, and it's quite important for me to keep my workflow intuitive and fluid. It's a no-brainer that I want most of my tools available from the terminal—and the editor is one of the most important. That's where Neovim comes in and shines :-). After trying lots of Neovim setups (nvChad, LazyVim, LunarVim, AstroVim) I decided to create my own config to avoid a bunch of plugins and configs I don't actually use. Here is how I did my setup.

First of all, let's talk about my Neovim config structure. I would say it's fairly standard:

```shell
.
├── init.lua
├── lazy-lock.json
└── lua
    ├── config
    │   ├── init.lua
    │   ├── keymaps.lua
    │   ├── lazy.lua
    │   ├── lsp.lua
    │   ├── options.lua
    │   └── statusline.lua
    └── plugins
        ├── blink.lua
        ├── colorscheme.lua
        ├── conform.lua
        ├── lsp.lua
        ├── mini.lua
        ├── nvim-tree.lua
        ├── treesitter.lua
        └── whichkey.lua
```
