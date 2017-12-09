---
title: C Strict Aliasing
excerpt: Meaning and dangers of aliasing in C and solutions to avoid related bugs.
categories:
  - development
tags:
  - c
  - pointer
  - aliasing
---

{% include toc %}

# Aliasing

Aliasing in C is about to refer to the same memory space as different types,
that is, accessing a variable's value through a pointer of another type.
Practically it means interpreting a variable's representation in memory as if it
was the represenation of a variable of a different type. In C it is quite
often.

```c
unsigned char uc = 65;  // ascii code of 'A'
char *cp = (char *)&uc; // alias to uc
printf("%c", *cp);      // prints 'A'
```

The example above shows this technique and there is no problem with it. An
`unsigned char` is interpreted as a `char` and is printed out.

The example below is OK too. If the actual value is changed through the lvalue
of either `*cp` or `uc`, since they are "compatible" in terms of aliasing, the
compiler will be aware of the change no matter through which lvalue was it
done.

```c
unsigned char uc = 65;  // ascii code of 'A'
char *cp = (char *)&uc; // alias to uc
uc++;                   // 'A' + 1 = 'B'
printf("%c", *cp);      // prints 'B'
```

The compiler will not suppose - even if it optimizies - that `*cp` remains
intact, while `uc` is changed. It will print 'B' correctly.

# Problem

If the same memory location is refferred through two incompatible types,
the compiler may think that the two lvalues are completely different,
they do not rely on each other, so if one is changed, the other will remain
intact.

```c
long   a  = 0x12345678;
short *bp = (short *)&a;
(*bp)++;
printf("%ld", a);
// may print either the decimal value equal to 0x12345678 or 0x12355678
// (assuming straight byte order and little endiannes for the sake of simplicity)
```

There are more problems with it. Referencing the address of `a` as if it would
be a short, covers only a part of its storage in memory. Assuming that long is
4 bytes and short is 2 (with straight byte order, little endian storage) the
memory looks as follows:

```
 +----+----+----+----+
 |0x12|0x34|0x56|0x78|   a long
 +----+----+----+----+
   ^    ^
   |    |
 +----+----+
 |0x12|0x34|  aliased as short
 +----+----+
```

It is obvious that on architectures with different endiannes it results
completely different value, so it is absolutely not portable.

Even bigger problem is that the compiler may think that the two lvalues `a` and
`*bp` are completely different variables, because of their incompatible types,
and assumes that if one is changed the other remains intact. It actually turns
into problem, when the compiler tries to optimize.

# Strict Aliasing

Strict aliasing is a concept on wich in certain circumstances the compiler can
rely on. It says, that even if two lvalues refer to the same memory location,
if their types are incompatible, there is no relation between them, they can
be handled absolutely independently. Relying on this rule/assumption the
compiler has an easier job on optimization.

Errors occur when compiler thinks this is the case and in real it is not.

# Solution

There are two possible solutions to avoid hard to find bugs resulting from
misunderstanding between the programmer and the compiler: Either making the
compiler be aware of that we are not that pedantic, so it's better not to rely
on such assumption, Or just always being type correct.

## No Strict Aliasing

The easier way is to let the compiler know that we ignore the Strict aliasing
rules, and make sure that it does not suppose that we are type correct. This
can be done by providing a flag to the compiler: `-fno-strict-aliasing`. Using
Makefile it should be added to `CFLAGS` and `CXXFLAGS` too.

This may be the best practice if dealing with legacy code. It secures
that the compiler will not make assumptions that it should not.

## Strict Aliasing Conformance

The prettier way is to be type correct and do not ever refer to the same memory
location as different incompatible types.

In terms of strict aliasing the following types are compatible:

- Those that differ only in signedness (`signed`/`unsigned`) or in qualifiers
  (e.g. `volatile`, `const`).

- A struct or union type can alias types contained inside them,
  e.g, an `int` can be aliased with a `union { int a; char b }`.

```c
typdef union {
    int  n;
    char c;
} union_t;

int      i  = 0x12345678;
union_t *up = (union_t *)&i;

i *= 2; // it will surely affect *up too

printf("%c", up->c);
```

- A character type (`char *`, `signed char *`, `unsigned char *`) can point
  to (alias) any memory location.

```c
int   a  = 0x12345678;
char *cp = (char *)&a;
printf("%c", *cp);
```
