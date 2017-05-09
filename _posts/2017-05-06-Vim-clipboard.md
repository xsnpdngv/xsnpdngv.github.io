---
title: Using the X11-clipboard in Vim
categories:
  - development
tags:
  - vim
  - linux
  - mac
---

Vim operations like copying, deleting, pasting by default use its own inner register
which is not related to the clipboard, so these operations cannot interwork
with the system's other applications. It is possible to relate this two
to each other, however doing so might not be straightforward and obvious.

The key concept here is to make Vim to use the X11-clipboard as the unnamed
register to which it saves what has been yanked (copied), deleted or changed, so
another program even other Vim instance can access it to be pasted.

# Vim Support

This can only be done, if Vim has support for `+clipboard` feature which can be
checked with

```bash
vim --version | grep clipboard
```
If there is a `-clipboard` (with the minus sign), then the feature is not supported.

On **Linux**, packages like `vim-gnome` and `vim-gtk` have this feature
included, so installing any of them should solve this issue e.g.,

```bash
sudo apt-get install vim-gnome
```

On **MacOS**, brewing Vim with the required option will help.

```bash
brew install vim --with-client-server
```
Remember to check if the newly installed version is executed for `vi` or `vim`.
If not, set `PATH` or add `alias` in profile accordingly.

# .vimrc

Set clipboard (if the feature is available) to the `unnamedplus` register as
seen below. See `:help clipboard` for more information.

```vimscript
if has('clipboard')
  set clipboard=unnamedplus
endif
```

# X11

Since the clipboard belongs to the windowing system, in case of remote
sessions it requires X11 forwarding, even for pure terminal use. Using ssh
with option -Y will do it. For example connecting to Linux running in the
Virtual Machine would look like below.

```bash
ssh -Y -p 2222 ubuntu@localhost
```

If **MacOS** is the host machine, so it runs the X server (`XQuartz`), then it
should have all options enabled under _Preferences_ > _Pasteboard_  to have the
feature surely work.

# Pitfalls

Without even assigning the unnamed register to the X11-clipboard, when X11 forwarding
is active, Vim connects to it. A slow remote connection might cause Vim to connect
to the clipboard slowly, so it results noticable delay in starting. In such annoying
cases it's better to make Vim not to connect to the clipboard.

```vimscript
set clipboard=exclude:.*
```
