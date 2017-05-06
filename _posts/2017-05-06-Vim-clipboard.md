---
title: Using the clipboard in Vim
categories:
  - development
tags:
  - vim
  - linux
  - mac
---

Using the clipboard under Vim is not straightforward and obvious.
The key concept here is to make Vim to use the clipboard as the unnamed
register to which it saves what has been yanked (copied), deleted or changed, so
another program even other Vim instance can access it to be put (pasted).

# Vim Support

This can only be done, if Vim has support for `+clipboard` feature which can be
checked with `vim --version`. If there is a `-clipboard`, then this feature is
not supported.

On Linux packages like vim-gnome, vim-athena and vim-gtx have this feature, so
installing any of them should solve this issue.

```bash
sudo apt-get install vim-gnome
```

On MacOS brewing Vim with the required option will help.

```bash
brew install vim --with-client-server
```
Remember to check if for `vi` or `vim` the newly installed version is
executed. If not, set `PATH` or add `alias` accordingly.

# .vimrc

Set clipboard (if the feature is available) to unnamedplus as below. See
`:help clipboard` for more information.

```vimscript
if has('clipboard')
  set clipboard=unnamedplus
endif
```

# X11

Since the clipboard belongs to the windowing system, in case of remote
sessions it requires X11 forwarding even for pure terminal use. Using ssh
with option -Y will do it.

```bash
ssh -Y -p 2222 ubuntu@localhost
```

If MacOS runs the X server (`XQuartz`), then it should have all Pasteboard
options to be enabled under Preferences to have the feature work flawlessly.
