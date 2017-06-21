---
title: Profiling with Callgrind
categories:
  - development
tags:
  - profiling
  - linux
  - ubuntu
---

Callgrind is a profiling tool that can be used via the
[Valgrind](http://valgrind.org/)
framework and its report can be displayed/inspected with the
[KCachegrind](https://kcachegrind.github.io/)
profile data visualization tool.

When you use Callgrind to profile a program, the program is transformed into an
intermediate format and then is run in a virtual processor emulated by valgrind.
That makes possible to keep track of operations and that makes the program
run much more slower than otherwise.

Install
-------

```bash
sudo apt-get install valgrind kcachegrind graphviz
```

Prepare
-------

Not necessarily required but useful to have the debugging information in the binary
so if it's possible compile with the -g option.

```bash
gcc -g -o program program.c
```

Profile
-------

```bash
# check options
man valgrind

# profile
valgrind --tool=callgrind <program> [program-options]

# profile with additional options e.g.
valgrind --tool=callgrind --dump-instr=yes --simulate-cache=yes --collect-jumps=yes <program> [program-options]
```

For more details on Callgrind's features and way of working, it's worth checking out its
[site](http://valgrind.org/docs/manual/cl-manual.html).

Inspect
-------

KCahcegrind is a graphical program and its interface is quite self-explanatory.
If it is run from the directory where Callgrind just stored its reports into, it opens
those by default.

```bash
kcachegrind &
```

