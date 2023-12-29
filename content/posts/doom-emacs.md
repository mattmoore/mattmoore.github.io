---
title: "Install Doom Emacs on Apple Silicon Mac"
summary: "Install Doom Emacs on Apple Silicon Mac"
date: 2023-12-29T10:42:42-05:00
draft: false
---

## Install [d12frosted/emacs-plus](d12frosted/emacs-plus)

```shell
brew tap d12frosted/emacs-plus
brew install emacs-plus --with-native-comp --with-modern-black-dragon-icon
```

Wait for it to finish installing (may take a while since it's compiling from source via `--with-native-comp`).

After it's done, link the app with either:

```shell
ln -s /opt/homebrew/opt/emacs-plus@29/Emacs.app /Applications
```

or

```shell
osascript -e 'tell application "Finder" to make alias file to posix file "/opt/homebrew/opt/emacs-plus@29/Emacs.app" at POSIX file "/Applications"'
```

## Install [doomemacs/doomemacs](doomemacs/doomemacs)

```shell
git clone --depth 1 https://github.com/doomemacs/doomemacs ~/.config/emacs
~/.config/emacs/bin/doom install
```

...which takes a while. Confirm yes to any prompts.


You should now be able to launch Doom Emacs from your Mac applications - it's named "Emacs.app"
