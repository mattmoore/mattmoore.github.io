---
title: "Neovim Lua Mappings"
summary: "My neovim Lua mappings."
date: 2024-02-04T17:15:23-05:00
draft: false
---

```shell
~/.config/nvim/init.lua
```

```lua
vim.g.mapleader = " "
vim.opt.number = true
vim.opt.autoindent = true
vim.opt.tabstop = 2
vim.opt.shiftwidth = 2
vim.opt.smarttab = true
vim.opt.softtabstop = 2
vim.opt.expandtab = true
vim.opt.mouse = 'a'
vim.opt.termguicolors = true
vim.keymap.set('v', '<', '<gv', { remap = false })
vim.keymap.set('v', '>', '>gv', { remap = false })
```

In particular, to use `>` to indent and `<` to unindent while in visual select mode, without having to reselect the text, so that in visual select mode, you can tap `>` or `<` to shift right or left repeatedly, respectively:

```lua
vim.keymap.set('v', '<', '<gv', { remap = false })
vim.keymap.set('v', '>', '>gv', { remap = false })
```

For posterity, this used to be done in older Vim with this:

```vim
vnoremap < <gv
vnoremap > >gv
```
