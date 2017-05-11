---
title: Using the X11-clipboard in Vim
categories:
  - development
tags:
  - vim
  - linux
  - mac
---

Vim operations like copying (`y`), deleting (`x`, `X`, `D`, `dd`), pasting (`p`, `P`)
by default use their own inner register which is not related to the clipboard, so these
operations cannot interwork neither with the system's other applications nor with Vim's
other instances. Though, it is possible to relate this two to each other, 
doing so might not be straightforward and obvious.

{% include toc %}

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

## Linux

On Linux, packages like `vim-gnome` and `vim-gtk` have this feature
included, so installing any of them should solve this issue e.g.,

```bash
sudo apt-get install vim-gnome
```

## MacOS

On MacOS, there are two ways to make the option available and working.

### Add Vim the option

Brewing Vim with `--with-client-server` option will let the feature fairly work, but
requires `XQuartz` running because it needs the X11 forwarding protocol to access the
clipboard. If it is not running when opening Vim for the first time, it will be
started automatically but it takes a while. Moreover, the solution is not complete.
Though, the copy-paste between instances of Vim works fine, interaction with OS-X's
native applications will work only after starting an `XQuartz` application.

```bash
# install Vim with +clipboard support
brew install vim --with-client-server
```
After installing, remember to check if the new version is executed
`which vi`, `which vim`. If not, set `PATH` or add `alias` in profile accordingly.

### Use MacVim

Brewing MacVim in a way to override system Vim will give the best result. This will not
require `XQuartz` for any reason, and is able to access the system clipboard
in a native way, without X11 (which is not used by default in MacOS).
  
```bash
# install MacVim to support clipboard in a native way
brew install macvim --with-override-system-vim
```

Installing MacVim this way is intended to substitute the system Vim, so probably
this will be the default for `vi` or `vim` after it's done.

# .vimrc

If the clipboard is available, then assign to it the `unnamed` and `unnamedplus`
registers as seen below. Ddepending on the system, it varies which is the better,
so having both set is a good approach. Adding `autoselect` also to the clipboard
makes visual Vim selections automatically be available to paste, without the
need to copy/delete them. See `:help clipboard` for more information.

```vimscript
if has('clipboard')
  set clipboard=unnamed,unnamedplus,autoselect
endif
```

# X11 Forwarding

Since the clipboard belongs to the windowing system, in case of remote
sessions it requires X11 forwarding, even for pure terminal use. Using ssh
with option -Y will do it. For example connecting to a Linux running in the
virtual machine would look like below.

```bash
ssh -Y -p 2222 ubuntu@localhost
```

In order to make this kind of connection acceptable for a **Vagrant** virtual
machine, add the following to the `Vagrantfile`.

```ruby
# If true, X11 forwarding over SSH connections is enabled. Defaults to false.
config.ssh.forward_x11 = true
```

If **MacOS** is the host machine, so it runs the X server (`XQuartz`), then it
should have all options enabled under _Preferences_ > _Pasteboard_  to have the
feature surely work.

# Downside

Without even assigning the unnamed register to the X11-clipboard, when X11 forwarding
is active, Vim connects to it (except for MacVim). A slow remote connection might cause
Vim to connect to the clipboard slowly, so it results noticable delay in starting.
In such annoying cases it might be better to make Vim not to connect to the clipboard.

```vimscript
set clipboard=exclude:.*
```
