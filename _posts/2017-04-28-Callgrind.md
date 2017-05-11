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
valgrind --tool=callgrind <program> [program-options]

# or even
valgrind --tool=callgrind --dump-instr=yes --simulate-cache=yes --collect-jumps=yes <program> [program-options]
```

NOTE: This way the program runs much slower...

Inspect
-------

```bash
kcachegrind &
```
