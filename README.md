# C PROGRAMMING HANDBOOK
### From Zero to Expert: A Complete Reference for Systems Programmers

*A comprehensive handbook covering every aspect of the C programming language — from first principles through compiler internals, security, performance, and production systems programming.*

---

> **How to use this book:** Each chapter builds on prior ones. Beginners should read linearly. Professionals can navigate by section. Every major concept follows the same teaching arc: *What → Why → Intuition → How it works → Syntax → Examples → Memory model → Compiler view → Performance → Pitfalls → Best practices.*

---

# TABLE OF CONTENTS

- Part I: Foundations (Chapters 1–6)
- Part II: Memory and Pointers (Chapters 7–10)
- Part III: Compound Types (Chapter 11)
- Part IV: Scope, Linkage, and Modular Design (Chapters 12–15)
- Part V: The Standard Library (Chapters 16–19)
- Part VI: Error Handling (Chapter 20)
- Part VII: Undefined Behavior (Chapter 21)
- Part VIII: Security (Chapter 22)
- Part IX: Performance (Chapter 23)
- Part X: Concurrency (Chapter 24)
- Part XI: Systems Programming (Chapters 25–27)
- Part XII: Data Structures (Chapters 28–29)
- Part XIII: Tooling (Chapters 30–31)
- Part XIV: Advanced Topics (Chapters 32–34)
- Part XV: Software Engineering in C (Chapter 35)
- Appendices A–J

---


---

# PART I — FOUNDATIONS

*Theme: From zero to a working mental model of C programs. You will learn what C is, how a program becomes a process, how the compiler thinks, and how to write, build, and reason about basic C programs.*

---

# Chapter 1 — The World of C

## 1.1 What Is C?

C is a general-purpose, procedural programming language designed to give the programmer direct, efficient control over the hardware with minimal abstraction overhead. It was designed not to hide the machine — it was designed to map cleanly onto it.

A precise one-sentence definition: **C is a portable, statically typed, procedural language in which the programmer manages memory manually, expressions map predictably to machine operations, and the behavior of a program is defined relative to an abstract machine specified by the ISO standard.**

Every word in that definition matters:

- *Portable*: A C program written against the standard can be compiled for different CPUs and operating systems. Portability is not automatic — it requires discipline — but it is achievable.
- *Statically typed*: Every expression has a type known at compile time. The type governs what operations are valid.
- *Procedural*: Programs are organized as sequences of statements grouped into functions. There is no built-in concept of objects or classes.
- *Manual memory management*: You decide when to allocate memory and when to release it. The language will not do it for you.
- *Abstract machine*: The C standard defines behavior relative to an imaginary computer. Real compilers translate that to real hardware.

## 1.2 History and Design Philosophy

C was created by Dennis Ritchie at Bell Labs between 1969 and 1973, growing out of an earlier language called B (itself derived from BCPL). It was designed as the implementation language for the Unix operating system. This origin is everything: Unix needed to be portable across hardware, efficient enough to replace assembly for an OS, and flexible enough to express system-level operations like direct memory access and I/O control.

The design philosophy can be stated as a set of priorities:

1. **Trust the programmer.** C assumes you know what you are doing. It will not insert bounds checks, null checks, or type-safety guards unless you write them.
2. **Don't pay for what you don't use.** Features that cost at runtime — garbage collection, bounds checking, exception handling — are absent. You add overhead only when you choose to.
3. **Stay close to the hardware.** Integers, pointers, arrays, and bitwise operations map directly to hardware concepts. What you write is approximately what the CPU executes.
4. **Remain portable.** Despite mapping to hardware, C programs can be recompiled for new architectures without modification — when written carefully.

These four priorities create the nature of C: powerful, efficient, and unforgiving. The same properties that make C excellent for systems programming make it dangerous for careless use.

## 1.3 Why C Still Matters

The following software is written in C or relies critically on C:

- The Linux kernel, FreeBSD, and most UNIX operating systems
- The CPython interpreter (Python's reference implementation)
- The SQLite database (one of the most widely deployed software components on Earth)
- OpenSSL, libc, glibc, musl
- GCC and LLVM (the compilers used for most other languages)
- Redis, Nginx, PostgreSQL
- Every embedded system from aircraft control to microwave ovens

C is not the right tool for every job. But it is the language that underlies the rest of computing. Understanding it deeply makes you a better programmer in every language.

## 1.4 What C Is NOT

- C is not C++. C++ grew from C but added classes, templates, RAII, exceptions, and far more. A C compiler will reject most C++ code and vice versa.
- C is not assembly. C provides a portable abstraction over hardware. Assembly code is hardware-specific and requires no compiler.
- C is not "low-level" in the sense of being simple. Its semantics, especially around undefined behavior, are subtle and require careful study.
- C is not a safe language. It will let you corrupt memory, read uninitialized bytes, and invoke undefined behavior without complaint.

## 1.5 The C Standards Timeline

| Standard | Year | Key Additions |
|---|---|---|
| K&R C | 1978 | Original book definition |
| C89 / ANSI C | 1989 | First formal standard |
| C90 | 1990 | ISO adopted C89 with minor changes |
| C99 | 1999 | `//` comments, VLAs, `stdint.h`, designated initializers, `restrict`, `_Bool` |
| C11 | 2011 | `_Generic`, `_Atomic`, `_Static_assert`, threads, anonymous structs/unions |
| C17 | 2018 | Bug fixes to C11, no major new features |
| C23 | 2023 | `typeof`, `nullptr`, `_BitInt`, `#embed`, `[[attributes]]`, `<stdbit.h>` |

Unless otherwise noted, this book targets C11 and C17, with C23 features covered in Chapter 34. Code examples are compatible with GCC 9+ and Clang 9+.

## 1.6 How a C Program Becomes a Running Process

Here is the high-level arc from source code to execution. Each step is detailed in Chapter 2.

```
Source file (.c)
      │
      ▼ Preprocessor (cpp)
Preprocessed source (.i)
      │
      ▼ Compiler front end (parsing, semantic analysis)
Intermediate representation
      │
      ▼ Compiler back end (optimization, code generation)
Assembly (.s)
      │
      ▼ Assembler (as)
Object file (.o)
      │
      ▼ Linker (ld) ◄─── Libraries (.a, .so)
Executable (ELF, PE, Mach-O)
      │
      ▼ OS loader
Running process
```

## 1.7 Your First C Program — Understood Deeply

```c
#include <stdio.h>

int main(void) {
    printf("Hello, world\n");
    return 0;
}
```

Let us read this not as a beginner's greeting but as a precise technical statement:

- `#include <stdio.h>` is a preprocessor directive. It instructs the preprocessor to insert the contents of the standard I/O header before the compiler sees this file. This header provides the *declaration* of `printf`, telling the compiler what arguments it takes and what it returns.
- `int main(void)` declares the entry point of the program. `int` is the return type; `main` is the name the OS loader calls; `void` states that this function takes no parameters. (The alternate form `int main(int argc, char *argv[])` accepts command-line arguments.)
- `printf("Hello, world\n")` calls the standard library function that formats and writes text to standard output. The `\n` is an escape sequence representing a newline character.
- `return 0` returns the value zero to the operating system. By convention, zero indicates success; any nonzero value indicates failure.

Every character here is deliberate. C has no optional syntax sugar. What you write is what you mean.

## 1.8 The Five Memory Regions — First Look

Every running C program has its memory organized into regions. We will study these in depth in Chapter 7. For now, understand that they exist:

```
High addresses
┌─────────────────┐
│   Stack         │  ← Local variables, function parameters, return addresses
├─────────────────┤
│   ↓ grows down  │
│                 │
│   ↑ grows up    │
├─────────────────┤
│   Heap          │  ← Dynamically allocated memory (malloc/free)
├─────────────────┤
│   BSS segment   │  ← Uninitialized global and static variables (zeroed)
├─────────────────┤
│   Data segment  │  ← Initialized global and static variables
├─────────────────┤
│   Text segment  │  ← Program code (read-only)
└─────────────────┘
Low addresses
```

This layout is the mental map you need for everything that follows.

---

## Summary

C is a portable, efficient, procedural language designed to give programmers direct control over hardware without hiding it. It was created to write Unix and remains the foundation of modern systems software. Understanding C means understanding the abstract machine it defines, the memory model it relies on, and the undefined behaviors it allows.

## Key Takeaways

- C trusts the programmer and does not add runtime safety overhead.
- A C program passes through preprocessing, compilation, assembly, and linking before execution.
- Five memory regions organize every running process: text, data, BSS, heap, stack.
- C standards evolved from K&R (1978) through C23 (2023).

## Common Pitfalls

- Confusing C with C++. They share syntax but have different semantics and different standard libraries.
- Assuming C is "just assembly with nicer syntax." C has a complex abstract machine with subtle rules.
- Thinking `return 0` from `main` is optional. In C89 it is required; in C99+ omitting it returns 0 implicitly from `main` only — not from other functions.

## Exercises

**Easy:** Write a program that prints your name and the current year. Compile it with `gcc -Wall -Wextra -std=c11 -o hello hello.c` and run it.

**Medium:** Modify the program to print the sizes of `int`, `long`, `long long`, `float`, `double`, and `void *` using `printf` and `sizeof`. What does this tell you about the machine you are running on?

**Hard:** Without running it, explain exactly what happens — at each stage of the compilation pipeline — when you compile and run the hello world program. What does the preprocessor produce? What does the linker do with `printf`?

---

# Chapter 2 — The Compilation Pipeline

## 2.1 The Four Stages

When you invoke `gcc hello.c -o hello`, four distinct programs run in sequence. Most programmers treat this as a black box. That is a mistake. Every debugging session, every linker error, every subtle performance difference traces back to one of these four stages.

The four stages are:

1. **Preprocessing** — text transformation
2. **Compilation** — translation to assembly
3. **Assembly** — translation to machine code
4. **Linking** — combining object files into an executable

You can observe each stage explicitly:

```bash
gcc -E hello.c -o hello.i      # Stop after preprocessing
gcc -S hello.i -o hello.s      # Stop after compilation (produces assembly)
gcc -c hello.s -o hello.o      # Stop after assembly (produces object file)
gcc hello.o -o hello            # Link only
```

## 2.2 Stage 1: The Preprocessor

The preprocessor is a *text processor*. It knows nothing about C syntax, types, or semantics. It operates purely on tokens and text.

What the preprocessor does:

- Processes `#include` directives: replaces them with the contents of the named file
- Processes `#define` directives: replaces subsequent occurrences of defined names with their expansions
- Processes conditional compilation directives: `#if`, `#ifdef`, `#ifndef`, `#elif`, `#else`, `#endif`
- Strips comments
- Performs line splicing (lines ending in `\` are joined with the next line)
- Produces `#line` markers so the compiler can report errors with original source locations

To see the preprocessed output:

```bash
gcc -E hello.c
```

The output of `#include <stdio.h>` alone is typically thousands of lines — all the declarations from the standard I/O header and everything it includes.

**Critical insight:** The preprocessor is completely separate from the C language. A syntactically invalid C program can still pass through the preprocessor. A preprocessor macro can generate syntactically invalid C. The preprocessor runs first, blind to what the C compiler will later do with its output.

## 2.3 Stage 2: Compilation

The compiler front end reads the preprocessed source and:

1. **Tokenizes** (lexical analysis): converts the character stream into tokens — keywords, identifiers, literals, operators, punctuation.
2. **Parses** (syntax analysis): builds an Abstract Syntax Tree (AST) from the token stream, verifying grammar.
3. **Performs semantic analysis**: checks types, resolves identifiers, validates operations.
4. **Generates an intermediate representation** (IR): GCC uses GIMPLE and RTL; Clang uses LLVM IR.

The optimizer then works on the IR, performing transformations that preserve meaning while improving performance. Finally, the back end generates architecture-specific assembly code.

The compiler sees *one translation unit at a time*. A translation unit is a single `.c` file after preprocessing. The compiler does not see other `.c` files. This is fundamental: it explains why declarations in header files are necessary (the compiler needs to know the types involved in function calls), and it explains why certain optimizations are limited until link time.

## 2.4 Stage 3: Assembly

The assembler converts the assembly text file (`.s`) into an object file (`.o`). The object file contains:

- Machine code (binary instructions for the target CPU)
- A symbol table (listing defined and referenced symbols)
- Relocation entries (locations that need to be patched when the final addresses are known)
- Debugging information (if compiled with `-g`)
- Section data (code, initialized data, uninitialized data, read-only data)

Object files do not contain complete programs. References to functions or variables defined in other translation units are left as *unresolved symbols*, marked with relocation entries for the linker.

## 2.5 Stage 4: Linking

The linker:

1. Takes all object files and libraries specified
2. Resolves every symbol reference to a symbol definition
3. Patches addresses (relocation)
4. Produces the final executable

If a symbol is referenced but never defined anywhere in the inputs, the linker produces an "undefined reference" error. If a symbol is defined in multiple object files with external linkage, the linker produces a "multiple definition" error.

**Static libraries** (`.a` on Unix, `.lib` on Windows) are archives of object files. The linker extracts only the object files that define needed symbols.

**Dynamic libraries** (`.so` on Linux, `.dylib` on macOS, `.dll` on Windows) are linked at runtime by the dynamic linker/loader. The executable records which dynamic libraries it needs and which symbols it expects from them.

```
Static link:        .c → .o → executable (all code inside)
Dynamic link:       .c → .o → executable + .so (loaded at runtime)
```

Static linking produces larger, self-contained executables. Dynamic linking produces smaller executables but requires the library to be present at runtime.

## 2.6 Translation Units

A *translation unit* is the fundamental unit of compilation in C. It consists of a single source file plus all the files it includes (directly or transitively) via `#include`.

Each translation unit is compiled independently. This has consequences:

- The compiler cannot optimize across translation units (unless LTO is enabled — see Chapter 33).
- A function defined in one translation unit is invisible to the compiler of another — only its *declaration* (from a header) is visible.
- `static` variables and functions are *local to their translation unit* — they cannot be referenced from another translation unit.

Understanding translation units is essential for understanding linkage (Chapter 12), header design (Chapter 14), and link-time errors.

## 2.7 GCC and Clang: The Two Dominant Compilers

**GCC (GNU Compiler Collection):**
- Older, more mature
- Excellent support for obscure platforms and architectures
- Warning messages can be cryptic
- Extensions are GCC-specific (though Clang supports most)

**Clang (LLVM-based):**
- Cleaner, more informative error messages
- Better static analysis integration
- LibTooling enables source-level tooling
- Now the default on macOS and many BSD systems

For this book, code compiles correctly on both. Platform-specific behavior is noted explicitly.

## 2.8 Essential Compiler Flags

These flags should be used for every project:

```bash
gcc -std=c11          # Use C11 standard
    -Wall             # Enable most common warnings
    -Wextra           # Enable additional warnings
    -Wpedantic        # Reject non-standard extensions
    -Werror           # Treat warnings as errors (use in CI)
    -g                # Include debug symbols
    -O0               # No optimization (for debugging)
    -O2               # Standard optimization (for production)
    -fsanitize=address,undefined  # Enable sanitizers (development)
```

Example for a development build:

```bash
gcc -std=c11 -Wall -Wextra -Wpedantic -g -O0 \
    -fsanitize=address,undefined \
    -o myprogram myprogram.c
```

Example for a production build:

```bash
gcc -std=c11 -Wall -Wextra -O2 -DNDEBUG \
    -o myprogram myprogram.c
```

## 2.9 Understanding Error Messages

A compilation error tells you: the file name, the line number, the column, and a description. Reading them carefully solves most problems immediately.

```
hello.c:5:5: error: implicit declaration of function 'prnit'
     prnit("Hello\n");
     ^~~~~
```

This tells you: file `hello.c`, line 5, column 5. The identifier `prnit` was used but not declared. (It's a typo of `printf` — the function is called `printf`, and it is declared in `<stdio.h>`, which you must include.)

Warning messages follow the same format. Never ignore warnings. In a correctly written C program, there are no warnings.

## 2.10 Build Systems

For projects with more than one file, you need a build system. The minimal standard is **Make**.

A Makefile:

```makefile
CC = gcc
CFLAGS = -std=c11 -Wall -Wextra -g

myprogram: main.o util.o
	$(CC) $(CFLAGS) -o myprogram main.o util.o

main.o: main.c util.h
	$(CC) $(CFLAGS) -c main.c

util.o: util.c util.h
	$(CC) $(CFLAGS) -c util.c

clean:
	rm -f *.o myprogram
```

The indentation before each command must be a **tab**, not spaces. Make is sensitive to this.

CMake is the more modern choice for larger projects and is covered in Chapter 31.

---

## Summary

A C program passes through four distinct stages before executing: preprocessing (text transformation), compilation (C to assembly via IR), assembly (assembly to machine code), and linking (combining objects into an executable). Each stage has distinct failure modes. The compiler sees one translation unit at a time; the linker combines them. Always compile with `-Wall -Wextra` and understand every warning your compiler produces.

## Key Takeaways

- The preprocessor is a text processor that knows no C semantics.
- Each `.c` file is a separate translation unit, compiled independently.
- The linker resolves symbol references across translation units.
- Static libraries embed code in the executable; dynamic libraries load at runtime.
- Use `-Wall -Wextra -std=c11` on every compilation.

## Common Pitfalls

- Forgetting to include a header, causing "implicit declaration" warnings (and undefined behavior in C99+).
- Using `==` to compare strings (compares pointers, not content).
- Forgetting that Make requires tabs, not spaces.
- Linking against a library but forgetting the `-l` flag.

## Exercises

**Easy:** Create a two-file C project: `main.c` calls a function defined in `util.c`. Write a Makefile that compiles both and links them. Verify that `make` only recompiles what changed.

**Medium:** Run `gcc -E` on a file that includes `<stdio.h>`. How many lines does the preprocessed output contain? Find where `printf` is declared in the output.

**Hard:** Using `objdump -t` on an object file, locate the symbol table. Identify which symbols are defined (marked `T` for text section) and which are undefined (marked `U`). Explain what happens to the undefined symbols during linking.

---

# Chapter 3 — Types, Values, and Variables

## 3.1 The Type System Philosophy

In C, *every expression has a type*. The type determines:

- How many bytes the object occupies in memory
- How those bytes are interpreted (as a signed integer, unsigned integer, floating-point number, address, etc.)
- Which operations are valid
- What happens during conversion to other types

C's type system is *static* (types are known at compile time) and *weak* (implicit conversions between types are pervasive, and some are dangerous). The programmer is responsible for understanding how types interact.

## 3.2 The Value vs. Object Distinction

This distinction is subtle but important.

An **object** is a region of storage in memory. It has an address, a size, and a type.

A **value** is the abstract mathematical meaning that the bits in an object represent according to the object's type.

```c
int x = 42;
```

Here, `x` names an object: a region of memory, typically 4 bytes, at some address (say, `0x7fff5000`). The value of that object — the integer 42 — is the interpretation of those 4 bytes as a two's-complement signed integer.

An *lvalue* is an expression that refers to an object (has a location). An *rvalue* is an expression that produces a value (does not necessarily refer to a persistent location). The names come from their typical positions relative to the `=` operator.

## 3.3 Integer Types

C defines the following integer types, from smallest to largest:

| Type | Minimum size | Typical size (LP64) | Range (signed) |
|---|---|---|---|
| `char` | 8 bits | 8 bits | platform-defined |
| `short` | 16 bits | 16 bits | -32,768 to 32,767 |
| `int` | 16 bits | 32 bits | -2,147,483,648 to 2,147,483,647 |
| `long` | 32 bits | 64 bits (Linux/macOS) | varies |
| `long long` | 64 bits | 64 bits | -9.2×10¹⁸ to 9.2×10¹⁸ |

The C standard guarantees *minimum* sizes, not exact sizes. The only guarantee about relationships is: `sizeof(char) <= sizeof(short) <= sizeof(int) <= sizeof(long) <= sizeof(long long)`.

**The `int` guarantee:** The standard guarantees that `int` is at least 16 bits. In practice, on every modern platform, `int` is 32 bits. But code that assumes `int` is 32 bits is technically non-portable.

## 3.4 Signed vs. Unsigned

Every integer type except `char` comes in signed and unsigned variants:

```c
signed int   n = -42;     /* can hold negative values */
unsigned int u = 42;      /* cannot hold negative values; doubles positive range */
```

Signed integers use two's complement representation on all modern platforms (and this is now required by C23). Two's complement stores the value by flipping all bits and adding one for negative numbers:

```
 42  = 0000 0000 0000 0000 0000 0000 0010 1010  (32-bit)
-42  = 1111 1111 1111 1111 1111 1111 1101 0110  (two's complement)
```

Unsigned integers use pure binary. They wrap around on overflow: adding 1 to `UINT_MAX` gives 0. This wrap-around is *defined* behavior for unsigned integers.

**Critical:** Overflow of a *signed* integer is *undefined behavior* in C. This is not a technicality — compilers actively exploit this for optimization. See Chapter 21.

## 3.5 Fixed-Width Integer Types

Because the sizes of `int`, `long`, etc., vary by platform, the `<stdint.h>` header (C99+) provides types with guaranteed sizes:

```c
#include <stdint.h>

int8_t    i8;   /* exactly 8 bits, signed  */
uint8_t   u8;   /* exactly 8 bits, unsigned */
int16_t   i16;  /* exactly 16 bits, signed  */
uint16_t  u16;
int32_t   i32;  /* exactly 32 bits, signed  */
uint32_t  u32;
int64_t   i64;  /* exactly 64 bits, signed  */
uint64_t  u64;

/* Fastest types of at least N bits: */
int_fast32_t   fast32;   /* fastest signed type >= 32 bits */
uint_fast32_t  ufast32;

/* Smallest types of at least N bits: */
int_least32_t  least32;  /* smallest signed type >= 32 bits */

/* Pointer-sized integer: */
intptr_t   iptr;   /* signed integer that can hold a void * */
uintptr_t  uptr;   /* unsigned integer that can hold a void * */

/* Maximum-width integer: */
intmax_t   imax;
uintmax_t  umax;
```

**Rule:** When the exact size of an integer matters (protocols, file formats, hardware registers), use `stdint.h` types. When you just need "a reasonable integer," use `int`.

## 3.6 Floating-Point Types

C provides three floating-point types:

| Type | Size | Precision | Range (approx) |
|---|---|---|---|
| `float` | 32 bits | ~7 decimal digits | ±3.4×10³⁸ |
| `double` | 64 bits | ~15 decimal digits | ±1.8×10³⁰⁸ |
| `long double` | 80–128 bits | platform-dependent | varies |

All three types follow IEEE 754 (on all modern platforms, though the standard does not mandate this).

**Default for floating point:** Use `double` unless you have a specific reason for `float` (memory constraints, SIMD operations). `float` is not faster than `double` on most modern CPUs.

## 3.7 IEEE 754 Essentials

IEEE 754 defines how floating-point numbers are stored and how arithmetic works. Key facts:

- Most numbers cannot be represented exactly. `0.1` in `double` is approximately `0.10000000000000000555...`
- Never test floating-point values for exact equality. Use `fabs(a - b) < epsilon`.
- Three special values exist: `+Infinity`, `-Infinity`, and `NaN` (Not a Number).
- `0.0 / 0.0` produces `NaN`. `1.0 / 0.0` produces `+Infinity`.
- `NaN` is not equal to anything, including itself: `nan != nan` is true.

```c
#include <math.h>
#include <float.h>

double a = 0.1 + 0.2;
double b = 0.3;

/* Wrong: */
if (a == b) { ... }              /* May not trigger! */

/* Correct: */
if (fabs(a - b) < 1e-9) { ... } /* Compare with tolerance */
```

## 3.8 The `char` Type

`char` is the fundamental character type. It is exactly one byte — the only type the standard guarantees is exactly 1 byte (`sizeof(char) == 1` by definition).

The signedness of plain `char` is implementation-defined. On most x86 Linux systems, `char` is signed. On ARM, it is often unsigned. This matters when:

- Storing values > 127 in a `char` (negative if signed)
- Comparing `char` values to constants like `EOF` (-1), which will fail on unsigned `char`

**Rule:** Use `unsigned char` when manipulating raw bytes. Use `char` only for character data. Use `signed char` when you need a small signed integer.

```c
/* Reading from a file or stream: */
int c = fgetc(fp);  /* int, not char — EOF is -1 */
if (c == EOF) { /* handle end */ }
unsigned char byte = (unsigned char)c;  /* safe byte extraction */
```

## 3.9 The `void` Type

`void` is an incomplete type with no values and no size. It serves three roles:

1. `void` as return type: function returns nothing.
2. `void *`: a generic pointer type — points to any object but cannot be dereferenced without casting.
3. `(void)` parameter list: function takes no arguments. Note: `int f()` in C means "takes unspecified arguments" — very different from `int f(void)`.

## 3.10 Variable Declaration and Initialization

```c
int x;           /* declaration: x exists, uninitialized (undefined value) */
int y = 10;      /* declaration with initialization */
int a, b, c;     /* declare multiple variables of the same type */
int arr[5] = {1, 2, 3, 4, 5};  /* array initialization */
int arr2[5] = {0};              /* all elements zero */
```

**Reading an uninitialized variable is undefined behavior.** The compiler is not required to put any particular value there. In practice, the value is whatever happens to be in memory at that location — often garbage. With sanitizers enabled, reading uninitialized memory is detected immediately.

## 3.11 The `const` Qualifier

`const` declares that an object's value should not be modified through that name. It is enforced by the compiler.

```c
const int MAX = 100;    /* MAX cannot be modified */
MAX = 200;              /* Compiler error */
```

`const` does not mean "compile-time constant" in C (unlike C++). A `const int` is a runtime value that the programmer promises not to change through that name.

`const` and pointers interact in two distinct ways:

```c
const int *p;       /* pointer to const int: p can change, *p cannot */
int * const p;      /* const pointer to int: p cannot change, *p can */
const int * const p;/* const pointer to const int: neither can change */
```

Read pointer declarations right to left: "p is a const pointer to int."

## 3.12 Type Qualifiers Summary

| Qualifier | Meaning |
|---|---|
| `const` | Object is read-only through this name |
| `volatile` | Object may change unexpectedly (tells compiler not to cache) |
| `restrict` | Pointer is the only way to access this object (optimization hint) |
| `_Atomic` | Object is accessed atomically (C11+) |

`volatile` is for memory-mapped hardware registers, signal handlers, and `setjmp`/`longjmp`. It prevents the compiler from optimizing away reads and writes to the object.

## 3.13 Storage Class Specifiers

| Specifier | Meaning |
|---|---|
| `auto` | Local variable (default; almost never written explicitly) |
| `register` | Hint to store in a register (obsolete in C11; advisory only) |
| `static` | Persistent storage duration (for locals); internal linkage (for globals) |
| `extern` | References a definition in another translation unit |

## 3.14 `typedef`

`typedef` creates an alias for a type. It does not create a new type — it creates a new name for an existing type.

```c
typedef unsigned long size_t;   /* size_t is an alias for unsigned long */
typedef struct Node Node;       /* avoids writing 'struct' repeatedly */
typedef int (*Comparator)(const void *, const void *);  /* function pointer type */
```

## 3.15 Enumerations

```c
enum Color { RED, GREEN, BLUE };  /* RED=0, GREEN=1, BLUE=2 */
enum Color c = GREEN;

enum Status {
    STATUS_OK    = 0,
    STATUS_ERROR = -1,
    STATUS_RETRY = 1
};
```

In C, enum values are `int`. The enum type is just `int` in disguise. The compiler will not warn if you assign an integer to an enum variable or compare an enum to an unrelated integer — this is a type-safety limitation compared to C++.

## 3.16 Sizes, Alignment, and `sizeof`

`sizeof` is a compile-time operator (not a function) that returns the size of a type or expression in bytes.

```c
sizeof(int)           /* typically 4 */
sizeof(double)        /* typically 8 */
sizeof(int *)         /* 4 on 32-bit, 8 on 64-bit */
sizeof("hello")       /* 6: 5 chars + null terminator */

int arr[10];
sizeof(arr)           /* 40: 10 * sizeof(int) */
sizeof(arr) / sizeof(arr[0])  /* 10: number of elements — classic idiom */
```

The return type of `sizeof` is `size_t` — an unsigned integer type, typically `unsigned long` on 64-bit platforms.

**Alignment:** Every type has an alignment requirement — an address the object must start at. For most types, the alignment equals the size. `int` requires 4-byte alignment. `double` requires 8-byte alignment. `char` has alignment 1 (no restriction).

```c
#include <stddef.h>
_Alignof(double)    /* typically 8 */
_Alignof(int)       /* typically 4 */
```

---

## Summary

C's type system governs memory layout, valid operations, and conversion behavior. Integer types have platform-dependent sizes; use `<stdint.h>` when exact sizes matter. Floating-point types follow IEEE 754. Plain `char` has implementation-defined signedness. Variables must be initialized before reading. `const` prevents modification through a name. `sizeof` queries the size of any type or expression.

## Key Takeaways

- C integer sizes are minimums, not guarantees; use `int32_t` etc. for exact widths.
- Signed integer overflow is undefined behavior; unsigned overflow wraps.
- `char` signedness is platform-dependent — use `unsigned char` for bytes.
- Reading an uninitialized variable is undefined behavior.
- `const int *p` and `int * const p` are different: the first is a pointer-to-const, the second is a const pointer.

## Common Pitfalls

- Comparing floating-point values with `==` instead of checking within a tolerance.
- Using `char` instead of `int` to store the return value of `fgetc` (missing `EOF`).
- Assuming `int` is 32 bits when writing portable code.
- Ignoring `sizeof` result type (`size_t`) and comparing it to signed integers.

## Exercises

**Easy:** Write a program that prints the size (using `sizeof`) and range (using `<limits.h>`) of every integer type. What does `CHAR_MIN` tell you about your platform?

**Medium:** Demonstrate the difference between `const int *p` and `int * const p` with a code example that shows which assignments are legal and which the compiler rejects.

**Hard:** Explain in detail what happens when you read a `uint8_t` variable that has not been initialized, on a platform where the stack grows downward from a high address. What does AddressSanitizer report? What does UBSan report?

---

# Chapter 4 — Operators and Expressions

## 4.1 Expressions and Lvalues

An *expression* is a sequence of operators and operands that computes a value, designates an object, or produces a side effect — or some combination.

Every expression has a type. Some expressions are *lvalues*: they designate an object that can be assigned to. Others are *rvalues*: they produce a value that is not a persistent object.

```c
int x = 5;
x = 10;         /* x is an lvalue: it names an object that can be assigned */
x + 1;          /* rvalue: produces a value, but there's no object to assign to */
```

## 4.2 Arithmetic Operators

```c
int a = 10, b = 3;
int sum  = a + b;   /* 13 */
int diff = a - b;   /* 7  */
int prod = a * b;   /* 30 */
int quot = a / b;   /* 3  — integer division truncates toward zero */
int rem  = a % b;   /* 1  — remainder; sign follows dividend in C99+ */
```

Integer division truncates toward zero. `7 / 2 == 3`. `-7 / 2 == -3` (in C99+; in C89 the direction was implementation-defined).

The remainder `%` satisfies: `(a/b)*b + a%b == a`. For `a = -7, b = 2`: `(-7/2)*2 + (-7)%2 = -3*2 + (-1) = -7`. So `(-7) % 2 == -1` in C99+.

## 4.3 Integer Overflow

**Unsigned overflow** wraps around modulo 2^N (defined behavior):

```c
uint8_t u = 255;
u++;           /* u is now 0 — wraps around */
```

**Signed overflow is undefined behavior:**

```c
int i = INT_MAX;
i++;           /* undefined behavior — do not do this */
```

Compilers use this rule aggressively. A common optimization: if a loop variable is `int` and the loop condition is `i < INT_MAX + 1`, the compiler may assume this is always true (since signed overflow is UB) and optimize away the entire loop condition. This has caused real security bugs.

Safe arithmetic with overflow detection:

```c
#include <stdckdint.h>  /* C23 */
int result;
if (ckd_add(&result, a, b)) {
    /* overflow occurred */
}

/* C11 and earlier: */
if (a > 0 && b > INT_MAX - a) { /* overflow would occur */ }
```

## 4.4 Relational and Equality Operators

```c
a < b     /* less than */
a > b     /* greater than */
a <= b    /* less than or equal */
a >= b    /* greater than or equal */
a == b    /* equal */
a != b    /* not equal */
```

All relational operators produce `int`: 1 for true, 0 for false. There is no boolean type in C89; `_Bool` exists in C99.

**Warning:** Comparing signed and unsigned values:

```c
int  s = -1;
unsigned int u = 1;
if (s < u) { ... }   /* WARNING: s is implicitly converted to unsigned! -1 becomes UINT_MAX */
```

The compiler should warn about this with `-Wsign-compare` (included in `-Wextra`).

## 4.5 Logical Operators

```c
a && b    /* logical AND: true iff both a and b are nonzero */
a || b    /* logical OR:  true iff at least one is nonzero */
!a        /* logical NOT: 1 if a is zero, 0 otherwise */
```

**Short-circuit evaluation:** In `a && b`, if `a` is zero, `b` is not evaluated. In `a || b`, if `a` is nonzero, `b` is not evaluated. This is guaranteed by the C standard and is often used deliberately:

```c
if (p != NULL && p->value > 0) { ... }  /* safe: p->value only evaluated if p != NULL */
```

## 4.6 Bitwise Operators

Bitwise operators treat integers as sequences of bits:

```c
a & b    /* bitwise AND */
a | b    /* bitwise OR  */
a ^ b    /* bitwise XOR */
~a       /* bitwise NOT (complement) */
a << n   /* left shift by n bits (multiply by 2^n for unsigned) */
a >> n   /* right shift by n bits */
```

Common bitwise idioms:

```c
/* Test bit k: */
if (flags & (1u << k)) { ... }

/* Set bit k: */
flags |= (1u << k);

/* Clear bit k: */
flags &= ~(1u << k);

/* Toggle bit k: */
flags ^= (1u << k);

/* Check power of two: */
int is_power_of_two = (n > 0) && ((n & (n - 1)) == 0);
```

**Shift pitfalls:**

- Left-shifting a negative value is UB.
- Left-shifting into the sign bit is UB (signed types).
- Shifting by a negative amount is UB.
- Shifting by >= the bit width of the type is UB.
- Always shift unsigned values when the result must be used as a bit pattern.

```c
/* Wrong: */
int mask = 1 << 31;    /* UB if int is 32 bits */

/* Correct: */
uint32_t mask = 1u << 31;    /* defined: result is 2147483648 */
```

## 4.7 Assignment Operators

```c
x = 5;      /* simple assignment */
x += 3;     /* x = x + 3 */
x -= 2;     /* x = x - 2 */
x *= 4;     /* x = x * 4 */
x /= 2;     /* x = x / 2 */
x %= 3;     /* x = x % 3 */
x &= mask;  /* x = x & mask */
x |= flag;  /* x = x | flag */
x ^= bits;  /* x = x ^ bits */
x <<= 2;    /* x = x << 2 */
x >>= 1;    /* x = x >> 1 */
```

Assignment is an expression: `y = (x = 5)` assigns 5 to both `x` and `y`. This is valid C but should be used sparingly for clarity.

## 4.8 Increment and Decrement

```c
x++    /* post-increment: value of expression is x, then x is incremented */
++x    /* pre-increment:  x is incremented, value of expression is new x  */
x--    /* post-decrement */
--x    /* pre-decrement */
```

**Where it does not matter:** `x++;` as a standalone statement. The result is the same as `++x;`.

**Where it matters:** In expressions: `y = x++` sets `y` to the old value of `x`; `y = ++x` sets `y` to the new value.

**Critical pitfall:** Using the same variable with increment in the same expression is undefined behavior in C (prior to C17's sequencing rules clarification, and still dangerous):

```c
int a[5] = {0};
int i = 0;
a[i] = i++;   /* undefined behavior */
```

## 4.9 The Conditional (Ternary) Operator

```c
condition ? expression_if_true : expression_if_false
```

```c
int max = (a > b) ? a : b;
const char *sign = (x > 0) ? "positive" : (x < 0) ? "negative" : "zero";
```

The conditional operator is an expression (not a statement). Both branches must have compatible types.

## 4.10 Operator Precedence (Key Levels)

C has 15 precedence levels. Rather than memorize all of them, use parentheses when in doubt.

The most important rules to memorize:

1. `()`, `[]`, `->`, `.` — highest (postfix)
2. Unary: `!`, `~`, `++`, `--`, `*` (dereference), `&` (address-of), `sizeof`
3. `*`, `/`, `%`
4. `+`, `-`
5. `<<`, `>>`
6. `<`, `<=`, `>`, `>=`
7. `==`, `!=`
8. `&` (bitwise AND)
9. `^` (bitwise XOR)
10. `|` (bitwise OR)
11. `&&` (logical AND)
12. `||` (logical OR)
13. `?:` (conditional)
14. `=` and compound assignment
15. `,` (comma) — lowest

Classic mistake: bitwise operators have lower precedence than comparison operators:

```c
if (flags & MASK == 0)    /* parsed as: flags & (MASK == 0) — wrong! */
if ((flags & MASK) == 0)  /* correct */
```

## 4.11 Implicit Conversions: Integer Promotions and Usual Arithmetic Conversions

C applies automatic type conversions in expressions. These are not optional — they happen whether you want them to or not.

**Integer promotions:** Any type smaller than `int` (`char`, `short`, bit-fields) is promoted to `int` (or `unsigned int` if it can't fit) before being used in arithmetic.

```c
char a = 200, b = 100;
int result = a + b;    /* a and b are promoted to int before addition */
```

**Usual arithmetic conversions** (applied to binary operators):

1. If either operand is `long double`, convert the other to `long double`.
2. Else if either is `double`, convert to `double`.
3. Else if either is `float`, convert to `float`.
4. Else apply integer promotions and:
   - If types match, done.
   - If both signed or both unsigned, convert smaller to larger.
   - If unsigned rank >= signed rank, convert signed to unsigned.
   - If signed type can represent all values of unsigned type, convert unsigned to signed.
   - Else both to unsigned version of signed type.

The unsigned/signed conversion rule causes the classic bug:

```c
int x = -1;
if (x < sizeof(int)) { ... }  /* sizeof returns size_t (unsigned); -1 converted to huge unsigned value */
```

## 4.12 Explicit Casts

```c
double d = 3.7;
int i = (int)d;          /* truncates: i == 3 */
int *p = (int *)malloc(sizeof(int));  /* cast void* to int* */
```

Casts are powerful and dangerous. They silence compiler warnings and can hide bugs. Before casting, ask yourself: why does the type not match already? If you find yourself casting frequently, your design may have a type mismatch problem.

---

## Summary

C expressions are composed of operators with defined precedence and associativity. Signed integer overflow is undefined behavior; unsigned overflow wraps. Integer promotions and arithmetic conversions happen implicitly and can cause subtle bugs, especially when mixing signed and unsigned types. Bitwise shift operators on signed values have several undefined behavior cases. Always parenthesize bitwise operations mixed with comparisons.

## Key Takeaways

- Signed integer overflow is undefined behavior — compilers exploit this.
- Unsigned integers wrap on overflow: this is defined.
- Mixing signed and unsigned in comparisons causes implicit conversion surprises.
- `&&` and `||` short-circuit: the right operand may not be evaluated.
- Precedence of `&`, `|`, `^` is lower than `==` and `!=` — always parenthesize.

## Exercises

**Easy:** Write a program demonstrating short-circuit evaluation: have the right operand of `&&` call a function that prints a message. Show that the message is not printed when the left operand is false.

**Medium:** What does this expression evaluate to on a 32-bit platform: `sizeof(int) - 5`? Why? What does `-Wall` report?

**Hard:** Implement a portable, UB-free multiplication of two `int` values that detects overflow and returns a status code. Test it with `INT_MAX * 2` and `INT_MAX * 1`.

---

# Chapter 5 — Control Flow

## 5.1 Structured Programming

C provides structured control flow: sequential execution, conditional branching, and looping. Every C program, no matter how complex, is built from these three primitives. The `goto` statement exists but is used only in specific, idiomatic patterns.

## 5.2 `if`, `else if`, `else`

```c
if (condition) {
    /* executed if condition is nonzero */
} else if (other_condition) {
    /* executed if first was zero and this is nonzero */
} else {
    /* executed if all conditions were zero */
}
```

**The braces are not required for single-statement bodies, but always use them:**

```c
/* Dangerous: */
if (x > 0)
    do_something();
    also_this();    /* this runs regardless of condition! indentation is misleading */

/* Safe: */
if (x > 0) {
    do_something();
}
also_this();        /* clearly unconditional */
```

**The dangling else:** In `if (a) if (b) s1; else s2;`, the `else` binds to the nearest `if` (`if (b)`). Braces resolve ambiguity.

## 5.3 `switch`

```c
switch (expression) {
    case CONSTANT_1:
        /* statements */
        break;
    case CONSTANT_2:
        /* statements */
        break;
    default:
        /* statements */
        break;
}
```

The expression must be an integer type. Cases must be integer constant expressions. The `break` statement exits the switch. Without `break`, execution **falls through** to the next case — this is often a bug.

```c
/* Deliberate fall-through (document it): */
switch (c) {
    case 'a':
    case 'e':
    case 'i':
    case 'o':
    case 'u':
        puts("vowel");    /* all five cases fall through to here */
        break;
    default:
        puts("consonant");
        break;
}
```

GCC and Clang will warn about implicit fall-through with `-Wimplicit-fallthrough`. Use `__attribute__((fallthrough));` (GCC/Clang) or `[[fallthrough]];` (C23) to explicitly mark intentional fall-through.

## 5.4 `while`

```c
while (condition) {
    /* body: executed as long as condition is nonzero */
}
```

The condition is checked before each iteration. If it is initially false, the body never executes.

## 5.5 `do-while`

```c
do {
    /* body: executed at least once */
} while (condition);
```

The body executes first, then the condition is checked. Use `do-while` when the body must execute at least once.

```c
/* Reading input until valid: */
int value;
do {
    printf("Enter a positive number: ");
    scanf("%d", &value);
} while (value <= 0);
```

## 5.6 `for`

```c
for (initialization; condition; increment) {
    body;
}
```

This is exactly equivalent to:

```c
{
    initialization;
    while (condition) {
        body;
        increment;
    }
}
```

Any part can be omitted. `for (;;)` is an infinite loop (all three parts omitted):

```c
for (;;) {
    if (done) break;
}
```

C99 allows variable declarations in the initialization:

```c
for (int i = 0; i < n; i++) {
    /* i is scoped to this loop */
}
```

**Off-by-one errors:** The most common `for` loop mistake. For an array of `n` elements:

```c
/* Correct: indices 0 through n-1 */
for (int i = 0; i < n; i++) { arr[i]; }

/* Wrong: accesses arr[n] — one past the end */
for (int i = 0; i <= n; i++) { arr[i]; }
```

## 5.7 `break` and `continue`

- `break` exits the innermost enclosing `for`, `while`, `do-while`, or `switch`.
- `continue` skips the rest of the current loop body and proceeds to the next iteration.

```c
for (int i = 0; i < 100; i++) {
    if (i % 2 == 0) continue;    /* skip even numbers */
    if (i > 50) break;           /* stop at 50 */
    process(i);
}
```

Neither `break` nor `continue` exits multiple levels of nesting. For that, use a flag variable or `goto`.

## 5.8 `goto`

`goto` jumps unconditionally to a labeled statement within the same function. Its use is almost universally discouraged in modern code, with one important exception: **error handling with cleanup**.

```c
int process_file(const char *path) {
    FILE *f = fopen(path, "r");
    if (!f) return -1;

    char *buf = malloc(1024);
    if (!buf) goto cleanup_file;

    int *data = malloc(512 * sizeof(int));
    if (!data) goto cleanup_buf;

    /* do work */
    int result = do_work(f, buf, data);

    free(data);
cleanup_buf:
    free(buf);
cleanup_file:
    fclose(f);
    return result;
}
```

This pattern — `goto` jumping forward to cleanup code — is used extensively in the Linux kernel and is considered idiomatic C for resource cleanup. It is the closest C comes to RAII (Resource Acquisition Is Initialization).

**Never:** use `goto` to jump backward (creating implicit loops), jump into the middle of a block, or jump across variable initialization.

## 5.9 `return`

`return expression;` exits the current function and provides the return value to the caller.

```c
int add(int a, int b) {
    return a + b;    /* function ends, value returned */
}

void print_hello(void) {
    printf("hello\n");
    return;          /* optional in void functions */
}
```

A non-`void` function that falls off the end without returning is undefined behavior (except `main`, which implicitly returns 0 in C99+).

## 5.10 Control Flow and the Compiler

The compiler extensively reorganizes control flow during optimization:

- **Dead code elimination:** If a branch can be proven unreachable (e.g., because a condition is always true or always false), the compiler removes it.
- **Branch hoisting:** Loop-invariant conditions may be moved outside the loop.
- **Branch prediction hints:** `__builtin_expect(condition, expected_value)` tells the compiler which branch is likely, allowing better instruction ordering.

```c
/* Tell compiler this is the unlikely path (e.g., error handling): */
if (__builtin_expect(ptr == NULL, 0)) {
    handle_error();
}
```

Modern CPUs have branch predictors that observe branch history. Unpredictable branches (50/50) are expensive. Predictable branches (nearly always taken or not taken) are nearly free.

---

## Summary

C provides `if`/`else`, `switch`, `while`, `do-while`, `for`, `break`, `continue`, `goto`, and `return` for control flow. Always brace `if` bodies. Beware fall-through in `switch`. Use `goto` only for forward jumps to cleanup labels. Off-by-one errors in `for` loops are the most common mistake. The compiler reorganizes control flow aggressively.

## Key Takeaways

- Always use braces around control flow bodies, even for single statements.
- `switch` fall-through is the default; always add `break` unless fall-through is intentional.
- `do-while` executes the body at least once.
- `goto` for cleanup is idiomatic; `goto` in other patterns is usually a design flaw.
- Signed overflow in loop conditions can be exploited by the optimizer.

## Exercises

**Easy:** Write a `for` loop that prints all numbers from 1 to 100, printing "Fizz" for multiples of 3, "Buzz" for multiples of 5, and "FizzBuzz" for multiples of both.

**Medium:** Rewrite the following using `goto` for cleanup, ensuring no resources leak even if any allocation fails:
```c
void *a = malloc(100), *b = malloc(200), *c = malloc(300);
/* ... use a, b, c ... */
free(a); free(b); free(c);
```

**Hard:** Write a loop that computes the sum of all integers from 1 to N. Explain why the compiler may transform `for (int i = 1; i <= N; i++)` differently than `for (unsigned int i = 1; i <= N; i++)` when N is `INT_MAX`. Which version has defined behavior?

---

# Chapter 6 — Functions

## 6.1 What Functions Are

A function is a named block of code that takes zero or more arguments, performs a computation, and optionally returns a value. Functions are the primary unit of code organization in C.

Beyond organization, functions enable:
- **Abstraction:** Callers need not know how a function works, only what it does.
- **Reuse:** One definition, many call sites.
- **Recursion:** A function may call itself.
- **Separate compilation:** Functions in different files are compiled independently and linked together.

## 6.2 Declaration vs. Definition

A **declaration** tells the compiler a function exists: its name, return type, and parameter types. A **definition** provides the actual code.

```c
/* Declaration (prototype): */
int add(int a, int b);     /* tells compiler: add takes two ints, returns int */

/* Definition: */
int add(int a, int b) {    /* the actual code */
    return a + b;
}
```

Every function must be declared before it is called (in C99+). The declaration can be in a header file. The definition can be in any source file, as long as exactly one definition exists across all translation units (the One Definition Rule).

## 6.3 Parameters and Pass-by-Value

C passes all arguments **by value**. A copy of the argument is made and passed to the function. The function operates on the copy; the original is unchanged.

```c
void double_it(int x) {
    x = x * 2;     /* modifies the copy */
    printf("%d\n", x);
}

int main(void) {
    int n = 5;
    double_it(n);   /* n is still 5 after this call */
    printf("%d\n", n);  /* prints 5 */
}
```

To modify the caller's variable, pass a pointer:

```c
void double_it(int *x) {
    *x = *x * 2;   /* modifies the object pointed to by x */
}

int main(void) {
    int n = 5;
    double_it(&n);  /* pass the address of n */
    printf("%d\n", n);  /* prints 10 */
}
```

## 6.4 Return Values

A function's return type is declared before the function name. `return` exits the function and passes the value to the caller.

```c
double average(double a, double b) {
    return (a + b) / 2.0;
}

/* Calling it: */
double result = average(3.0, 7.0);  /* result = 5.0 */
```

A function may have multiple return statements — control exits at the first one encountered.

## 6.5 Variadic Functions

Variadic functions accept a variable number of arguments. The `...` in the parameter list indicates the variadic part.

```c
#include <stdarg.h>

/* Sum 'count' integer arguments: */
int sum(int count, ...) {
    va_list args;
    va_start(args, count);    /* initialize: last named param is 'count' */
    
    int total = 0;
    for (int i = 0; i < count; i++) {
        total += va_arg(args, int);   /* extract next argument as int */
    }
    
    va_end(args);             /* clean up */
    return total;
}

int main(void) {
    printf("%d\n", sum(3, 10, 20, 30));  /* prints 60 */
}
```

**Important constraints:**
- There must be at least one named parameter before `...`.
- The caller and function must agree on the number and types of arguments — there is no runtime type information. Getting this wrong is undefined behavior.
- Arguments undergo default promotions: `char` and `short` become `int`; `float` becomes `double`.

`printf` is a variadic function whose format string encodes the types and count of subsequent arguments.

## 6.6 Inline Functions

The `inline` keyword suggests to the compiler that calls to this function should be replaced by the function's body:

```c
static inline int max(int a, int b) {
    return a > b ? a : b;
}
```

**Important:** `inline` is advisory, not mandatory. The compiler may ignore it (and often does for large functions). Conversely, the compiler may inline functions not marked `inline` if it determines it is profitable.

In headers, `static inline` is the preferred pattern: `static` gives the function internal linkage (one per translation unit, no link-time conflicts), and `inline` suggests inlining.

## 6.7 Function Pointers

A function pointer stores the address of a function. Through a function pointer, you can call the function it points to.

```c
/* Declaration: pointer to function taking two ints, returning int */
int (*compare)(int, int);

/* Assigning a function to the pointer: */
int less_than(int a, int b) { return a < b; }
compare = less_than;

/* Calling through the pointer: */
int result = compare(3, 5);     /* calls less_than(3, 5) */
int result2 = (*compare)(3, 5); /* equivalent, older style */
```

Function pointers enable **callbacks** — passing behavior as data:

```c
/* A sort function that takes a comparator callback: */
void sort(int *arr, int n, int (*cmp)(int, int)) {
    /* insertion sort using cmp to compare elements */
    for (int i = 1; i < n; i++) {
        int key = arr[i];
        int j = i - 1;
        while (j >= 0 && cmp(arr[j], key) > 0) {
            arr[j+1] = arr[j];
            j--;
        }
        arr[j+1] = key;
    }
}
```

**Tables of function pointers** are a powerful C pattern for dispatch:

```c
typedef void (*Handler)(void);

Handler handlers[] = {
    handle_event_a,
    handle_event_b,
    handle_event_c
};

handlers[event_type]();   /* dispatch based on event type */
```

## 6.8 Recursive Functions

A function that calls itself:

```c
int factorial(int n) {
    if (n <= 1) return 1;
    return n * factorial(n - 1);
}
```

Each call creates a new stack frame. Deep recursion risks stack overflow. For `factorial(10000)`, the recursion depth is 10,000 frames — likely to overflow the stack.

**Tail recursion:** A call is a tail call if it is the last operation in the function. Some compilers can optimize tail calls to avoid growing the stack (tail call optimization, TCO).

```c
/* Tail-recursive factorial with accumulator: */
int factorial_tail(int n, int acc) {
    if (n <= 1) return acc;
    return factorial_tail(n - 1, n * acc);  /* tail call */
}
```

GCC and Clang perform TCO with `-O2`, but C does not guarantee it.

## 6.9 The Call Stack

Every function call pushes a *stack frame* onto the call stack. The frame contains:

```
Stack frame layout (typical, implementation-defined):
┌─────────────────────────────┐
│ Return address              │  ← Where to return when function exits
│ Saved frame pointer         │  ← Caller's frame pointer
│ Local variables             │  ← All locals of this function
│ Padding/alignment           │  ← To maintain alignment requirements
│ Arguments (on some ABIs)    │  ← Or in registers on modern ABIs
└─────────────────────────────┘
```

Stack frames are created on function entry and destroyed on function exit. The stack pointer (`%rsp` on x86-64) tracks the top of the stack.

Stack overflow occurs when the stack grows into memory it does not own — typically when recursion is too deep or local arrays are too large.

## 6.10 Calling Conventions and ABI

A *calling convention* defines how function arguments are passed and how the return value is communicated. On x86-64 Linux (System V AMD64 ABI):

- First 6 integer/pointer arguments: RDI, RSI, RDX, RCX, R8, R9
- First 8 floating-point arguments: XMM0–XMM7
- Additional arguments: pushed on the stack (right to left)
- Return value: RAX (integer/pointer), XMM0 (floating-point)

This means most functions with 6 or fewer arguments pass everything in registers — no stack traffic for arguments at all. This is why C function calls are fast.

## 6.11 `main` and Its Special Rules

`main` is the program's entry point. It has two standard signatures:

```c
int main(void);
int main(int argc, char *argv[]);
```

- `argc`: the count of command-line arguments (always at least 1 — `argv[0]` is the program name).
- `argv`: array of strings (null-terminated). `argv[argc]` is guaranteed to be `NULL`.
- Returns 0 for success; nonzero for failure. Return values 0 and `EXIT_SUCCESS` are equivalent; both are guaranteed to indicate success to the shell.

In C99+, `main` implicitly returns 0 if control falls off the end — this is the only function with this special behavior.

---

## Summary

Functions are the fundamental organizational unit of C programs. Arguments are passed by value; use pointers to modify caller variables. Function pointers enable callbacks and dispatch tables. Variadic functions use `<stdarg.h>`. The call stack holds one frame per active function call. Stack overflow results from excessive recursion or large locals. Calling conventions define how arguments are passed and are platform-specific.

## Key Takeaways

- C is strictly pass-by-value; pass pointers to modify caller's data.
- Declare functions before use (via prototypes or definitions).
- Variadic functions have no type safety — the caller and callee must agree.
- Function pointers are first-class values in C.
- The call stack is finite; recursive functions can overflow it.
- `inline` is a hint, not a command.

## Exercises

**Easy:** Write a function `swap(int *a, int *b)` that swaps the values of two integers. Demonstrate that it modifies the caller's values.

**Medium:** Implement a generic `map` function that takes an array of ints, its length, and a function pointer `int (*f)(int)`, and applies `f` to each element in place.

**Hard:** Implement a recursive descent parser for arithmetic expressions (supporting `+`, `-`, `*`, `/`, and parentheses). Use mutually recursive functions for `expr()`, `term()`, and `factor()`.


---

# PART II — MEMORY AND POINTERS

*Theme: The heart of C. Everything in Part I is preparation for this. Understanding memory and pointers is what separates C programmers from C experts.*

---

# Chapter 7 — The Memory Model

## 7.1 The C Abstract Machine

The C standard defines behavior relative to an *abstract machine* — an imaginary computer that executes C programs. Real hardware is just one possible implementation of this machine.

The abstract machine has:
- A memory consisting of bytes, each identified by an address
- Objects of various types occupying contiguous sequences of bytes
- A stack for automatic storage
- A heap for allocated storage
- A program counter and execution model

Compilers are free to implement the abstract machine any way they choose, as long as the *observable behavior* of a program matches what the abstract machine would produce. This is the "as-if" rule: the compiler can transform code however it likes, as long as the result behaves as if the abstract machine ran the original code.

This is why optimizations are legal and why undefined behavior is so dangerous — if your code invokes UB, the program no longer needs to behave as if the abstract machine ran it at all.

## 7.2 The Five Memory Segments

A running process has its virtual address space divided into segments:

```
Virtual address space (typical Linux, 64-bit):
┌──────────────────────────────────┐  High addresses (~0x7FFF FFFF FFFF)
│  Kernel space                    │  ← Not accessible from user code
├──────────────────────────────────┤
│  Stack                           │  ← Grows downward
│  ↓                               │
│       (unmapped space)           │
│  ↑                               │
│  Heap                            │  ← Grows upward
├──────────────────────────────────┤
│  BSS segment                     │  ← Uninitialized globals (zeroed)
├──────────────────────────────────┤
│  Data segment                    │  ← Initialized globals
├──────────────────────────────────┤
│  Text segment (code)             │  ← Read-only: compiled instructions
└──────────────────────────────────┘  Low addresses (~0x0000 0000 0000)
```

**Text segment:** Contains the compiled machine code of your program. Read-only (writing to it causes a segfault). Also contains string literals (which is why modifying a string literal is undefined behavior).

**Data segment:** Contains global and `static` variables that are explicitly initialized to nonzero values.
```c
int global = 42;         /* lives in data segment */
static int count = 100;  /* lives in data segment */
```

**BSS segment:** Contains global and `static` variables that are either uninitialized or initialized to zero. The OS zeroes this segment before the program starts. (BSS stands for "Block Started by Symbol" — a historical name.)
```c
int uninitialized_global;  /* BSS: initialized to 0 */
static int zeroed = 0;     /* BSS: initialized to 0 */
```

**Heap:** The region managed by `malloc`/`free`. Grows upward from a low address. Managed by the allocator (`glibc` uses `ptmalloc`, `jemalloc` and `tcmalloc` are alternatives).

**Stack:** The region for automatic variables (local variables and function parameters). Grows downward on x86. Each function call creates a new frame on the stack; returning from a function destroys its frame.

## 7.3 The Stack In Depth

The stack is a contiguous region of memory. The *stack pointer* (a CPU register) tracks the boundary between used and unused stack space.

```
Before calling f():           During f() execution:
┌──────────────┐             ┌──────────────┐
│ (caller      │             │ (caller      │
│  frame)      │             │  frame)      │
├──────────────┤ ← SP        ├──────────────┤
│              │             │ return addr  │
│   (unused)   │             │ saved rbp    │
│              │             │ local: x     │  ← SP
└──────────────┘             │ local: y     │
                             ├──────────────┤
                             │              │
                             │   (unused)   │
                             └──────────────┘
```

When `f()` is called:
1. The return address is pushed onto the stack.
2. The caller's frame pointer is saved.
3. The stack pointer is decremented by the size of `f`'s locals.
4. Locals are now accessible relative to the frame pointer.

When `f()` returns:
1. The stack pointer is restored to where it was before `f`'s locals.
2. The caller's frame pointer is restored.
3. The return address is popped and jumped to.

**Stack size:** Typically 1–8 MB on desktop Linux (configurable with `ulimit -s`). Arrays larger than a few kilobytes should go on the heap.

## 7.4 Object Lifetime and Storage Duration

Every object in C has a *lifetime* — the time during which it is valid to access it. Accessing an object outside its lifetime is undefined behavior.

**Automatic storage duration:** Objects declared inside a function body (without `static`). Created when the block containing their declaration is entered; destroyed when it exits.

```c
void f(void) {
    int x = 5;    /* created on block entry */
    {
        int y = 10;  /* created here */
    }              /* y destroyed here */
    /* y is gone; x still exists */
}                  /* x destroyed here */
```

**Static storage duration:** Objects declared at file scope, or inside functions with `static`. Created once, before `main` begins. Destroyed when the program exits.

**Allocated storage duration:** Objects created with `malloc` (and friends). Created on the call to `malloc`; destroyed on the call to `free`. The programmer controls the lifetime explicitly.

**Temporary lifetime:** Objects that are the result of expressions not assigned to a variable. They are valid through the end of the enclosing full expression.

## 7.5 Alignment

Every type has an *alignment requirement*: a constraint on which addresses objects of that type may start at. An `int` (typically 4 bytes) must start at an address that is a multiple of 4. A `double` (typically 8 bytes) must start at a multiple of 8.

Alignment exists because hardware accesses memory most efficiently at aligned addresses. An unaligned access (where it is supported at all) takes extra cycles; on some architectures (ARMv6 and earlier, MIPS), it causes a bus error.

```c
/* What _Alignof tells you: */
_Alignof(char)        /* 1  — no alignment requirement */
_Alignof(short)       /* 2  */
_Alignof(int)         /* 4  */
_Alignof(double)      /* 8  */
_Alignof(void *)      /* 8  (on 64-bit) */
```

The compiler ensures alignment automatically for variables and struct members (by inserting padding — covered in Chapter 11).

You can specify alignment explicitly:

```c
_Alignas(64) int arr[16];   /* arr starts at a 64-byte boundary (cache line) */
```

## 7.6 Virtual vs Physical Memory

When you see an address in C, it is a *virtual* address. The CPU's Memory Management Unit (MMU) translates virtual addresses to physical addresses using page tables maintained by the OS.

This means:
- Two processes with `int x` at address `0x7fff0000` have completely separate physical memory.
- The heap can appear contiguous even when physical memory is fragmented.
- Memory-mapped I/O, files, and shared memory all appear as virtual address ranges.
- ASLR (Address Space Layout Randomization) randomizes the starting addresses of stack, heap, and libraries at each execution — a security feature.

## 7.7 Cache Architecture and Performance

CPUs do not read memory one byte at a time. They fetch *cache lines* — typically 64 bytes on x86 — from main memory into caches.

```
CPU registers (cycles: <1)
L1 cache (cycles: 4,  size: ~32KB)
L2 cache (cycles: 12, size: ~256KB)
L3 cache (cycles: 40, size: ~8MB)
Main memory (cycles: 200+)
```

The implication: *where* your data is in memory matters almost as much as *what* computation you do. A tight loop over a 100-element array fits in L1 cache and runs at nearly register speed. A loop that follows random pointers through a large linked list causes a cache miss on every access — 50× slower.

This is the performance reason to prefer arrays over linked lists, to prefer struct-of-arrays over array-of-structs in some cases, and to write cache-friendly algorithms. Chapter 23 covers this in depth.

---

## Summary

The C abstract machine defines an execution model that compilers implement via the "as-if" rule. Running processes have five memory regions: text (code), data (initialized globals), BSS (zeroed globals), heap (dynamic allocation), and stack (local variables). Objects have lifetimes that must be respected. Every type has an alignment requirement. Virtual addresses are translated to physical by the MMU. Cache architecture means memory access patterns dominate performance.

## Key Takeaways

- Text, data, BSS, heap, and stack are distinct regions with distinct purposes.
- Automatic (local) objects die when their block exits — never return a pointer to a local.
- Alignment requirements must be satisfied; the compiler handles this via padding.
- The stack is finite: large local arrays cause stack overflow.
- Virtual addresses are not physical addresses; ASLR randomizes them.

## Common Pitfalls

- Returning a pointer to a local variable (dangling pointer).
- Assuming global variables are uninitialized (they are zeroed by the OS).
- Creating arrays larger than ~1 MB as local variables.
- Misunderstanding that `malloc` addresses are virtual, not physical.

## Exercises

**Easy:** Write a program that prints the addresses of a local variable, a global variable, a string literal, a heap-allocated object, and a function. Which addresses fall in which memory regions?

**Medium:** Write a function that intentionally overflows the stack by recursing without a base case. What does the OS do? What error message do you get?

**Hard:** Explain the difference in cache behavior between iterating over a 1000×1000 `int` matrix row-by-row vs column-by-column. Write both versions, time them, and explain the difference in terms of cache lines.

---

# Chapter 8 — Pointers: Fundamentals and Arithmetic

## 8.1 What a Pointer Is

A pointer is a variable that holds a memory address. More precisely, in C: a pointer is an object whose value is the address of another object (or a null pointer, or one-past-the-end of an array, or an indeterminate value if uninitialized).

Pointers are not magic. They are just integers that happen to represent addresses. On a 64-bit platform, a pointer is 8 bytes — the same size as `uint64_t`. The type associated with a pointer (`int *`, `char *`, `double *`) tells the compiler what kind of object lives at that address, how many bytes to read or write when dereferencing, and how far to advance when doing pointer arithmetic.

## 8.2 Why Pointers Exist

Pointers solve three fundamental problems in C:

1. **Indirection:** Passing large objects to functions without copying them, and allowing functions to modify the caller's variables.
2. **Dynamic allocation:** `malloc` returns a pointer to newly allocated memory. Without pointers, dynamic allocation is impossible.
3. **Generic programming:** `void *` is a pointer to anything, enabling generic containers and library functions like `qsort` and `memcpy`.

## 8.3 Pointer Declaration Syntax

```c
int *p;         /* p is a pointer to int */
double *q;      /* q is a pointer to double */
char *s;        /* s is a pointer to char */
int **pp;       /* pp is a pointer to a pointer to int */
void *v;        /* v is a generic pointer */
int (*fp)(int); /* fp is a pointer to a function taking int, returning int */
```

**The clockwise/spiral rule:** To read complex declarations, start at the identifier and read clockwise (right, up, left, down), applying the following:
- `*` to the left: "pointer to"
- `[N]` to the right: "array of N"
- `(args)` to the right: "function taking args"

```c
int *(*fp)[10];
/* Start at fp: "fp"
   Go right, hit ')': back out
   Go left, hit *: "fp is a pointer to"
   Go right, hit [10]: "an array of 10"
   Go right, nothing
   Go left, hit *: "pointers to"
   Continue left: "int"
   Result: fp is a pointer to an array of 10 pointers to int */
```

## 8.4 Address-of and Dereference

`&x` (address-of): produces a pointer to `x`. The type is pointer-to-type-of-x.

`*p` (dereference): accesses the object that `p` points to. The type is the pointed-to type.

```c
int x = 42;
int *p = &x;    /* p holds the address of x */
int y = *p;     /* y = 42: dereference p to get x's value */
*p = 100;       /* modifies x through p: now x == 100 */
```

```
Memory view:
Address    Object    Value
0x1000     x         42        ← x lives here
0x2000     p         0x1000    ← p holds x's address

After *p = 100:
0x1000     x         100       ← modified through p
0x2000     p         0x1000    ← unchanged
```

## 8.5 NULL Pointers

A null pointer is a pointer that does not point to any object. Its value is guaranteed to compare equal to zero.

```c
int *p = NULL;     /* p is a null pointer */
int *q = 0;        /* also a null pointer (0 in pointer context) */
```

Dereferencing a null pointer is undefined behavior. On most systems it causes a segmentation fault (SIGSEGV), but this is not guaranteed — the behavior is literally undefined.

Always check pointers from `malloc` and similar functions:

```c
int *arr = malloc(100 * sizeof(int));
if (arr == NULL) {
    /* handle allocation failure */
    return -1;
}
```

`NULL` is defined in `<stddef.h>` (and many other headers). In C11, the `nullptr` keyword is not available (that is C++); in C23, `nullptr` is added as a null pointer constant of type `nullptr_t`.

## 8.6 Pointer Arithmetic

You can add or subtract integers from pointers. The result is another pointer into the same array.

**The rule:** Adding `n` to a pointer of type `T *` advances the pointer by `n * sizeof(T)` bytes.

```c
int arr[5] = {10, 20, 30, 40, 50};
int *p = arr;     /* p points to arr[0] */
int *q = p + 2;   /* q points to arr[2] (advanced by 2*4 = 8 bytes) */
int val = *q;     /* val = 30 */
p++;              /* p now points to arr[1] */
```

```
Memory layout:
Address   Value   Pointer
0x1000    10      p = arr (p points here initially)
0x1004    20
0x1008    30      q = p + 2 (points here)
0x100C    40
0x1010    50
```

**Pointer subtraction:** Subtracting two pointers of the same type yields the number of elements between them (not bytes). The result type is `ptrdiff_t` (a signed integer type from `<stddef.h>`).

```c
ptrdiff_t diff = q - p;   /* diff = 2 (two elements apart) */
```

**Bounds:** Pointer arithmetic is only defined within an array (and one element past the end, for comparison purposes only). Going beyond is undefined behavior.

```c
int arr[5];
int *p = arr + 5;   /* legal: one-past-the-end pointer */
int *q = arr + 6;   /* undefined behavior: too far */
int x = *(arr + 5); /* undefined behavior: dereferencing past-the-end */
```

## 8.7 Pointer Comparison

Pointers to elements of the same array can be compared with `<`, `>`, `<=`, `>=`, `==`, `!=`. The comparison reflects their positions in the array.

```c
int arr[5];
int *p = arr + 1;
int *q = arr + 3;
if (p < q) { ... }   /* true: p is before q in the array */
```

Comparing pointers from different objects is undefined behavior (except `==` and `!=`, which have defined but unspecified results).

## 8.8 `void *`: The Generic Pointer

`void *` is a pointer to an untyped memory location. It can point to any object. It cannot be dereferenced without first casting to a concrete type (because the compiler doesn't know how many bytes to read).

```c
void *p = malloc(100);  /* malloc returns void * */
int *ip = p;            /* implicit conversion: void* → int* (valid in C, not C++) */
*ip = 42;               /* dereference the int* */
```

`memcpy`, `memset`, and `memmove` take `void *` arguments, allowing them to work on any data type.

## 8.9 The Strict Aliasing Rule

**One of the most important rules in C programming, and one of the least understood.**

The strict aliasing rule states: an object may only be accessed through an lvalue of:
- Its type
- A qualified version of its type
- A signed/unsigned variant of its type
- A character type (`char`, `unsigned char`, `signed char`)
- A struct or union type that contains one of the above

Accessing an object through a pointer to an incompatible type is **undefined behavior**. The compiler uses this rule to assume that pointers of different types do not alias, enabling powerful optimizations.

```c
/* Classic violation: */
int i = 0x41424344;
float f = *(float *)&i;   /* undefined behavior: reading int as float */

/* Legal way (using memcpy): */
float f;
memcpy(&f, &i, sizeof(f));  /* defined: copies bytes, no aliasing issue */
```

The `unsigned char` exception exists specifically for byte-level memory operations:

```c
void print_bytes(void *obj, size_t n) {
    unsigned char *p = (unsigned char *)obj;  /* legal: char can alias anything */
    for (size_t i = 0; i < n; i++) {
        printf("%02x ", p[i]);
    }
}
```

**Practical consequence:** If you have a function like:
```c
void f(int *a, float *b) {
    *a = 1;
    *b = 2.0f;
    return *a;   /* compiler assumes this is still 1 (a and b can't alias) */
}
```
The compiler is allowed to return 1 without re-reading `*a`, because `int *` and `float *` cannot legally alias. If your code violates this, you get silently wrong results.

## 8.10 The `restrict` Qualifier

`restrict` is a promise to the compiler: this pointer is the only way to access the memory it points to (within the scope of the declaration). This allows the compiler to optimize more aggressively.

```c
void memcpy_impl(void * restrict dst, const void * restrict src, size_t n) {
    /* restrict tells compiler: dst and src do not overlap */
    /* This enables SIMD/vectorized implementation */
    unsigned char *d = dst;
    const unsigned char *s = src;
    while (n--) *d++ = *s++;
}
```

If you violate the `restrict` promise (pass overlapping pointers), the behavior is undefined.

## 8.11 Multi-level Pointers

A pointer can point to another pointer:

```c
int x = 42;
int *p = &x;     /* p → x */
int **pp = &p;   /* pp → p → x */

**pp = 100;      /* modifies x through two levels of indirection */
```

Multi-level pointers appear naturally when a function needs to return a newly allocated pointer:

```c
int allocate_array(int **out, size_t n) {
    *out = malloc(n * sizeof(int));
    return *out != NULL ? 0 : -1;
}

int *arr;
if (allocate_array(&arr, 100) != 0) { /* handle error */ }
```

## 8.12 Pointer Size and Architecture

On 32-bit platforms, pointers are 4 bytes. On 64-bit platforms, pointers are 8 bytes. (`sizeof(void *)` tells you the pointer size on the current platform.)

This matters for:
- Struct layouts that contain pointers
- Casting between pointers and integers (use `uintptr_t` from `<stdint.h>`)
- File formats or protocols that store addresses

## 8.13 Function Pointers: In Depth

A function pointer's type must exactly match the function's signature:

```c
int add(int a, int b) { return a + b; }
int sub(int a, int b) { return a - b; }

int (*op)(int, int);   /* pointer to function(int, int) → int */
op = add;
printf("%d\n", op(3, 4));   /* 7 */
op = sub;
printf("%d\n", op(3, 4));   /* -1 */
```

Calling a function through an incompatible pointer type is undefined behavior:

```c
void f(void);
int (*bad)(int) = (int (*)(int))f;  /* type mismatch */
bad(42);   /* undefined behavior */
```

---

## Summary

A pointer is an object holding a memory address. Pointer arithmetic advances by element size, not by bytes. NULL pointers must not be dereferenced. The strict aliasing rule is a critical constraint on pointer types: accessing an object through an incompatible pointer type is undefined behavior and enables compiler optimizations. `restrict` promises non-aliasing. Function pointers hold addresses of functions and enable callbacks and dispatch.

## Key Takeaways

- Pointer arithmetic advances by `n * sizeof(T)` bytes when type is `T *`.
- Dereferencing NULL is undefined behavior (usually SIGSEGV).
- The strict aliasing rule: only access an object through a compatible type pointer, or `char *`.
- `restrict` is a promise that enables optimization; violating it is UB.
- Multi-level pointers (`int **`) allow functions to return allocated pointers.

## Common Pitfalls

- Dereferencing an uninitialized pointer.
- Returning a pointer to a local variable.
- Using a pointer after `free` (use-after-free).
- Violating strict aliasing (silently wrong results).
- Comparing pointers from different objects.

## Exercises

**Easy:** Write a function `pointer_walk(int *arr, int n)` that uses pointer arithmetic (no subscript operator) to sum all elements.

**Medium:** Implement `void reverse(int *arr, int n)` using two pointers — one starting at the beginning, one at the end — advancing toward each other.

**Hard:** Write a function that safely type-puns a `float` to a `uint32_t` (to inspect its bits) without violating strict aliasing. Use `memcpy`. Verify the bit pattern against the IEEE 754 representation.

---

# Chapter 9 — Arrays and Strings

## 9.1 Arrays: Declaration and Layout

An array is a contiguous sequence of objects of the same type.

```c
int arr[5];              /* 5 ints: arr[0] through arr[4] */
int init[5] = {1,2,3,4,5};         /* initialized */
int partial[5] = {1, 2};            /* rest initialized to 0 */
int zeroed[100] = {0};              /* all zeroed */
int sized[] = {10, 20, 30};         /* size inferred: 3 elements */
```

Memory layout — elements are contiguous, no gaps:

```
arr[5] (int, 4 bytes each):
Address   Element
0x1000    arr[0]    (4 bytes)
0x1004    arr[1]    (4 bytes)
0x1008    arr[2]    (4 bytes)
0x100C    arr[3]    (4 bytes)
0x1010    arr[4]    (4 bytes)
```

`arr[i]` is exactly equivalent to `*(arr + i)`. The subscript operator is just syntactic sugar for pointer arithmetic and dereference.

## 9.2 Array Decay

**This is one of the most important rules in C.**

An array name, when used in most expression contexts, **decays** (implicitly converts) to a pointer to its first element.

```c
int arr[5] = {1, 2, 3, 4, 5};

int *p = arr;           /* arr decays to &arr[0]: a pointer to int */
printf("%d\n", *p);     /* 1 */
printf("%d\n", p[2]);   /* 3 — pointer subscript works just like array subscript */
```

**Exceptions where decay does NOT occur:**
- `sizeof(arr)` — gives the size of the whole array, not a pointer
- `&arr` — gives a pointer to the whole array (`int (*)[5]`), not to the first element
- `_Alignof(arr)` — gives alignment of the array type

**Consequence:** When you pass an array to a function, you pass a pointer, not a copy of the array. The function sees a pointer and has no way to determine the array's length without being told.

```c
void sum(int arr[], int n) {   /* arr is really int*, not an array */
    /* sizeof(arr) here is sizeof(int*), NOT sizeof the original array! */
}
```

## 9.3 Arrays Are NOT Pointers

Despite decay, arrays and pointers are different things:

```c
int arr[5];
int *p = arr;

sizeof(arr)   /* 20 (5 * sizeof(int)) — size of the array */
sizeof(p)     /* 8 (size of a pointer on 64-bit) */

&arr          /* type: int (*)[5] — pointer to array-of-5-ints */
&p            /* type: int **    — pointer to pointer-to-int */
```

The array name is not a variable — you cannot assign to it or increment it:
```c
arr = p;    /* error: array type is not assignable */
arr++;      /* error: cannot increment an array */
```

## 9.4 Out-of-Bounds Access

Accessing an array element outside its bounds is **undefined behavior**. The most common C bug.

```c
int arr[5];
arr[5] = 42;    /* UB: index 5 is out of bounds (valid: 0–4) */
arr[-1] = 0;    /* UB: negative index */
```

The compiler does not add runtime bounds checks. The behavior is truly undefined — the program may crash, silently corrupt data, or appear to work correctly. AddressSanitizer and Valgrind detect these accesses.

## 9.5 Multidimensional Arrays

```c
int matrix[3][4];   /* 3 rows, 4 columns */
matrix[1][2] = 42;  /* row 1, column 2 */
```

C multidimensional arrays are stored in *row-major order*: all elements of row 0 first, then row 1, etc.

```
matrix[3][4] memory layout:
[0][0] [0][1] [0][2] [0][3] | [1][0] [1][1] [1][2] [1][3] | [2][0] ...
```

This matters enormously for performance: iterating row-by-row is cache-friendly; iterating column-by-column is not.

When passing multidimensional arrays to functions, all dimensions except the first must be specified:

```c
void process(int matrix[][4], int rows) {  /* need to know column count */
    for (int i = 0; i < rows; i++)
        for (int j = 0; j < 4; j++)
            matrix[i][j] *= 2;
}
```

## 9.6 Variable-Length Arrays (VLAs)

C99 introduced VLAs: arrays whose size is determined at runtime.

```c
void process(int n) {
    int arr[n];    /* VLA: size determined at runtime */
    /* arr lives on the stack */
}
```

VLAs are allocated on the stack. They are destroyed when the enclosing scope exits. They cannot be initialized with `= {...}`.

**Important:** VLAs were mandatory in C99 and optional (but common) in C11. They are optional in C17 and C23. Avoid VLAs for large sizes (risk of stack overflow) or in safety-critical code. Use `malloc` for variable-size arrays if the size might be large.

## 9.7 Strings in C

A string in C is a null-terminated sequence of characters: a contiguous array of `char` values ending with the null character `'\0'` (value 0).

```c
char str[] = "hello";   /* {'h', 'e', 'l', 'l', 'o', '\0'}: 6 bytes */
char *p = "hello";      /* pointer to string literal (read-only!) */
```

```
str array in memory:
Address   Value
0x1000    'h'  (0x68)
0x1001    'e'  (0x65)
0x1002    'l'  (0x6C)
0x1003    'l'  (0x6C)
0x1004    'o'  (0x6F)
0x1005    '\0' (0x00)  ← null terminator
```

**String literals** are stored in the read-only text segment. Attempting to modify them is undefined behavior (usually SIGSEGV):

```c
char *p = "hello";
p[0] = 'H';    /* undefined behavior: string literals are read-only */

char arr[] = "hello";   /* copy into a writable array */
arr[0] = 'H';           /* OK: arr is a local array */
```

The null terminator is what makes a C string a string. Library functions like `strlen`, `strcpy`, `printf` with `%s` all scan for `'\0'` to find the end. If your char array lacks a null terminator, these functions will read beyond the array — undefined behavior and a classic security vulnerability.

## 9.8 String Library Functions (`<string.h>`)

| Function | Purpose | Safe? |
|---|---|---|
| `strlen(s)` | Length (excluding null) | ✓ (if s is valid string) |
| `strcpy(dst, src)` | Copy string | ✗ (no bounds check on dst) |
| `strncpy(dst, src, n)` | Copy at most n chars | ✗ (may not null-terminate) |
| `strcat(dst, src)` | Append string | ✗ (no bounds check) |
| `strncat(dst, src, n)` | Append at most n chars | ✓ (null-terminates) |
| `strcmp(s1, s2)` | Compare strings | ✓ |
| `strncmp(s1, s2, n)` | Compare at most n chars | ✓ |
| `strchr(s, c)` | Find first char | ✓ |
| `strstr(s, t)` | Find substring | ✓ |
| `strtok(s, delim)` | Tokenize (not thread-safe) | ✗ |
| `memcpy(dst, src, n)` | Copy n bytes | ✗ (regions must not overlap) |
| `memmove(dst, src, n)` | Copy n bytes (overlap safe) | ✓ |
| `memset(s, c, n)` | Fill n bytes with c | ✓ |
| `memcmp(s1, s2, n)` | Compare n bytes | ✓ |

**`strcpy` is dangerous:** It copies until it finds `'\0'` in `src`, writing to `dst` without knowing how large `dst` is. If `src` is longer than `dst`, you corrupt adjacent memory.

**`strncpy` is broken in a different way:** If `src` is >= `n` characters, the result is not null-terminated. It also zero-pads `dst` to `n` bytes if `src` is shorter — a performance cost that surprises users.

**The correct functions for safe string copying:**
```c
/* Available on BSD systems, and with -D_GNU_SOURCE on glibc: */
strlcpy(dst, src, sizeof(dst));    /* always null-terminates; returns strlen(src) */
strlcat(dst, src, sizeof(dst));    /* always null-terminates */

/* C11 Annex K (optional): */
strcpy_s(dst, sizeof(dst), src);
strcat_s(dst, sizeof(dst), src);

/* Or just use snprintf: */
snprintf(dst, sizeof(dst), "%s", src);  /* always null-terminates */
```

## 9.9 Buffer Overflows via Strings

Buffer overflow is the most historically significant security vulnerability class in C:

```c
void vulnerable(char *user_input) {
    char buf[64];
    strcpy(buf, user_input);  /* if strlen(user_input) >= 64, overflow! */
}
```

If `user_input` is 200 characters long, `strcpy` writes 200 bytes into a 64-byte buffer, corrupting whatever comes after `buf` on the stack — including the return address. An attacker can craft input that overwrites the return address to point to injected shellcode.

This class of attack (stack buffer overflow) was the dominant exploit technique from the 1980s through the early 2000s. Modern mitigations (stack canaries, ASLR, NX bits) make it harder but not impossible.

**Always bound your writes.** Use `snprintf` for string formatting, `fgets` for line reading, and carefully computed sizes for memory operations.

## 9.10 Passing Arrays to Functions

When an array is passed to a function, it decays to a pointer. The function must be told the length separately:

```c
double average(const double *arr, size_t n) {
    double sum = 0.0;
    for (size_t i = 0; i < n; i++) sum += arr[i];
    return sum / n;
}

double data[5] = {1.0, 2.0, 3.0, 4.0, 5.0};
double avg = average(data, 5);   /* pass array and its length */
```

**Pattern:** Use `const T *` for arrays that will not be modified. Use `T *` for arrays that will be modified. Use `size_t` for the length parameter.

---

## Summary

Arrays are contiguous sequences of same-type objects. Array names decay to pointers to the first element in most contexts, but `sizeof` and `&` are exceptions. Out-of-bounds access is undefined behavior. Strings are null-terminated character arrays; library functions rely on this terminator. Use bounds-checked string functions. Always pass array length separately when passing arrays to functions.

## Key Takeaways

- Array decay: `int arr[5]` in most expressions becomes `int *` pointing to `arr[0]`.
- `sizeof(arr)` gives the total size; `sizeof(ptr)` gives pointer size — they are different.
- Strings must be null-terminated; many functions crash or corrupt memory if they are not.
- `strcpy` and `gets` are dangerous; use `snprintf` and `fgets`.
- Row-major iteration over multidimensional arrays is cache-friendly.

## Exercises

**Easy:** Write `size_t my_strlen(const char *s)` without using `<string.h>`. Verify it matches `strlen`.

**Medium:** Write a function that reads a line of user input safely (up to a given maximum length) using `fgets`, stripping the trailing newline if present.

**Hard:** Implement `void *my_memmove(void *dst, const void *src, size_t n)` that correctly handles overlapping source and destination regions. Explain why `memcpy` is insufficient for overlapping regions.

---

# Chapter 10 — Dynamic Memory Management

## 10.1 Why Dynamic Allocation Exists

Automatic (stack) allocation is fast but limited: the size must be known at compile time (or at call time for VLAs), and objects are destroyed when their scope exits. You cannot return a dynamically sized array from a function using automatic storage.

Dynamic allocation lets you:
- Allocate memory whose size is determined at runtime
- Allocate memory that outlives the function that created it
- Allocate very large objects (stack is typically 1–8 MB; heap can use all available memory)
- Build data structures whose size changes over time (dynamic arrays, linked lists, trees)

## 10.2 `malloc`

```c
#include <stdlib.h>

void *malloc(size_t size);
```

`malloc(n)` allocates `n` bytes of memory from the heap and returns a pointer to the first byte. The returned memory is:
- Properly aligned for any fundamental type
- Uninitialized (contains garbage)
- `NULL` if allocation fails

```c
int *arr = malloc(100 * sizeof(int));
if (arr == NULL) {
    /* Allocation failed — must handle this */
    perror("malloc");
    return -1;
}
/* arr now points to 400 bytes of uninitialized memory */
arr[0] = 42;  /* valid */
free(arr);    /* release when done */
```

**Always check for NULL.** `malloc` can fail if the system is out of memory or if the requested size overflows `size_t`. In security-critical code, failing to check is a bug. In most applications, failing to check causes a null pointer dereference shortly thereafter.

**Size calculation pitfalls:**
```c
/* Potential overflow if n is very large: */
int *arr = malloc(n * sizeof(int));  /* if n * sizeof(int) overflows size_t, allocates too little */

/* Safe: use calloc or check first */
if (n > SIZE_MAX / sizeof(int)) { /* overflow! */ }
int *arr = malloc(n * sizeof(int));  /* now safe */
```

## 10.3 `calloc`

```c
void *calloc(size_t count, size_t size);
```

`calloc(count, size)` allocates `count * size` bytes and zeroes them. The multiplication is performed safely — if it overflows, `calloc` returns NULL.

```c
int *arr = calloc(100, sizeof(int));  /* 100 ints, all zero */
```

Use `calloc` when you want zeroed memory (common for arrays that will be partially filled) or when you want overflow-safe size calculation.

## 10.4 `realloc`

```c
void *realloc(void *ptr, size_t new_size);
```

`realloc` changes the size of a previously allocated block. The content is preserved up to the minimum of old and new sizes. The returned pointer may differ from the input pointer (the block may have moved).

**Classic misuse:**
```c
/* WRONG: if realloc fails, ptr is leaked */
ptr = realloc(ptr, new_size);
if (ptr == NULL) { /* we just lost the original pointer */ }

/* CORRECT: save in a temporary */
void *new_ptr = realloc(ptr, new_size);
if (new_ptr == NULL) {
    /* ptr is still valid — still points to original allocation */
    free(ptr);
    return -1;
}
ptr = new_ptr;  /* safe to update now */
```

## 10.5 `free`

```c
void free(void *ptr);
```

`free(ptr)` releases the memory pointed to by `ptr`. After `free`:
- The memory is returned to the allocator's free pool.
- `ptr` is now a *dangling pointer* — it still holds the old address, but accessing that memory is undefined behavior.
- Calling `free` again on the same pointer (double free) is undefined behavior.
- Calling `free(NULL)` is safe and defined to do nothing.

**Defensive pattern after free:**
```c
free(ptr);
ptr = NULL;   /* prevents accidental use-after-free or double-free */
```

## 10.6 The Allocator Internals

How does `malloc` work? Most implementations maintain a *free list* of available memory blocks. Each block has a header storing its size and status (free or allocated). When you call `malloc(n)`:

1. The allocator searches the free list for a block of at least `n` bytes.
2. If found, it marks it allocated and returns a pointer past the header.
3. If not found, it requests more memory from the OS (`sbrk` or `mmap`).

When you call `free(ptr)`:
1. The allocator uses the header just before `ptr` to find the block's metadata.
2. It marks the block free and possibly merges with adjacent free blocks.

This is why writing before or after an allocated block corrupts allocator metadata and causes crashes in future `malloc`/`free` calls — often far from the actual bug.

```
Heap block layout (conceptual):
┌──────────────────┐
│ size, flags      │  ← allocator header (before the pointer you see)
├──────────────────┤
│ user data        │  ← malloc returns pointer to here
│ (n bytes)        │
├──────────────────┤
│ next block hdr   │
│ ...              │
```

## 10.7 Memory Leaks

A memory leak occurs when allocated memory is never freed. The memory remains owned by the process but is inaccessible because the pointer was lost.

```c
void leaky(void) {
    int *arr = malloc(100 * sizeof(int));
    if (some_condition) return;  /* leak: arr is never freed */
    free(arr);
}
```

Leaks accumulate over time. In long-running programs (servers, daemons), leaks eventually exhaust memory and crash the process.

**Detection:** Valgrind, AddressSanitizer (with `-fsanitize=address`), and LeakSanitizer (with `-fsanitize=leak`) detect leaks.

## 10.8 Use-After-Free

Accessing memory after calling `free` on it is undefined behavior. The memory has been returned to the allocator and may have been reallocated for a different purpose.

```c
int *p = malloc(sizeof(int));
*p = 42;
free(p);
*p = 100;   /* undefined behavior: use-after-free */
printf("%d\n", *p);  /* may print anything, crash, or silently corrupt state */
```

Use-after-free is a severe security vulnerability: an attacker who can control what gets allocated in the freed region can control what the code reads or writes there.

**Detection:** AddressSanitizer detects use-after-free immediately at the point of the bad access.

## 10.9 Double Free

Calling `free` twice on the same pointer corrupts the allocator's free list:

```c
int *p = malloc(sizeof(int));
free(p);
free(p);  /* undefined behavior: double free */
```

Double free often causes a crash in a future `malloc` or `free` call, making the bug hard to locate. It is also exploitable: in some allocator implementations, a double free can be leveraged to execute arbitrary code.

**Prevention:** Set pointers to `NULL` after freeing. `free(NULL)` is safe and does nothing.

## 10.10 Ownership and Lifetime: The Contract

Manual memory management requires a clear ownership model: every allocated object must have exactly one owner — the code responsible for eventually calling `free`.

Design patterns:
- **Caller allocates, callee frees:** Uncommon. Confusing. Avoid.
- **Callee allocates, caller frees:** Common. The function that creates an object returns a pointer; the caller owns it and must free it.
- **Single owner, explicit transfer:** Ownership is transferred via a function call (the function takes the pointer and becomes the new owner).

Document ownership in comments and function signatures:

```c
/* Returns a newly allocated string. Caller must free. */
char *create_greeting(const char *name);

/* Frees the greeting string. After this, the pointer is invalid. */
void free_greeting(char *greeting);
```

## 10.11 Custom Allocators

For performance-critical code, standard `malloc` may be too slow (due to lock contention, fragmentation, or bookkeeping overhead). Custom allocators optimize for specific patterns:

**Arena allocator:** Allocate from a big block; free everything at once. O(1) allocation, O(1) total deallocation. Ideal for request-scoped allocation (web servers, compilers).

```c
typedef struct {
    char *begin;
    char *end;
} Arena;

void *arena_alloc(Arena *a, size_t size, size_t align) {
    /* align the current position */
    uintptr_t ptr = (uintptr_t)a->begin;
    ptr = (ptr + align - 1) & ~(align - 1);
    if ((char *)ptr + size > a->end) return NULL;  /* out of space */
    a->begin = (char *)ptr + size;
    return (char *)ptr;
}
```

**Pool allocator:** Pre-allocate N objects of the same size; recycle them. Zero fragmentation, O(1) alloc and free. Ideal for objects created and destroyed frequently (e.g., network packets, nodes in a tree).

## 10.12 Memory Debugging Tools

**Valgrind Memcheck:**
```bash
valgrind --leak-check=full --track-origins=yes ./myprogram
```
Detects: uninitialized reads, use-after-free, double free, out-of-bounds reads and writes, memory leaks. Slowdown: 10–50×.

**AddressSanitizer:**
```bash
gcc -fsanitize=address -g -o myprogram myprogram.c
./myprogram
```
Detects: buffer overflows, use-after-free, stack buffer overflows, use-after-return. Slowdown: ~2×. Better for CI than Valgrind.

**Combining sanitizers:**
```bash
gcc -fsanitize=address,undefined,leak -g -o myprogram myprogram.c
```

---

## Summary

Dynamic memory allocation (`malloc`, `calloc`, `realloc`, `free`) provides heap memory whose size and lifetime are controlled by the programmer. Every `malloc` must be paired with exactly one `free`. Failure to free causes leaks; freeing twice causes double-free; using after freeing causes use-after-free. All three are undefined behavior and security vulnerabilities. The `realloc` function must be used carefully to avoid leaking the original pointer on failure. Custom allocators (arena, pool) provide performance improvements for specific allocation patterns.

## Key Takeaways

- Always check `malloc`/`calloc`/`realloc` return value for NULL.
- After `free`, set the pointer to NULL to prevent accidental reuse.
- Never free memory you don't own; never free memory twice.
- Keep a clear ownership model: every allocated object has exactly one owner.
- Use `calloc` for zero-initialized arrays and for safe size multiplication.

## Exercises

**Easy:** Write a function that allocates and returns a dynamically sized integer array. Write a corresponding free function. Demonstrate correct usage.

**Medium:** Implement a simple arena allocator with `arena_init(size)`, `arena_alloc(arena, size)`, and `arena_reset(arena)`. Test it by allocating many small objects.

**Hard:** Implement a generic pool allocator for fixed-size objects. Use a free list of recycled objects to achieve O(1) allocation and deallocation. Test with thousands of allocations and frees in random order.


---

# PART III — COMPOUND TYPES AND DATA LAYOUT

---

# Chapter 11 — Structs, Unions, and Bitfields

## 11.1 Structs: Definition and Access

A struct groups related data of possibly different types into a single object.

```c
struct Point {
    double x;
    double y;
};

struct Point p;
p.x = 3.0;
p.y = 4.0;

struct Point p2 = {1.0, 2.0};       /* positional initialization */
struct Point p3 = {.x = 5.0, .y = 6.0};  /* designated initializer (C99) */
```

Members are accessed with `.` (dot) for values and `->` for pointers:

```c
struct Point *pp = &p;
pp->x = 10.0;    /* equivalent to (*pp).x = 10.0 */
```

## 11.2 Struct Memory Layout and Padding

The compiler lays out struct members in declaration order. However, it inserts *padding* bytes to satisfy each member's alignment requirement.

```c
struct Example {
    char   a;    /* 1 byte  at offset 0 */
    /* 3 bytes padding */
    int    b;    /* 4 bytes at offset 4 */
    char   c;    /* 1 byte  at offset 8 */
    /* 7 bytes padding */
    double d;    /* 8 bytes at offset 16 */
};
/* Total: 24 bytes */
```

```
Byte layout of struct Example:
Offset: 0    1    2    3    4    5    6    7    8    9   10   11   12   13   14   15   16...23
        [a] [pad][pad][pad][   b(4 bytes)   ][c] [pad][pad][pad][pad][pad][pad][pad][  d(8 bytes)  ]
```

**Rule:** The size of a struct is always a multiple of the alignment of its largest member. Trailing padding is added to enforce this.

**Minimizing padding by reordering members** (largest to smallest):

```c
struct Efficient {
    double d;    /* 8 bytes at offset 0  */
    int    b;    /* 4 bytes at offset 8  */
    char   a;    /* 1 byte  at offset 12 */
    char   c;    /* 1 byte  at offset 13 */
    /* 2 bytes padding */
};
/* Total: 16 bytes — saves 8 bytes vs Example above */
```

Finding the offset of a member: use `offsetof` from `<stddef.h>`:

```c
#include <stddef.h>
offsetof(struct Example, d)   /* 16 */
offsetof(struct Efficient, b) /* 8 */
```

## 11.3 Struct Operations

```c
/* Assignment: copies all members (shallow copy) */
struct Point a = {1.0, 2.0};
struct Point b = a;    /* b is a complete copy */
b.x = 99.0;            /* does not affect a */

/* Comparison: not supported directly */
if (a == b) { }        /* Error: cannot compare structs with == */
/* Must compare member by member: */
if (a.x == b.x && a.y == b.y) { }
```

## 11.4 Opaque Structs and Forward Declarations

A forward declaration lets you use a pointer to a struct before defining it:

```c
/* header.h */
struct Buffer;           /* forward declaration: Buffer exists, definition elsewhere */
struct Buffer *buffer_create(size_t capacity);
void buffer_free(struct Buffer *buf);
```

Users of this API work with `struct Buffer *` without knowing its internal layout. This is the *opaque pointer pattern* — the primary encapsulation technique in C.

## 11.5 Typedef and Structs

```c
typedef struct {
    double x;
    double y;
} Point;    /* Now 'Point' can be used instead of 'struct Point' */

Point p = {1.0, 2.0};
```

For self-referential structs (linked lists), the tag name is needed:

```c
typedef struct Node {
    int value;
    struct Node *next;   /* can't use 'Node' here yet; must use 'struct Node' */
} Node;
```

## 11.6 Flexible Array Members

A struct may end with a zero-size array member (C99):

```c
struct Message {
    size_t length;
    char   data[];   /* flexible array member */
};

/* Allocate for a message with a specific payload size: */
size_t payload_len = 100;
struct Message *msg = malloc(sizeof(struct Message) + payload_len);
msg->length = payload_len;
memcpy(msg->data, "hello", 5);
```

This pattern allocates the struct and its variable-length data in a single allocation. It's a common technique in protocol implementations.

## 11.7 Unions

A union allocates enough memory for its largest member, and all members share that memory. Reading one member after writing another is called *type punning*.

```c
union Value {
    int    i;
    float  f;
    char   bytes[4];
};

union Value v;
v.i = 42;
printf("%d\n", v.i);      /* 42 */
printf("%02x%02x%02x%02x\n",
       v.bytes[0], v.bytes[1], v.bytes[2], v.bytes[3]);
/* Prints the bytes of int 42 in memory */
```

In C (unlike C++), reading a member other than the one most recently written is defined: you get the bytes of the stored value reinterpreted as the new type. This is explicitly legal in C and is the correct way to type-pun.

## 11.8 Tagged Unions (Discriminated Unions)

Combine a union with an enum tag to create a type-safe variant:

```c
typedef enum { TYPE_INT, TYPE_FLOAT, TYPE_STRING } ValueType;

typedef struct {
    ValueType type;
    union {
        int    i;
        float  f;
        char  *s;
    };   /* anonymous union (C11) */
} Value;

Value v = { .type = TYPE_INT, .i = 42 };
switch (v.type) {
    case TYPE_INT:    printf("int: %d\n", v.i);   break;
    case TYPE_FLOAT:  printf("float: %f\n", v.f); break;
    case TYPE_STRING: printf("str: %s\n", v.s);   break;
}
```

## 11.9 Bitfields

Bitfields pack multiple integer values into a single word, specifying the number of bits each occupies:

```c
struct Flags {
    unsigned int active : 1;    /* 1 bit */
    unsigned int mode   : 3;    /* 3 bits: values 0–7 */
    unsigned int level  : 4;    /* 4 bits: values 0–15 */
};

struct Flags f;
f.active = 1;
f.mode   = 5;
f.level  = 12;
```

Bitfields save memory when packing status flags but have portability issues:
- The order of bits within a word is implementation-defined.
- Whether `int` bitfields are signed or unsigned is implementation-defined.
- Bitfields interact unpredictably with unions and type punning.
- Bitfields cannot be passed to variadic functions or have their address taken.

For portable bit manipulation, prefer explicit bitwise operations on `uint32_t` fields over bitfields.

## 11.10 Struct ABI Stability

Once you publish a struct in a library's public API, you cannot change it without breaking binary compatibility:
- Adding members changes the size.
- Reordering members changes offsets.
- Either breaks code compiled against the old layout.

Techniques for ABI-stable struct evolution:
- Use opaque pointers (hide the struct entirely).
- Reserve padding members that can be repurposed later.
- Version the struct (different names for v1, v2).
- Use a size field and initialize with zeros (new members are zero by default).

---

## Summary

Structs group related data and lay it out contiguously in memory with compiler-inserted padding to satisfy alignment. Reorder members from largest to smallest to minimize padding. Unions share memory across members. Tagged unions combine a type discriminator with a union for safe variant types. Bitfields pack multiple values into a word but have portability hazards. The opaque pointer pattern hides struct internals for encapsulation.

## Key Takeaways

- Struct members are laid out in order with padding for alignment.
- `sizeof(struct)` may be larger than the sum of member sizes due to padding.
- `offsetof(type, member)` gives the byte offset of a member.
- Unions allow reading stored bytes under different types (legal in C).
- Bitfields are implementation-defined in bit ordering — prefer explicit bitwise ops.

## Exercises

**Easy:** Define a `struct Rectangle` with width and height. Write functions to compute area and perimeter. Print the size and member offsets.

**Medium:** Create a tagged union representing a JSON-like value (null, bool, number, string, array pointer). Write a function that prints any value.

**Hard:** Design a `struct` that is exactly 16 bytes on a 64-bit platform, contains two `double` fields, minimizes padding, and verify using `sizeof` and `offsetof`. Then redesign it to be exactly 24 bytes by intentionally introducing padding.

---

# PART IV — SCOPE, LINKAGE, AND MODULAR DESIGN

---

# Chapter 12 — Scope, Linkage, and Storage Duration

## 12.1 The Four Kinds of Scope

**Block scope:** An identifier declared inside a block `{}` is visible from its declaration to the end of that block.

**File scope:** An identifier declared outside any function is visible from its declaration to the end of the translation unit.

**Function scope:** Only labels (`goto` targets) have function scope — visible throughout the function regardless of where declared.

**Function prototype scope:** Parameter names in a function prototype. They are not significant outside the prototype and may be omitted.

## 12.2 Linkage

*Linkage* determines whether two declarations of the same name in different translation units (or different scopes within one) refer to the same object.

**External linkage:** The name refers to the same entity across all translation units. Global functions and non-static global variables have external linkage by default.

**Internal linkage:** The name is visible only within its translation unit. Declared with `static` at file scope.

**No linkage:** Local variables and parameters. Each declaration is a distinct object.

```c
/* file1.c */
int global = 10;          /* external linkage: visible to all TUs */
static int hidden = 20;   /* internal linkage: only visible in file1.c */

void public_func(void) {} /* external linkage */
static void private_func(void) {} /* internal linkage */

void example(void) {
    int local = 30;       /* no linkage: unique to this call */
}
```

## 12.3 `extern` Declarations

`extern` declares that a name is defined elsewhere (in another translation unit or later in this one):

```c
/* In header.h: */
extern int counter;        /* declaration: counter exists, defined somewhere */
extern void increment(void);

/* In counter.c: */
int counter = 0;          /* definition: allocates storage */
void increment(void) { counter++; }

/* In main.c: */
#include "header.h"
int main(void) {
    increment();
    printf("%d\n", counter);
}
```

A declaration can appear many times; a definition must appear exactly once (across all translation units, for external linkage objects).

## 12.4 `static` at Function Scope

A local variable declared `static` has static storage duration: it is initialized once (before the first call) and retains its value between calls.

```c
int counter(void) {
    static int count = 0;   /* initialized once to 0 */
    return ++count;         /* increments and returns each time */
}

printf("%d\n", counter());  /* 1 */
printf("%d\n", counter());  /* 2 */
printf("%d\n", counter());  /* 3 */
```

Static locals are stored in the data or BSS segment, not on the stack. They are not thread-safe without synchronization (since they are shared state).

## 12.5 Global Mutable State: Dangers

Global variables create invisible dependencies between functions. They make code hard to test (you can't isolate a function from the global state), hard to reason about (any function might modify the global), and unsafe in multithreaded code.

Prefer to:
- Pass data as function parameters.
- Return results via return values or output parameters.
- Group related state into structs and pass pointers.
- Use global variables only for true program-wide constants or for OS-interface singletons.

## 12.6 Thread-Local Storage

In multithreaded programs, each thread may need its own copy of a variable. `_Thread_local` (C11) creates a separate instance per thread:

```c
_Thread_local int error_code = 0;   /* each thread has its own error_code */
```

The standard `errno` variable is thread-local on POSIX systems.

---

## Summary

Scope controls where a name is visible. Linkage determines whether the same name in different translation units refers to the same object. External linkage (default for globals and functions) is visible across translation units. Internal linkage (`static` at file scope) is private to the translation unit. Static locals retain values between calls. Minimize global mutable state.

## Key Takeaways

- `static` at file scope = internal linkage (private to translation unit).
- `static` at function scope = static storage duration (persists between calls).
- `extern` declares a name defined elsewhere.
- A name can be declared many times but defined exactly once.
- Global mutable state is dangerous in multithreaded programs.

## Exercises

**Easy:** Create a two-file project with a counter variable in one file. Access it from another using `extern`. Show that `static` on the counter hides it.

**Medium:** Implement a module with private state (a `static` global counter array) and a public API. Show that the internals are inaccessible from other translation units.

**Hard:** Write a thread-safe counter using `_Thread_local` storage. Spawn three threads, each incrementing its local counter 1000 times. Verify that the counters are independent.

---

# Chapter 13 — The Preprocessor and Macros

## 13.1 The Preprocessor's Role

The preprocessor operates on source text before the compiler sees it. It performs:
- File inclusion (`#include`)
- Macro expansion (`#define` and replacement)
- Conditional compilation (`#if`, `#ifdef`, etc.)
- Stringification and token pasting

The preprocessor knows nothing about C types, expressions, or semantics. It manipulates tokens. This power combined with ignorance makes it both useful and dangerous.

## 13.2 `#include` and Include Guards

```c
#include <stdio.h>       /* search system include paths */
#include "myheader.h"    /* search current directory first */
```

Without guards, including a header twice causes duplicate declarations:

```c
/* point.h — protected by an include guard: */
#ifndef POINT_H
#define POINT_H

typedef struct {
    double x, y;
} Point;

double distance(Point a, Point b);

#endif /* POINT_H */
```

The `#pragma once` directive (non-standard but widely supported) is simpler:

```c
#pragma once
/* rest of header */
```

Prefer `#ifndef` guards for portability; `#pragma once` for convenience in non-portable codebases.

## 13.3 Object-Like Macros

```c
#define PI        3.14159265358979
#define MAX_SIZE  1024
#define AUTHOR    "Dennis Ritchie"
```

Object-like macros perform simple text replacement. After preprocessing, every occurrence of `PI` becomes `3.14159265358979`.

Prefer `static const` or `enum` for numeric constants:
- `static const double PI = 3.14159265358979;` — has type, scope, debuggable
- `enum { MAX_SIZE = 1024 };` — integer constant with type

## 13.4 Function-Like Macros

```c
#define MAX(a, b)  ((a) > (b) ? (a) : (b))
#define SQUARE(x)  ((x) * (x))
#define ABS(x)     ((x) < 0 ? -(x) : (x))
```

**Always parenthesize everything:**
```c
/* Without parentheses: */
#define BAD_SQUARE(x)  x * x
int y = BAD_SQUARE(1 + 2);  /* expands to: 1 + 2 * 1 + 2 = 5, not 9! */

/* With parentheses: */
#define SQUARE(x)  ((x) * (x))
int y = SQUARE(1 + 2);  /* expands to: ((1+2) * (1+2)) = 9 */
```

**Double evaluation pitfall:**
```c
int x = 5;
int y = SQUARE(x++);  /* expands to: ((x++) * (x++)) — undefined behavior! */
```

For this reason, prefer inline functions over function-like macros when the language allows:

```c
static inline int square(int x) { return x * x; }  /* no double evaluation */
```

## 13.5 Stringification with `#`

The `#` operator in a macro definition stringifies its argument:

```c
#define STRINGIFY(x)  #x

STRINGIFY(hello)    /* becomes "hello" */
STRINGIFY(3 + 4)    /* becomes "3 + 4" */
```

Useful for assertion macros:

```c
#define ASSERT(cond) \
    do { \
        if (!(cond)) { \
            fprintf(stderr, "Assertion failed: %s at %s:%d\n", \
                    #cond, __FILE__, __LINE__); \
            abort(); \
        } \
    } while (0)
```

## 13.6 Token Pasting with `##`

The `##` operator concatenates two tokens:

```c
#define DECLARE_COUNTER(name) \
    static int counter_##name = 0

DECLARE_COUNTER(requests);  /* declares: static int counter_requests = 0 */
DECLARE_COUNTER(errors);    /* declares: static int counter_errors = 0 */
```

## 13.7 Variadic Macros

```c
#define LOG(fmt, ...)  fprintf(stderr, "[LOG] " fmt "\n", ##__VA_ARGS__)

LOG("Hello");                    /* [LOG] Hello */
LOG("Value: %d", 42);           /* [LOG] Value: 42 */
LOG("Two: %d %s", 2, "items");  /* [LOG] Two: 2 items */
```

`##__VA_ARGS__` is a GCC extension that removes the preceding comma when `__VA_ARGS__` is empty. The C99 standard requires at least one argument for `...`; C23 relaxes this.

## 13.8 Predefined Macros

| Macro | Value |
|---|---|
| `__FILE__` | Current source file name (string literal) |
| `__LINE__` | Current line number (integer) |
| `__func__` | Current function name (C99, string literal) |
| `__DATE__` | Compilation date as "Mmm DD YYYY" |
| `__TIME__` | Compilation time as "HH:MM:SS" |
| `__STDC_VERSION__` | Standard version: 199901L, 201112L, 201710L, 202311L |

## 13.9 Conditional Compilation

```c
#ifdef DEBUG
    #define DLOG(msg) fprintf(stderr, "DEBUG: %s\n", msg)
#else
    #define DLOG(msg) /* nothing */
#endif

#if defined(__linux__)
    /* Linux-specific code */
#elif defined(__APPLE__)
    /* macOS-specific code */
#elif defined(_WIN32)
    /* Windows-specific code */
#else
    #error "Unsupported platform"
#endif

#if __STDC_VERSION__ >= 202311L
    /* C23 features available */
#endif
```

## 13.10 X-Macros

The X-macro pattern uses a single source of truth for a list, applying different actions each time:

```c
/* Define the list once: */
#define COLORS \
    X(RED,   "red",   0xFF0000) \
    X(GREEN, "green", 0x00FF00) \
    X(BLUE,  "blue",  0x0000FF)

/* Generate an enum: */
typedef enum {
#define X(id, name, hex) id,
    COLORS
#undef X
    COLOR_COUNT
} Color;

/* Generate a name lookup table: */
const char *color_names[] = {
#define X(id, name, hex) name,
    COLORS
#undef X
};

/* Generate a hex value table: */
int color_hex[] = {
#define X(id, name, hex) hex,
    COLORS
#undef X
};
```

This ensures the enum, name table, and hex table stay synchronized. Adding a new color in one place automatically updates all three.

## 13.11 `_Static_assert`

Compile-time assertions that produce a clear error message:

```c
_Static_assert(sizeof(int) == 4, "This code requires 32-bit int");
_Static_assert(sizeof(void *) == 8, "This code requires 64-bit pointers");
_Static_assert(offsetof(struct Header, type) == 0, "type must be first member");
```

In C23, `static_assert` is the preferred spelling (it's a keyword, not a macro).

## 13.12 When to Use Macros vs. Alternatives

| Use case | Prefer |
|---|---|
| Integer constants | `enum` or `static const` |
| Floating-point constants | `static const double` |
| Simple inline computations | `static inline` function |
| Generic code (type-independent) | Macros (or `_Generic` in C11) |
| Compile-time string operations | Macros |
| Code generation (X-macros) | Macros |
| Debug logging with file/line | Macros (because `__FILE__`, `__LINE__`) |
| Conditional compilation | Macros (no alternative) |

---

## Summary

The preprocessor transforms source text before compilation. `#define` creates object-like and function-like macros. Function-like macros require aggressive parenthesization and avoid double evaluation. `#` stringifies arguments; `##` pastes tokens. Conditional compilation selects code based on compile-time conditions. X-macros maintain synchronized tables. `_Static_assert` catches errors at compile time. Prefer `static inline` functions over macros when possible.

## Key Takeaways

- Macro expansion is textual: parenthesize all arguments and the whole expression.
- Function-like macros evaluate arguments multiple times — avoid side effects in arguments.
- Include guards or `#pragma once` prevent multiple inclusion.
- X-macros maintain a single source of truth for related data tables.
- Prefer `_Static_assert` over runtime assertions for compile-time checkable conditions.

## Exercises

**Easy:** Write an `ARRAY_SIZE(arr)` macro that returns the number of elements in an array. Verify it works for different array types.

**Medium:** Use X-macros to generate both an enum and a `const char *` name array for the days of the week. Write a `day_name(Day d)` function that uses the table.

**Hard:** Write a `SAFE_CAST(value, from_type, to_type)` macro that uses `_Static_assert` to verify that `sizeof(from_type) >= sizeof(to_type)` at compile time. Demonstrate it catching an unsafe cast.

---

# Chapter 14 — Header Files and Modular Design

## 14.1 The Role of Header Files

Header files contain *declarations* that are shared across multiple translation units. They allow the compiler to understand function signatures, struct layouts, and constant values when compiling any file that includes them.

A header file should contain:
- Type definitions (`typedef`, `struct` definitions, `enum` definitions)
- Function prototypes
- Macro definitions
- `extern` variable declarations (not definitions)
- `static inline` function definitions
- `_Static_assert` checks on types

A header file should NOT contain:
- Non-`inline`, non-`static` function definitions (would cause multiple definition errors if included in multiple TUs)
- Variable definitions without `extern` (same problem)
- Any code that allocates resources

## 14.2 Self-Contained Headers

A header should be able to be included in any order without depending on other headers having been included first. This means a header must include everything it needs:

```c
/* Bad: queue.h assumes stdio.h was included before it */
struct Queue { FILE *log; ... };

/* Good: queue.h includes what it needs */
#include <stdio.h>
struct Queue { FILE *log; ... };
```

Test self-containment by creating a `.c` file that includes only your header as its first line and compiles without errors.

## 14.3 The Opaque Pointer Pattern

The most powerful encapsulation technique in C:

```c
/* buffer.h — public API */
#ifndef BUFFER_H
#define BUFFER_H
#include <stddef.h>

typedef struct Buffer Buffer;   /* forward declaration only */

Buffer *buffer_create(size_t capacity);
int     buffer_append(Buffer *b, const char *data, size_t len);
size_t  buffer_size(const Buffer *b);
const char *buffer_data(const Buffer *b);
void    buffer_free(Buffer *b);

#endif
```

```c
/* buffer.c — implementation (internal struct definition) */
#include "buffer.h"
#include <stdlib.h>
#include <string.h>

struct Buffer {        /* only buffer.c sees this */
    char  *data;
    size_t size;
    size_t capacity;
};

Buffer *buffer_create(size_t capacity) {
    Buffer *b = malloc(sizeof(Buffer));
    if (!b) return NULL;
    b->data = malloc(capacity);
    if (!b->data) { free(b); return NULL; }
    b->size = 0;
    b->capacity = capacity;
    return b;
}
```

Users of `Buffer` cannot access internal fields — they work only through the API. This means the internal representation can change without breaking users, as long as the API stays the same.

## 14.4 API Design Principles

A good C API is:

- **Minimal:** Expose only what users need. Hidden implementation details can change freely.
- **Consistent:** Naming conventions, parameter order, and error handling should be uniform throughout.
- **Complete:** The API must support everything legitimate users need, or they will work around it unsafely.
- **Easy to use correctly, hard to use incorrectly:** The types and signatures should guide correct use.

**Naming conventions (common patterns):**

```c
/* Library prefix + noun + verb: */
buffer_create()
buffer_append()
buffer_free()

/* Or prefix_verb_noun: */
create_buffer()
append_to_buffer()
free_buffer()

/* Constants: ALL_CAPS with prefix: */
BUFFER_MAX_SIZE
BUFFER_DEFAULT_CAPACITY
```

## 14.5 Circular Dependencies and Resolution

If `a.h` includes `b.h` and `b.h` includes `a.h`, you have a circular include dependency. Resolution strategies:

1. **Forward declarations:** Instead of including `b.h` in `a.h`, just forward-declare the types you need: `struct B;`. Use `B *` (pointer) in your declarations — you only need the full definition if you're dereferencing or using members.

2. **Dependency inversion:** Factor out a common base header with shared types that both A and B include.

---

## Summary

Header files share declarations across translation units. They should be self-contained and protected by include guards. The opaque pointer pattern hides implementation details for encapsulation. Good API design is minimal, consistent, and difficult to misuse. Circular dependencies are resolved with forward declarations.

## Exercises

**Easy:** Create a `stack.h` and `stack.c` implementing an integer stack with push, pop, peek, and size functions. Use the opaque pointer pattern.

**Hard:** Design an API for a generic key-value store that can hold values of any type (using `void *`). Document the ownership rules: who frees the keys and values.

---

# Chapter 15 — Compilation and Linking in Depth

## 15.1 Object File Anatomy (ELF)

On Linux, object files and executables use the ELF (Executable and Linkable Format). Key sections:

| Section | Purpose |
|---|---|
| `.text` | Compiled machine code |
| `.data` | Initialized global/static variables |
| `.bss` | Uninitialized global/static variables (just a size in the file) |
| `.rodata` | Read-only data: string literals, `const` globals |
| `.symtab` | Symbol table (for debugging) |
| `.strtab` | String table (symbol names) |
| `.rel.text` / `.rela.text` | Relocation entries for code |
| `.debug_*` | DWARF debugging information |

Examine object files:
```bash
nm hello.o              # Symbol table
objdump -d hello.o      # Disassemble .text section
objdump -s -j .rodata hello.o  # Dump .rodata contents
readelf -a hello.o      # Full ELF information
```

## 15.2 Symbol Visibility

Symbols (global names) have *binding* and *type* attributes:

- `GLOBAL`: visible to the linker (external linkage)
- `LOCAL`: internal (static linkage)
- `WEAK`: like global but overridable

```c
int global_var = 42;            /* GLOBAL symbol in .data */
static int local_var = 0;       /* LOCAL symbol in .data */
int __attribute__((weak)) opt = 0;  /* WEAK symbol */
```

Weak symbols: if a strong definition exists anywhere, it wins. If no strong definition exists, the weak definition is used. Useful for optional overrides (e.g., a `malloc` replacement).

## 15.3 Static Libraries

A static library is an archive of object files:

```bash
ar rcs libmylib.a module1.o module2.o module3.o
```

Linking against a static library:
```bash
gcc main.o -L. -lmylib -o myprogram
```

The linker extracts only the object files from the archive that contain symbols needed by the program. Order matters: a library must come *after* the object files that use it on the command line.

## 15.4 Dynamic Libraries

```bash
gcc -fPIC -shared -o libmylib.so module1.c module2.c
gcc main.c -L. -lmylib -o myprogram
```

`-fPIC`: Position-Independent Code — required so the library can be loaded at any address (because ASLR randomizes loading addresses).

At runtime, the dynamic linker (`ld.so` on Linux) resolves library locations using:
- `LD_LIBRARY_PATH` environment variable
- `rpath` embedded in the executable
- `/etc/ld.so.conf` and the default system library paths

```bash
ldd myprogram          # Show dynamic library dependencies and resolved paths
```

## 15.5 Common Linker Errors

**Undefined reference:**
```
undefined reference to `my_function'
```
Cause: `my_function` is declared but never defined, or the object file/library containing it was not provided to the linker.

Fix: Add the object file or `-llib` flag.

**Multiple definition:**
```
multiple definition of `counter'
```
Cause: A variable or function with external linkage is defined in more than one translation unit.

Fix: Make it `static`, or move the definition to one file and use `extern` declarations in others.

**Link order issues:**
Object files must come before the libraries they depend on in the linker command. The linker resolves symbols in one left-to-right pass. If `main.o` references symbols from `libfoo.a`, you need:
```bash
gcc main.o -lfoo    # correct
gcc -lfoo main.o    # wrong: libfoo is scanned before main.o needs it
```

## 15.6 Link-Time Optimization (LTO)

With LTO, the compiler saves its IR (intermediate representation) in the object files. The linker can then optimize across translation units:

```bash
gcc -O2 -flto -c module1.c -o module1.o
gcc -O2 -flto -c module2.c -o module2.o
gcc -O2 -flto module1.o module2.o -o myprogram
```

LTO enables inlining across file boundaries, dead code elimination of unused exported functions, and cross-module constant propagation. Compile times increase significantly.

---

## Summary

Object files contain code, data, and symbols. The linker resolves symbols, relocates addresses, and combines object files into an executable. Static libraries embed code; dynamic libraries are resolved at runtime. LTO enables cross-module optimization. Common linker errors are undefined references (missing definitions) and multiple definitions (duplicate globals).

## Exercises

**Easy:** Using `nm` and `objdump`, examine an object file from one of your programs. Identify text, data, and BSS symbols. Find a string literal in `.rodata`.

**Hard:** Build a small dynamic library (.so) and a program that loads it using `dlopen`/`dlsym` at runtime (instead of at link time). Implement a simple plugin system where the program calls a function whose implementation is selected by loading a plugin at startup.

---

# PART V — THE STANDARD LIBRARY

---

# Chapter 16 — The Standard Library: Architecture and Key Headers

## 16.1 Overview

The C standard library is a set of functions, types, and macros defined by the ISO C standard. Every conforming C implementation provides it. It is divided into headers, each covering a functional area.

The key headers:

| Header | Contents |
|---|---|
| `<stdio.h>` | Input/output |
| `<stdlib.h>` | Memory, conversion, process control |
| `<string.h>` | String and memory operations |
| `<math.h>` | Mathematical functions |
| `<time.h>` | Time and date |
| `<stdint.h>` | Fixed-width integer types |
| `<stddef.h>` | `size_t`, `ptrdiff_t`, `NULL`, `offsetof` |
| `<stdbool.h>` | `bool`, `true`, `false` |
| `<limits.h>` | Integer type limits |
| `<float.h>` | Floating-point limits |
| `<errno.h>` | Error codes |
| `<assert.h>` | `assert` macro |
| `<signal.h>` | Signal handling |
| `<setjmp.h>` | Non-local jumps |
| `<stdarg.h>` | Variadic argument access |
| `<threads.h>` | Threading (C11) |
| `<stdatomic.h>` | Atomic operations (C11) |

## 16.2 `errno` and Error Handling

Many standard library functions signal errors by returning a sentinel value (typically -1 or NULL) and setting the global `errno` to an error code.

```c
#include <errno.h>
#include <string.h>  /* for strerror */

FILE *f = fopen("nonexistent.txt", "r");
if (f == NULL) {
    /* errno is set by fopen */
    fprintf(stderr, "Error opening file: %s\n", strerror(errno));
    /* or: */
    perror("fopen");  /* prints: "fopen: No such file or directory" */
}
```

`errno` must be checked *immediately* after the function that sets it — the next function call might change `errno`.

`errno` is thread-local on POSIX systems (each thread has its own `errno`).

Common `errno` values:
- `ENOMEM` — not enough memory
- `EINVAL` — invalid argument
- `ENOENT` — no such file or directory
- `EACCES` — permission denied
- `EEXIST` — file already exists
- `EAGAIN` / `EWOULDBLOCK` — resource temporarily unavailable

## 16.3 `<assert.h>`: Assertions

```c
#include <assert.h>

void divide(int a, int b) {
    assert(b != 0);    /* crashes with a message if b is 0 */
    return a / b;
}
```

If `NDEBUG` is defined (typically in release builds with `-DNDEBUG`), all `assert` calls are removed. Assertions check invariants — conditions that should always hold if the program is correct. Do not use them for runtime error handling (user input, file not found, malloc failure) — those can occur in correct programs.

---

# Chapter 17 — Input/Output: `<stdio.h>`

## 17.1 The Stdio Model

Standard I/O wraps file descriptors with buffering. A `FILE *` is a handle to an I/O stream. Three streams are available without opening:

- `stdin` — standard input (file descriptor 0)
- `stdout` — standard output (file descriptor 1)
- `stderr` — standard error (file descriptor 2)

**Buffering modes:**
- *Fully buffered:* Data is written when the buffer fills or `fflush` is called. Default for files.
- *Line buffered:* Data is written on newline. Default for `stdout` when connected to a terminal.
- *Unbuffered:* Data is written immediately. Default for `stderr`.

## 17.2 `printf` Format Specifiers

```c
printf("%d", 42);          /* decimal integer */
printf("%i", 42);          /* same as %d */
printf("%u", 42u);         /* unsigned decimal */
printf("%o", 42);          /* octal */
printf("%x", 42);          /* hex lowercase */
printf("%X", 42);          /* hex uppercase */
printf("%f", 3.14);        /* floating point, 6 decimals */
printf("%e", 3.14);        /* scientific notation */
printf("%g", 3.14);        /* shorter of %f/%e */
printf("%s", "hello");     /* string */
printf("%c", 'A');         /* character */
printf("%p", ptr);         /* pointer */
printf("%%");              /* literal % */

/* Width and precision: */
printf("%10d", 42);        /* right-justified in 10 chars */
printf("%-10d", 42);       /* left-justified */
printf("%010d", 42);       /* zero-padded */
printf("%.5f", 3.14);      /* 5 decimal places */
printf("%8.3f", 3.14);     /* width 8, 3 decimal places */

/* Length modifiers: */
printf("%ld", (long)n);         /* long */
printf("%lld", (long long)n);   /* long long */
printf("%zu", (size_t)n);       /* size_t */
printf("%td", (ptrdiff_t)n);    /* ptrdiff_t */
printf("%jd", (intmax_t)n);     /* intmax_t */
```

## 17.3 Format String Vulnerabilities

Format string attacks occur when user-supplied data is used as the format string:

```c
/* DANGEROUS: */
printf(user_input);           /* if user_input is "%x%x%x", reads stack! */
fprintf(log, user_input);     /* same problem */

/* SAFE: */
printf("%s", user_input);     /* user_input is data, not format */
```

A malicious user can use `%x` to read stack values, `%n` to write to arbitrary addresses. Always use a literal format string with `%s` for user-controlled strings.

## 17.4 File Operations

```c
FILE *f = fopen("data.txt", "r");   /* open for reading */
if (f == NULL) { perror("fopen"); exit(1); }

/* Reading: */
int c = fgetc(f);               /* read one character (-1 on EOF) */
char line[256];
fgets(line, sizeof(line), f);   /* read a line (safe) */

/* Never use: */
gets(buf);  /* removed in C11: no bounds checking */

/* Writing: */
FILE *out = fopen("out.txt", "w");
fprintf(out, "Value: %d\n", 42);
fwrite(data, sizeof(char), len, out);

/* Seek: */
fseek(f, 0, SEEK_SET);    /* go to beginning */
fseek(f, 0, SEEK_END);    /* go to end */
long pos = ftell(f);      /* current position */

/* Flush and close: */
fflush(out);   /* ensure all data is written */
fclose(f);
fclose(out);
```

**File open modes:**

| Mode | Description |
|---|---|
| `"r"` | Read. File must exist. |
| `"w"` | Write. Creates or truncates. |
| `"a"` | Append. Creates if needed. |
| `"r+"` | Read and write. File must exist. |
| `"w+"` | Read and write. Creates or truncates. |
| `"rb"` | Read binary. |
| `"wb"` | Write binary. |

## 17.5 `snprintf`: Safe Formatted Strings

```c
char buf[64];
snprintf(buf, sizeof(buf), "Hello, %s! You are %d years old.", name, age);
/* buf is always null-terminated; truncates if too long */
```

`snprintf` is always preferred over `sprintf`. The `n` parameter specifies the buffer size including the null terminator, so `snprintf(buf, n, ...)` never writes more than `n` bytes.

## 17.6 Error Handling with stdio

```c
if (ferror(f)) {
    /* I/O error occurred */
    clearerr(f);
}
if (feof(f)) {
    /* Reached end of file */
}
```

---

# Chapter 18 — String, Memory, and Utility Libraries

## 18.1 Safe Number Parsing

Never use `atoi`, `atof`, `atol` for user input — they have no error detection:

```c
/* Wrong: */
int n = atoi(str);  /* returns 0 for non-numeric strings, no error */

/* Correct: */
char *end;
errno = 0;
long n = strtol(str, &end, 10);
if (errno != 0) { /* range error */ }
if (end == str)  { /* no digits */ }
if (*end != '\0') { /* trailing garbage */ }
```

`strtol(str, &end, base)` converts `str` to a long in the given base, storing the first unconverted character in `*end`.

## 18.2 `qsort` and `bsearch`

```c
#include <stdlib.h>

int cmp_int(const void *a, const void *b) {
    int ia = *(const int *)a;
    int ib = *(const int *)b;
    return (ia > ib) - (ia < ib);  /* safe: avoids subtraction overflow */
}

int arr[] = {5, 2, 8, 1, 9, 3};
qsort(arr, 6, sizeof(int), cmp_int);

int key = 8;
int *found = bsearch(&key, arr, 6, sizeof(int), cmp_int);
if (found) printf("Found: %d\n", *found);
```

**The comparator pattern:** `(a > b) - (a < b)` returns -1, 0, or 1 safely. Never use `a - b` — it overflows for extreme values.

## 18.3 `memcpy` vs `memmove`

```c
/* memcpy: fast, but src and dst must not overlap */
memcpy(dst, src, n);

/* memmove: handles overlapping regions correctly */
memmove(dst, src, n);   /* safe when regions overlap */
```

When in doubt, use `memmove`. In practice, modern compilers and processors make them equally fast for non-overlapping regions.

## 18.4 `exit`, `abort`, and Cleanup

```c
exit(0);        /* normal termination: flushes stdio buffers, calls atexit handlers */
exit(1);        /* error termination */
_Exit(0);       /* immediate termination: no cleanup */
abort();        /* abnormal termination: sends SIGABRT, produces core dump */

/* Register cleanup functions: */
atexit(cleanup_function);   /* called in LIFO order by exit() */
```

---

# Chapter 19 — Time, Signals, and Other Standard Headers

## 19.1 Time (`<time.h>`)

```c
#include <time.h>

time_t now = time(NULL);        /* current time as seconds since epoch */
struct tm *local = localtime(&now);  /* convert to local time */
printf("Year: %d, Month: %d\n", local->tm_year + 1900, local->tm_mon + 1);

/* Formatting: */
char buf[64];
strftime(buf, sizeof(buf), "%Y-%m-%d %H:%M:%S", local);
printf("%s\n", buf);

/* High-resolution timing: */
struct timespec ts1, ts2;
clock_gettime(CLOCK_MONOTONIC, &ts1);
/* ... work ... */
clock_gettime(CLOCK_MONOTONIC, &ts2);
double elapsed = (ts2.tv_sec - ts1.tv_sec) +
                 (ts2.tv_nsec - ts1.tv_nsec) * 1e-9;
printf("Elapsed: %.6f seconds\n", elapsed);
```

Always use `CLOCK_MONOTONIC` for measuring elapsed time — it doesn't jump on NTP adjustments or clock changes. Use `CLOCK_REALTIME` only for wall-clock time.

## 19.2 `setjmp` / `longjmp`

Non-local jumps: jump out of deeply nested function calls without unwinding normally.

```c
#include <setjmp.h>

jmp_buf env;

void deep_function(void) {
    /* some error occurs */
    longjmp(env, 1);   /* jump back to setjmp, returning 1 */
}

int main(void) {
    int rc = setjmp(env);   /* first call: returns 0 */
    if (rc == 0) {
        deep_function();    /* this eventually calls longjmp */
    } else {
        printf("Caught error: %d\n", rc);  /* execution resumes here */
    }
}
```

`longjmp` does not call destructors or local cleanup. Use it carefully and document it clearly. Avoid using it across threads. It is not a substitute for exception handling.

## 19.3 `<ctype.h>`: Character Classification

```c
#include <ctype.h>

isalpha(c)    /* letter */
isdigit(c)    /* digit */
isalnum(c)    /* letter or digit */
isspace(c)    /* whitespace */
isupper(c)    /* uppercase letter */
islower(c)    /* lowercase letter */
toupper(c)    /* convert to uppercase */
tolower(c)    /* convert to lowercase */
isprint(c)    /* printable character */
```

**Pitfall:** These functions take `int`, not `char`. Passing a `signed char` with a value > 127 (negative if signed) causes undefined behavior. Always cast to `unsigned char` first:

```c
if (isdigit((unsigned char)c)) { ... }
```

---

# PART VI — ERROR HANDLING AND DEFENSIVE PROGRAMMING

---

# Chapter 20 — Error Handling Strategies

## 20.1 The C Error Philosophy

C has no exceptions. Errors are values — functions signal failure by returning a special value (typically -1, NULL, or a nonzero status code) and may set `errno`.

This requires discipline: you must check every function that can fail. Unchecked errors lead to programs that silently continue in corrupted state.

## 20.2 Return-Code Pattern

The dominant pattern in C:

```c
/* Return 0 for success, negative for error: */
int read_file(const char *path, char **out, size_t *out_len) {
    FILE *f = fopen(path, "rb");
    if (!f) return -1;
    
    if (fseek(f, 0, SEEK_END) != 0) { fclose(f); return -2; }
    long len = ftell(f);
    if (len < 0)               { fclose(f); return -3; }
    if (fseek(f, 0, SEEK_SET) != 0) { fclose(f); return -4; }
    
    char *buf = malloc((size_t)len + 1);
    if (!buf)                  { fclose(f); return -5; }
    
    if (fread(buf, 1, (size_t)len, f) != (size_t)len) {
        free(buf); fclose(f); return -6;
    }
    
    buf[len] = '\0';
    fclose(f);
    *out = buf;
    *out_len = (size_t)len;
    return 0;
}
```

This is verbose. The `goto` cleanup pattern is more maintainable:

```c
int read_file(const char *path, char **out, size_t *out_len) {
    int rc = 0;
    FILE *f = NULL;
    char *buf = NULL;
    
    f = fopen(path, "rb");
    if (!f) { rc = -1; goto done; }
    
    if (fseek(f, 0, SEEK_END) != 0) { rc = -2; goto done; }
    long len = ftell(f);
    if (len < 0)               { rc = -3; goto done; }
    if (fseek(f, 0, SEEK_SET) != 0) { rc = -4; goto done; }
    
    buf = malloc((size_t)len + 1);
    if (!buf) { rc = -5; goto done; }
    
    if (fread(buf, 1, (size_t)len, f) != (size_t)len) { rc = -6; goto done; }
    
    buf[len] = '\0';
    *out = buf;
    buf = NULL;    /* transfer ownership: don't free it below */
    *out_len = (size_t)len;

done:
    free(buf);     /* NULL if ownership was transferred; free(NULL) is safe */
    if (f) fclose(f);
    return rc;
}
```

## 20.3 Defensive Programming

Write code that verifies its preconditions:

```c
void process(int *arr, size_t n) {
    assert(arr != NULL);   /* catches bugs during development */
    assert(n > 0);
    /* ... */
}
```

In production code (where `NDEBUG` is defined and `assert` is disabled), add explicit checks for external inputs:

```c
int set_timeout(int milliseconds) {
    if (milliseconds < 0 || milliseconds > 60000) {
        return EINVAL;  /* reject invalid input */
    }
    /* ... */
    return 0;
}
```

## 20.4 Assertions in Production

Even with `NDEBUG`, some invariants are critical enough to check always:

```c
#define REQUIRE(cond) \
    do { \
        if (!(cond)) { \
            fprintf(stderr, "Requirement failed: %s at %s:%d in %s\n", \
                    #cond, __FILE__, __LINE__, __func__); \
            abort(); \
        } \
    } while (0)

void push(Stack *s, int value) {
    REQUIRE(s != NULL);
    REQUIRE(s->size < s->capacity);
    s->data[s->size++] = value;
}
```

## 20.5 Logging

A minimal thread-safe logging infrastructure:

```c
#include <stdio.h>
#include <time.h>
#include <stdarg.h>

typedef enum { LOG_DEBUG, LOG_INFO, LOG_WARN, LOG_ERROR } LogLevel;

static LogLevel g_log_level = LOG_INFO;

void log_message(LogLevel level, const char *file, int line, const char *fmt, ...) {
    if (level < g_log_level) return;
    
    const char *level_str[] = {"DEBUG", "INFO", "WARN", "ERROR"};
    struct timespec ts;
    clock_gettime(CLOCK_REALTIME, &ts);
    
    flockfile(stderr);
    fprintf(stderr, "[%ld.%03ld] [%s] %s:%d: ",
            ts.tv_sec, ts.tv_nsec / 1000000,
            level_str[level], file, line);
    va_list args;
    va_start(args, fmt);
    vfprintf(stderr, fmt, args);
    va_end(args);
    fprintf(stderr, "\n");
    funlockfile(stderr);
}

#define LOG_DEBUG(...)  log_message(LOG_DEBUG, __FILE__, __LINE__, __VA_ARGS__)
#define LOG_INFO(...)   log_message(LOG_INFO,  __FILE__, __LINE__, __VA_ARGS__)
#define LOG_WARN(...)   log_message(LOG_WARN,  __FILE__, __LINE__, __VA_ARGS__)
#define LOG_ERROR(...)  log_message(LOG_ERROR, __FILE__, __LINE__, __VA_ARGS__)
```

---

## Summary

C error handling uses return codes, `errno`, and explicit cleanup. The `goto`-cleanup pattern is idiomatic for functions with multiple resources. Assertions check invariants. Defensive programming validates inputs. Logging captures program behavior for diagnosis.

## Key Takeaways

- Check every function that can fail — unchecked errors are bugs.
- The goto-cleanup pattern handles multiple resources without leaking.
- Use `assert` for invariants (disabled in release); use explicit checks for external input.
- `errno` must be checked immediately after a failing call.

## Exercises

**Easy:** Write a `read_int_from_file` function that opens a file, reads one integer, and closes the file, with proper error checking and cleanup at every step.

**Hard:** Design an error type (`typedef struct { int code; const char *message; } Error`) and a set of functions that propagate errors across multiple layers. Implement a function that reads a CSV file, parses it, and reports the first error with file/line context.


---

# PART VII — UNDEFINED BEHAVIOR: THE COMPLETE GUIDE

---

# Chapter 21 — Undefined Behavior: Theory and Catalog

## 21.1 What Undefined Behavior Means — Precisely

The C standard uses three categories to characterize behavior not fully specified:

**Undefined behavior (UB):** The standard imposes no requirements. The compiler may do anything: crash the program, silently produce wrong results, delete data, or — on a conforming implementation — summon the nasal demons of folklore. Importantly, the compiler may *assume* UB does not occur, and optimize based on that assumption.

**Unspecified behavior:** The standard allows several outcomes, but they are all valid. The program is correct; you just can't predict which outcome you get. Example: the order of evaluation of function arguments.

**Implementation-defined behavior:** The standard allows the implementation to choose, but requires the implementation to document its choice. Example: the size of `int`.

Understanding UB is not academic. It is the single most important topic for writing correct C.

## 21.2 The Compiler's License to Optimize via UB

The key insight: **the C standard defines behavior relative to the abstract machine. If your program invokes undefined behavior, it is not a valid C program, and the compiler is not required to produce any particular result.**

Compilers actively use UB as an optimization license:

```c
/* This code: */
int f(int x) {
    return x + 1 > x;    /* signed overflow is UB */
}

/* An optimizing compiler may produce: */
int f(int x) {
    return 1;   /* "x+1 > x is always true IF there's no overflow,
                    and overflow is UB, so we can assume no overflow,
                    so this is always true" */
}
```

This is not a bug in the compiler. It is correct behavior according to the standard. The bug is in the C code.

## 21.3 UB Catalog

### UB 1: Signed Integer Overflow

```c
int x = INT_MAX;
int y = x + 1;      /* undefined behavior */
```

Safe alternatives:
```c
/* Detect before adding: */
if (x > INT_MAX - 1) { /* handle overflow */ }

/* C23: */
int result;
if (ckd_add(&result, x, 1)) { /* overflow detected */ }

/* Use unsigned arithmetic: */
unsigned int ux = (unsigned int)x;
unsigned int uy = ux + 1;   /* defined: wraps */
```

### UB 2: Null Pointer Dereference

```c
int *p = NULL;
*p = 42;     /* undefined behavior */
p->field;    /* undefined behavior */
```

Compilers may assume pointers are non-null. If you write:
```c
void f(int *p) {
    *p = 42;     /* compiler deduces: p is not null (or UB occurs) */
    if (p) { }   /* compiler may eliminate this check! */
}
```

### UB 3: Out-of-Bounds Array Access

```c
int arr[5];
arr[5] = 1;    /* one past end: UB */
arr[-1] = 1;   /* UB */
int *p = arr + 10;  /* beyond the array: UB even without dereferencing (except arr+5 which is legal) */
```

### UB 4: Use-After-Free

```c
int *p = malloc(sizeof(int));
free(p);
*p = 42;     /* undefined behavior */
```

### UB 5: Uninitialized Variables (Automatic Storage)

```c
int x;
printf("%d\n", x);   /* undefined behavior */
```

Note: global and static variables are zero-initialized. Only automatic (local) variables are uninitialized.

### UB 6: Strict Aliasing Violations

```c
int i = 0x3f800000;   /* IEEE 754 representation of 1.0f */
float f = *(float *)&i;  /* UB: accessing int object through float* */

/* Correct: */
float f;
memcpy(&f, &i, 4);   /* defined behavior */
```

### UB 7: Shift Operator Violations

```c
int x = 1;
int y = x << 32;    /* UB if int is 32 bits: shift count >= width */
int z = x << -1;    /* UB: negative shift count */
int w = -1 << 1;    /* UB: left-shift of negative value (C99/C11) */
```

Safe shifting:
```c
uint32_t x = 1u;
uint32_t y = x << 31;   /* defined: 2147483648 */
```

### UB 8: Sequence Point Violations (Pre-C11)

```c
int i = 0;
i = i++;       /* UB in C99: modifying i and using i between sequence points */
a[i] = i++;   /* UB */
f(i++, i++);  /* UB: order of argument evaluation is unspecified */
```

C11 replaced sequence points with a *sequenced-before* ordering relation. The expressions above are still UB in C11, though the rules are stated more precisely.

### UB 9: Data Races (C11)

```c
int counter = 0;  /* shared between threads */
/* Thread 1: */ counter++;  /* UB without synchronization */
/* Thread 2: */ counter++;  /* data race */
```

Use `_Atomic int counter` or protect with a mutex.

### UB 10: Calling a Function Through an Incompatible Pointer

```c
void f(int x) { }
void (*p)(double) = (void (*)(double))f;
p(3.14);   /* UB: calling f with wrong signature */
```

### UB 11: Modifying a String Literal

```c
char *s = "hello";
s[0] = 'H';   /* UB: string literals are read-only */
```

### UB 12: Integer Conversion Out of Range

```c
unsigned char x = 300;   /* 300 > UCHAR_MAX (255): actually defined (wraps to 44) */
signed char y = 200;     /* 200 > SCHAR_MAX (127): implementation-defined in C99, UB if overflow */
```

Note: Converting a value to an unsigned type is always defined (wraps). Converting to a signed type that cannot represent the value is implementation-defined in C99/C11 and implementation-defined (but required to be the same for all values) in C23.

## 21.4 Detecting and Preventing UB

**Compile-time tools:**

```bash
gcc -Wall -Wextra -Wundefined-behavior -fsanitize=undefined
clang -fsanitize=undefined,address,integer
```

**Runtime sanitizers:**

```bash
# UBSan: undefined behavior sanitizer
gcc -fsanitize=undefined -g
# ASan: address sanitizer (out-of-bounds, use-after-free)
gcc -fsanitize=address -g
# MSan: memory sanitizer (uninitialized reads, Clang only)
clang -fsanitize=memory -g
```

**Static analysis:**

```bash
scan-build gcc -c myfile.c   # Clang static analyzer
cppcheck --enable=all myfile.c
```

## 21.5 UB and Compiler Optimizations: Real Cases

**Case 1: Loop counter optimization**

```c
for (int i = 0; i <= INT_MAX; i++) {
    /* ... */
}
```

The compiler may optimize this to an infinite loop: since `i <= INT_MAX` is always true if overflow is UB, and overflow is UB, the loop never terminates. This is not a compiler bug.

**Case 2: Null check elimination**

```c
int *p = get_pointer();
*p = 42;          /* compiler: p must not be null here */
if (p != NULL) {  /* compiler: this is always true — eliminate the check */
    process(p);
}
```

**Case 3: Strict aliasing optimization**

```c
void update(int *a, float *b) {
    *a = 1;
    *b = 2.0f;
    return *a;   /* compiler: *a is still 1 (a and b cannot alias) */
}
```

The compiler returns 1 without re-reading `*a`. If `a` and `b` actually alias (same address, illegal under strict aliasing), the function returns 1 but the actual memory contains the representation of `2.0f` — silently wrong.

---

## Summary

Undefined behavior is not a runtime concept — it is a semantic category. If your code invokes UB, the C standard makes no guarantees about anything in your program. Compilers use UB as an optimization license: they assume UB never occurs, and they optimize based on that assumption. The 12 UB categories cataloged here cover the vast majority of real bugs. Sanitizers are the most effective runtime detection tool.

## Key Takeaways

- UB is not "implementation-defined" — it is genuinely undefined, and compilers exploit it.
- Signed overflow, out-of-bounds access, use-after-free, and strict aliasing are the most common UB.
- Sanitizers (`-fsanitize=undefined,address`) detect UB at runtime.
- Never assume UB is "probably fine" — it breaks silently in optimized builds.

## Exercises

**Easy:** Write a program that invokes signed integer overflow. Compile with `-O0` and `-O2`. Do the results differ? Now add `-fsanitize=undefined` and observe the output.

**Medium:** Write a function that strictly aliases an `int` through a `float *`. Compare results with `-O0` and `-O2`. Explain the difference.

**Hard:** Read and analyze a real CVE (Common Vulnerability Exposure) that was caused by compiler optimization exploiting undefined behavior. Describe the original code, the optimization applied, and the resulting vulnerability.

---

# PART VIII — SECURITY IN C

---

# Chapter 22 — Security Vulnerabilities and Defenses

## 22.1 The Security Implications of Manual Memory Management

C gives programmers complete control over memory. This power comes with the responsibility of ensuring that every memory operation is valid. When that responsibility is not met, the consequences range from program crashes to full system compromise.

The key insight: **in C, memory corruption is often exploitable.** A buffer overflow doesn't just crash — it can be crafted to overwrite a return address, function pointer, or security-critical variable to execute attacker-controlled code.

## 22.2 Stack Buffer Overflow

**The vulnerability:**

```c
void vulnerable(const char *user_input) {
    char buf[64];
    strcpy(buf, user_input);  /* no bounds check */
}
```

**The stack before and during overflow:**

```
Stack frame of vulnerable():
┌─────────────────────────┐  High addresses
│ return address          │  ← Target: overwrite to control execution
│ saved frame pointer     │
│ buf[63]                 │
│ ...                     │
│ buf[0]                  │  ← strcpy starts writing here
└─────────────────────────┘  Low addresses (stack grows down)
```

If `user_input` is 80 characters, `strcpy` writes 80 bytes into a 64-byte buffer, overwriting the saved frame pointer and the return address. An attacker crafts input so the return address is replaced with the address of malicious code.

**Defenses:**

1. **Bounds checking:** Use `strlcpy`, `snprintf`, or bounded functions.
2. **Stack canaries:** GCC inserts a random value (*canary*) before the return address. A buffer overflow that overwrites the return address must pass through the canary, which the function checks on exit.
3. **ASLR:** Address Space Layout Randomization makes it difficult to predict the address of injected code.
4. **NX/DEP:** Non-Executable stack prevents executing injected code.
5. **Stack protector:** `gcc -fstack-protector-strong` enables canaries.

## 22.3 Heap Buffer Overflow

Writing beyond the bounds of a `malloc`-allocated block corrupts the allocator's internal metadata:

```c
char *buf = malloc(64);
strcpy(buf, long_string);  /* overflow corrupts heap metadata */
/* Future malloc/free calls may behave unexpectedly */
```

Modern allocators (ptmalloc, jemalloc) have mitigations (canaries in block headers, validation on free), but these are not security guarantees — they are debugging aids.

## 22.4 Integer Overflow Leading to Security Issues

A classic pattern:

```c
void copy_data(char *dst, size_t count, size_t size) {
    /* if count * size overflows size_t, total_size is smaller than expected */
    size_t total_size = count * size;
    memcpy(dst, src, total_size);  /* copies less than needed */
}

char *buf = malloc(count * item_size);  /* may overflow: allocates too little */
```

Safe pattern:
```c
if (size != 0 && count > SIZE_MAX / size) {
    /* overflow would occur */
    return NULL;
}
size_t total = count * size;
char *buf = malloc(total);
```

## 22.5 Format String Vulnerabilities

```c
/* Vulnerable: user controls the format string */
void log_error(const char *user_msg) {
    printf(user_msg);   /* if user_msg is "%n", writes to arbitrary address */
}

/* Exploits: */
/* "%x %x %x" — read stack values */
/* "%100x%n"  — write value 100 to the address pointed to by next arg on stack */
```

`%n` writes the number of characters printed so far to the corresponding `int *` argument. If the attacker controls the format string and there are no arguments, printf walks up the stack finding "arguments" — whatever happens to be on the stack.

**Defense:** Always use a literal format string. Never pass user input as a format string.

```c
/* Safe: */
printf("%s", user_msg);
fprintf(log_file, "%s\n", user_msg);
```

## 22.6 Use-After-Free Vulnerabilities

```c
free(obj);
/* ... time passes, other allocations happen ... */
obj->callback();  /* use-after-free: obj may now be an attacker-controlled object */
```

In a server processing multiple requests, an attacker may be able to:
1. Trigger the free of a target object.
2. Send a crafted request that allocates a new object in the freed memory.
3. The attacker controls the content of the new object.
4. The original code uses the "old" pointer, now pointing to attacker-controlled data.

**Defense:** Set pointers to NULL after freeing. Use ownership tracking. Use AddressSanitizer to detect UAF.

## 22.7 Double Free

```c
free(ptr);
/* ... */
free(ptr);  /* double free: corrupts allocator free list */
```

An attacker who can trigger a double free can often achieve arbitrary code execution by manipulating the allocator's free list into a state that causes a subsequent `malloc` to return a pointer to arbitrary memory.

## 22.8 Off-by-One Errors

```c
char buf[64];
for (int i = 0; i <= 64; i++) {  /* should be i < 64 */
    buf[i] = 0;   /* writes buf[64] — one byte past the end */
}

/* String copy: forgetting the null terminator: */
char dst[5];
strncpy(dst, "hello", 5);  /* copies 5 bytes but no null terminator */
/* dst is not a valid C string */
```

One-byte overflows can still be exploitable. On some platforms, overwriting one byte past a buffer overwrites the low byte of the saved frame pointer, enabling control of execution.

## 22.9 Compiler Mitigations

| Mitigation | Flag | What it does |
|---|---|---|
| Stack canary | `-fstack-protector-strong` | Detects stack buffer overflows |
| ASLR | OS-controlled | Randomizes address layout |
| PIE | `-fPIE -pie` | Position-independent executable (for ASLR) |
| RELRO | `-Wl,-z,relro,-z,now` | Makes GOT/PLT read-only after startup |
| Fortify Source | `-D_FORTIFY_SOURCE=2` | Adds bounds checks to many library calls |

Enable all of these for security-sensitive code:
```bash
gcc -O2 -fstack-protector-strong -fPIE -pie \
    -D_FORTIFY_SOURCE=2 \
    -Wl,-z,relro,-z,now \
    myprogram.c -o myprogram
```

## 22.10 Secure Coding Checklist

- [ ] Never use `gets`, `strcpy`, `strcat`, `sprintf` without bounds
- [ ] Always check `malloc`/`calloc`/`realloc` for NULL
- [ ] Validate all array indices before use
- [ ] Never use user-controlled format strings
- [ ] Set pointers to NULL after freeing
- [ ] Check for integer overflow before allocating `count * size` bytes
- [ ] Use `snprintf` everywhere formatted strings go into buffers
- [ ] Enable compiler security flags (`-fstack-protector-strong`, `-D_FORTIFY_SOURCE=2`)
- [ ] Test with ASan and UBSan before every release
- [ ] Use `fgets` with explicit length for line input
- [ ] Validate all input lengths before copying

---

## Summary

C security vulnerabilities arise from the combination of manual memory management, no built-in bounds checking, and powerful pointer operations. Stack buffer overflows overwrite return addresses. Heap overflows corrupt allocator metadata. Integer overflow causes under-allocation. Format string attacks read and write arbitrary memory. Use-after-free and double free corrupt allocator state. All are mitigated by safe programming practices, compiler hardening flags, and testing with sanitizers.

---

# PART IX — PERFORMANCE ENGINEERING

---

# Chapter 23 — Performance Analysis and Optimization

## 23.1 The Performance Engineering Mindset

**Measure first. Optimize second.**

Intuition about performance is frequently wrong. The bottleneck is rarely where you think it is. Profile before changing anything.

```
Performance workflow:
1. Write correct code.
2. Profile to find the bottleneck.
3. Understand WHY it is slow.
4. Apply a targeted optimization.
5. Measure again to confirm improvement.
6. Repeat if needed.
```

Never optimize code that is not a bottleneck. Premature optimization wastes time and makes code harder to read.

## 23.2 Profiling Tools

**`gprof` (GCC profiling):**
```bash
gcc -pg -O2 myprogram.c -o myprogram
./myprogram
gprof myprogram gmon.out | less
```
Shows time spent per function (sampling-based on POSIX systems).

**`perf` (Linux performance counters):**
```bash
perf stat ./myprogram        # CPU event counts
perf record ./myprogram      # Sample call stack
perf report                  # Interactive annotated report
```
Shows where CPU cycles are spent with very low overhead.

**Valgrind Callgrind:**
```bash
valgrind --tool=callgrind ./myprogram
kcachegrind callgrind.out.*  # visualize
```
Instruction-level profiling. Higher overhead than `perf` but very detailed.

## 23.3 Cache Architecture and Data Layout

The difference between cache-friendly and cache-hostile code:

```c
#define N 1024

int matrix[N][N];

/* Cache-friendly: row-major order (sequential access) */
long sum_row_major(void) {
    long s = 0;
    for (int i = 0; i < N; i++)
        for (int j = 0; j < N; j++)
            s += matrix[i][j];   /* accesses memory sequentially */
    return s;
}

/* Cache-hostile: column-major order (strided access) */
long sum_col_major(void) {
    long s = 0;
    for (int j = 0; j < N; j++)
        for (int i = 0; i < N; i++)
            s += matrix[i][j];   /* jumps by N*sizeof(int) = 4096 bytes each step */
    return s;
}
```

The row-major version is typically 5–10× faster due to cache efficiency. Every row-major access hits data that is in the cache from the previous access. Every column-major access is a cache miss.

**Cache line:** 64 bytes on x86. When you access one byte, the CPU loads 64 bytes into cache. Subsequent accesses to the same 64-byte line are free.

## 23.4 Array-of-Structs vs. Struct-of-Arrays

```c
/* Array-of-structs (AoS) — common, intuitive */
struct Particle {
    float x, y, z;
    float vx, vy, vz;
    float mass;
    int active;
};
struct Particle particles[10000];

/* When updating positions, you load x,y,z AND vx,vy,vz AND mass AND active,
   even though you only need x,y,z,vx,vy,vz. Wasted cache bandwidth. */

/* Struct-of-arrays (SoA) — cache-efficient for batch operations */
struct Particles {
    float x[10000], y[10000], z[10000];
    float vx[10000], vy[10000], vz[10000];
    float mass[10000];
    int   active[10000];
};

/* Updating positions: access x[], y[], z[], vx[], vy[], vz[] sequentially.
   Perfect cache utilization. Also enables SIMD auto-vectorization. */
```

Use AoS when you typically access all fields of one object at once. Use SoA when you process one field across many objects (batch operations, simulations).

## 23.5 Compiler Optimizations

What `-O2` enables (key optimizations):

- **Constant folding:** `3 * 4 + 2` → `14` at compile time
- **Constant propagation:** If `x = 5` and `y = x + 3`, the compiler knows `y = 8`
- **Common subexpression elimination:** Compute `a*b` once when used multiple times
- **Dead code elimination:** Remove branches/code that cannot execute
- **Loop invariant code motion:** Hoist constant expressions out of loops
- **Inlining:** Replace short function calls with the function body
- **Strength reduction:** Replace expensive operations with cheaper equivalents (`x*2` → `x<<1`)
- **Auto-vectorization:** Use SIMD instructions for simple loops

Help the compiler:
```c
/* Help vectorization: */
void add_arrays(float * restrict a, const float * restrict b, int n) {
    /* restrict tells compiler a and b don't alias: SIMD safe */
    for (int i = 0; i < n; i++)
        a[i] += b[i];
}
```

## 23.6 Branch Prediction

Modern CPUs predict which way branches will go. Mispredictions cost ~15 cycles.

```c
/* Predictable branch: sorted data or data with clear patterns */
for (int i = 0; i < n; i++) {
    if (data[i] > 128) sum += data[i];  /* with sorted data: first all fail, then all succeed */
}

/* Unpredictable branch: random data → many mispredictions */
```

**Branchless alternatives:**

```c
/* With branch (potential misprediction): */
int max(int a, int b) {
    return a > b ? a : b;
}

/* Branchless (always executes both paths conceptually): */
int max_branchless(int a, int b) {
    int diff = a - b;
    int sign = diff >> 31;     /* arithmetic shift: 0 if positive, -1 if negative */
    return a - (diff & sign);  /* a if a >= b, b if a < b */
}
```

However: branchless code is not always faster. Profile first.

## 23.7 Memory Allocation Performance

`malloc`/`free` are surprisingly expensive for fine-grained allocations:
- They acquire a lock (in multithreaded programs)
- They search a free list
- They may call the OS for more memory

For hot paths with many small allocations, use:
- **Stack allocation:** Fastest possible. Limited to stack size.
- **Arena allocator:** Alloc is a pointer bump. Free is resetting a pointer.
- **Pool allocator:** Maintain a free list of same-sized objects.

## 23.8 Profiling and Benchmarking Methodology

A benchmark must be statistically valid:

```c
#include <time.h>
#include <stdio.h>

/* Run the function many times and report median time */
#define BENCH(name, code) do { \
    struct timespec start, end; \
    const int runs = 1000; \
    double times[runs]; \
    for (int r = 0; r < runs; r++) { \
        clock_gettime(CLOCK_MONOTONIC, &start); \
        (code); \
        clock_gettime(CLOCK_MONOTONIC, &end); \
        times[r] = (end.tv_sec - start.tv_sec) + \
                   (end.tv_nsec - start.tv_nsec) * 1e-9; \
    } \
    /* Sort times and report median */ \
    qsort(times, runs, sizeof(double), cmp_double); \
    printf("%s: median %.6f s\n", (name), times[runs/2]); \
} while(0)
```

- Use `CLOCK_MONOTONIC` for timing
- Run many iterations; report median (not mean — outliers from OS interrupts)
- Ensure the compiler doesn't optimize away the benchmarked code (use `volatile` or output the result)
- Benchmark in the same conditions as production (same optimization flags, same data patterns)

---

## Summary

Performance requires measurement, not guesswork. Profile with `perf` or Valgrind Callgrind to find bottlenecks. Cache efficiency dominates: access memory sequentially, prefer SoA for batch operations, match data layout to access patterns. Compiler optimizations at `-O2` are extensive; help with `restrict` and predictable branches. Custom allocators eliminate `malloc` overhead in hot paths.

---

# PART X — CONCURRENCY

---

# Chapter 24 — Concurrency: Threads, Atomics, and the Memory Model

## 24.1 Why Concurrency Is Hard

Concurrency introduces two fundamental challenges:

1. **Interference:** Two threads reading and writing shared data without coordination can interleave in arbitrary ways, producing results no sequential execution would produce.
2. **Visibility:** Without synchronization, changes made by one thread are not guaranteed to be visible to another. CPUs and compilers reorder memory operations.

## 24.2 C11 Threads

C11 introduced a standard threading interface via `<threads.h>`:

```c
#include <threads.h>
#include <stdio.h>

int thread_func(void *arg) {
    int id = *(int *)arg;
    printf("Thread %d running\n", id);
    return 0;
}

int main(void) {
    thrd_t t1, t2;
    int id1 = 1, id2 = 2;
    
    thrd_create(&t1, thread_func, &id1);
    thrd_create(&t2, thread_func, &id2);
    
    thrd_join(t1, NULL);
    thrd_join(t2, NULL);
    return 0;
}
```

In practice, most C programs use POSIX threads (pthreads) instead of C11 threads, as pthreads are more widely supported and have a richer API.

## 24.3 POSIX Threads (pthreads)

```c
#include <pthread.h>

void *thread_func(void *arg) {
    /* ... */
    return NULL;
}

pthread_t t;
pthread_create(&t, NULL, thread_func, arg);
pthread_join(t, NULL);  /* wait for thread to finish */
```

## 24.4 Mutexes

A mutex (mutual exclusion lock) ensures that only one thread executes a critical section at a time:

```c
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;
int counter = 0;

void increment(void) {
    pthread_mutex_lock(&lock);
    counter++;                      /* critical section */
    pthread_mutex_unlock(&lock);
}
```

**Rules:**
- Lock before accessing shared data; unlock after.
- Never access shared data without the lock.
- Never lock a mutex you already hold (unless using recursive mutexes).
- Always unlock on every code path, including error paths.

## 24.5 Condition Variables

Condition variables allow threads to wait for a condition to become true:

```c
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t  cond = PTHREAD_COND_INITIALIZER;
int data_ready = 0;

/* Producer: */
void produce(void) {
    pthread_mutex_lock(&lock);
    /* ... produce data ... */
    data_ready = 1;
    pthread_cond_signal(&cond);   /* wake one waiting thread */
    pthread_mutex_unlock(&lock);
}

/* Consumer: */
void consume(void) {
    pthread_mutex_lock(&lock);
    while (!data_ready) {             /* loop: check condition with lock held */
        pthread_cond_wait(&cond, &lock);  /* unlock, wait, re-lock */
    }
    /* ... consume data ... */
    pthread_mutex_unlock(&lock);
}
```

The `while` loop (not `if`) around `pthread_cond_wait` is mandatory: spurious wakeups can occur, and the condition must be re-checked.

## 24.6 Atomic Operations

For simple shared variables, atomic operations provide lock-free synchronization:

```c
#include <stdatomic.h>

_Atomic int counter = 0;

void increment(void) {
    atomic_fetch_add(&counter, 1);  /* atomic increment */
}

int read(void) {
    return atomic_load(&counter);   /* atomic read */
}
```

Atomic operations are guaranteed to be indivisible — no other thread can observe a partial update.

## 24.7 Memory Ordering

Modern CPUs and compilers reorder memory operations for performance. Atomic operations can specify *memory ordering* constraints:

| Order | Meaning |
|---|---|
| `memory_order_relaxed` | No ordering guarantees; only atomicity |
| `memory_order_acquire` | No reads/writes after this can be reordered before it |
| `memory_order_release` | No reads/writes before this can be reordered after it |
| `memory_order_acq_rel` | Both acquire and release |
| `memory_order_seq_cst` | Sequential consistency (default; strongest) |

**Acquire-release pattern for a flag:**

```c
_Atomic int ready = 0;
int data;

/* Writer thread: */
data = 42;                                    /* data written before flag */
atomic_store_explicit(&ready, 1, memory_order_release);  /* publish */

/* Reader thread: */
while (!atomic_load_explicit(&ready, memory_order_acquire))
    ;   /* spin until flag is seen */
int x = data;  /* guaranteed to see data = 42 */
```

## 24.8 Data Races: Definition

A **data race** occurs when:
1. Two or more threads access the same memory location concurrently,
2. At least one access is a write,
3. The accesses are not synchronized.

A data race is undefined behavior in C11. It does not just cause wrong values — it invalidates the entire program.

**ThreadSanitizer** detects data races:
```bash
gcc -fsanitize=thread -g -o myprogram myprogram.c -lpthread
```

## 24.9 Common Concurrency Bugs

**Deadlock:** Two threads each hold a lock the other needs.
```
Thread 1: lock(A), try to lock(B) — waiting
Thread 2: lock(B), try to lock(A) — waiting
```
Prevention: always acquire locks in the same order.

**Race condition:** Result depends on thread scheduling (sometimes correct, sometimes wrong).

**ABA problem:** Thread reads value A, another changes A→B→A, first thread sees A and wrongly assumes no change occurred. Relevant for lock-free algorithms.

---

## Summary

C11 provides threads and atomics. POSIX threads are the practical standard. Protect shared data with mutexes. Use condition variables for thread signaling. Atomic operations enable lock-free synchronization. Memory ordering controls visibility guarantees. Data races are undefined behavior. ThreadSanitizer detects races. Deadlocks occur from inconsistent lock ordering.

---

# PART XI — SYSTEMS PROGRAMMING

---

# Chapter 25 — Processes and Signals

## 25.1 The Process Model

A process is an instance of a running program. It has:
- A unique PID (process ID)
- Its own virtual address space
- File descriptors
- Environment variables
- Resource limits
- A parent process (except PID 1)

## 25.2 `fork` and `exec`

`fork` creates a child process that is an exact copy of the parent:

```c
#include <unistd.h>
#include <sys/wait.h>

pid_t pid = fork();
if (pid < 0) {
    perror("fork");
    exit(1);
} else if (pid == 0) {
    /* Child process */
    printf("Child PID: %d\n", getpid());
    exit(0);
} else {
    /* Parent process */
    printf("Parent: child PID is %d\n", pid);
    int status;
    waitpid(pid, &status, 0);  /* wait for child to finish */
}
```

`exec` replaces the current process image with a new program:

```c
execl("/bin/ls", "ls", "-la", NULL);
/* If execl returns, it failed */
perror("execl");
exit(1);
```

The classic fork+exec pattern:

```c
pid_t pid = fork();
if (pid == 0) {
    /* Child: exec the new program */
    execvp(argv[0], argv);
    exit(127);  /* exec failed */
}
/* Parent: wait for child */
waitpid(pid, &status, 0);
```

## 25.3 Signals

A signal is an asynchronous notification sent to a process. Common signals:

| Signal | Value | Default action | Common cause |
|---|---|---|---|
| SIGINT | 2 | Terminate | Ctrl+C |
| SIGTERM | 15 | Terminate | `kill` command |
| SIGKILL | 9 | Terminate | Cannot be caught |
| SIGSEGV | 11 | Core dump | Segmentation fault |
| SIGBUS | 7 | Core dump | Bus error |
| SIGHUP | 1 | Terminate | Terminal hangup |
| SIGCHLD | 17 | Ignore | Child process stopped/exited |
| SIGALRM | 14 | Terminate | Timer expired |
| SIGPIPE | 13 | Terminate | Write to closed pipe |

**Installing a signal handler:**

```c
#include <signal.h>
#include <stdio.h>

static volatile sig_atomic_t g_running = 1;

void sigint_handler(int sig) {
    (void)sig;
    g_running = 0;  /* set flag; main loop will check it */
}

int main(void) {
    struct sigaction sa = {0};
    sa.sa_handler = sigint_handler;
    sigemptyset(&sa.sa_mask);
    sigaction(SIGINT, &sa, NULL);  /* use sigaction, not signal() */
    
    while (g_running) {
        /* main loop */
    }
    return 0;
}
```

**Async-signal-safe functions:** Signal handlers can only call async-signal-safe functions. The list is limited: `write`, `read`, `exit`, `signal`, and a few others. `printf`, `malloc`, and `free` are NOT safe in signal handlers. The `volatile sig_atomic_t` flag pattern (set in handler, checked in main loop) is the standard approach.

---

# Chapter 26 — Files, Pipes, and IPC

## 26.1 POSIX File Descriptors

At the OS level, file I/O uses file descriptors (small non-negative integers). The standard I/O library (`FILE *`) wraps these with buffering.

```c
#include <fcntl.h>
#include <unistd.h>

int fd = open("data.txt", O_RDONLY);
if (fd < 0) { perror("open"); exit(1); }

char buf[256];
ssize_t n = read(fd, buf, sizeof(buf));
if (n < 0) { perror("read"); }
if (n == 0) { /* EOF */ }

write(fd, buf, n);  /* write n bytes */
close(fd);
```

Open flags:
- `O_RDONLY`, `O_WRONLY`, `O_RDWR`
- `O_CREAT | O_TRUNC` — create or truncate
- `O_APPEND` — always write at end
- `O_NONBLOCK` — don't block on operations

## 26.2 Pipes

Anonymous pipes connect a parent and child process:

```c
int pipefd[2];   /* pipefd[0]: read end, pipefd[1]: write end */
pipe(pipefd);

pid_t pid = fork();
if (pid == 0) {
    /* Child: read from pipe */
    close(pipefd[1]);          /* close write end */
    char buf[256];
    read(pipefd[0], buf, sizeof(buf));
    printf("Child received: %s\n", buf);
    close(pipefd[0]);
    exit(0);
} else {
    /* Parent: write to pipe */
    close(pipefd[0]);          /* close read end */
    write(pipefd[1], "hello", 5);
    close(pipefd[1]);          /* close write end: EOF for child */
    wait(NULL);
}
```

## 26.3 Memory-Mapped Files

`mmap` maps a file into the process's virtual address space:

```c
#include <sys/mman.h>
#include <fcntl.h>

int fd = open("data.bin", O_RDONLY);
struct stat sb;
fstat(fd, &sb);

void *map = mmap(NULL, sb.st_size, PROT_READ, MAP_PRIVATE, fd, 0);
if (map == MAP_FAILED) { perror("mmap"); exit(1); }
close(fd);

/* Access file contents directly as memory: */
unsigned char *data = map;
printf("First byte: %02x\n", data[0]);

munmap(map, sb.st_size);
```

`mmap` is excellent for:
- Random access to large files (OS handles I/O lazily via page faults)
- Sharing memory between processes (`MAP_SHARED`)
- Loading binary data without explicit I/O

## 26.4 I/O Multiplexing with `select` and `poll`

When handling multiple file descriptors, you need to know which are ready for I/O:

```c
#include <sys/select.h>

fd_set readfds;
FD_ZERO(&readfds);
FD_SET(fd1, &readfds);
FD_SET(fd2, &readfds);

struct timeval timeout = {.tv_sec = 5, .tv_usec = 0};
int ready = select(max_fd + 1, &readfds, NULL, NULL, &timeout);
if (ready > 0) {
    if (FD_ISSET(fd1, &readfds)) { /* fd1 is readable */ }
    if (FD_ISSET(fd2, &readfds)) { /* fd2 is readable */ }
}
```

For large numbers of FDs, use `epoll` (Linux) or `kqueue` (BSD/macOS) instead. `epoll` is O(1) per event; `select` and `poll` are O(n).

---

# Chapter 27 — Sockets and Networking

## 27.1 The BSD Socket API

Sockets provide a uniform interface for network communication:

```c
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <netdb.h>

/* TCP server: */
int server_fd = socket(AF_INET, SOCK_STREAM, 0);
int opt = 1;
setsockopt(server_fd, SOL_SOCKET, SO_REUSEADDR, &opt, sizeof(opt));

struct sockaddr_in addr = {
    .sin_family = AF_INET,
    .sin_port   = htons(8080),
    .sin_addr.s_addr = INADDR_ANY
};
bind(server_fd, (struct sockaddr *)&addr, sizeof(addr));
listen(server_fd, 10);

while (1) {
    struct sockaddr_in client_addr;
    socklen_t client_len = sizeof(client_addr);
    int client_fd = accept(server_fd, (struct sockaddr *)&client_addr, &client_len);
    
    char buf[1024];
    ssize_t n = recv(client_fd, buf, sizeof(buf) - 1, 0);
    buf[n] = '\0';
    
    const char *response = "HTTP/1.0 200 OK\r\n\r\nHello!";
    send(client_fd, response, strlen(response), 0);
    close(client_fd);
}
```

```c
/* TCP client: */
int fd = socket(AF_INET, SOCK_STREAM, 0);

struct addrinfo hints = {.ai_family = AF_INET, .ai_socktype = SOCK_STREAM};
struct addrinfo *result;
getaddrinfo("example.com", "80", &hints, &result);

connect(fd, result->ai_addr, result->ai_addrlen);
freeaddrinfo(result);

send(fd, "GET / HTTP/1.0\r\n\r\n", 18, 0);
char buf[4096];
ssize_t n = recv(fd, buf, sizeof(buf) - 1, 0);
buf[n] = '\0';
printf("%s\n", buf);
close(fd);
```

## 27.2 Byte Order

Network protocols use big-endian byte order. Host byte order is platform-dependent (typically little-endian on x86):

```c
uint16_t port = 8080;
uint16_t net_port = htons(port);    /* host-to-network short */
uint32_t addr = htonl(INADDR_ANY);  /* host-to-network long */

uint16_t host_port = ntohs(net_port);  /* network-to-host short */
```

Always convert multi-byte fields when sending/receiving over the network.

---

# PART XII — DATA STRUCTURES FROM SCRATCH

---

# Chapter 28 — Linear Data Structures

## 28.1 Dynamic Array (Vector)

A resizable array that grows as needed:

```c
typedef struct {
    int    *data;
    size_t  size;
    size_t  capacity;
} Vec;

Vec *vec_create(void) {
    Vec *v = malloc(sizeof(Vec));
    if (!v) return NULL;
    v->data = malloc(16 * sizeof(int));
    if (!v->data) { free(v); return NULL; }
    v->size = 0;
    v->capacity = 16;
    return v;
}

int vec_push(Vec *v, int value) {
    if (v->size == v->capacity) {
        size_t new_cap = v->capacity * 2;
        int *new_data = realloc(v->data, new_cap * sizeof(int));
        if (!new_data) return -1;  /* allocation failed; old data intact */
        v->data = new_data;
        v->capacity = new_cap;
    }
    v->data[v->size++] = value;
    return 0;
}

int vec_get(const Vec *v, size_t idx) {
    assert(idx < v->size);
    return v->data[idx];
}

void vec_free(Vec *v) {
    if (v) {
        free(v->data);
        free(v);
    }
}
```

**Complexity:** Push: amortized O(1) (occasional O(n) resize). Get: O(1). Memory: contiguous — excellent cache behavior.

The doubling strategy ensures that N pushes require O(N) total work, making each push amortized O(1).

## 28.2 Singly Linked List

```c
typedef struct Node {
    int value;
    struct Node *next;
} Node;

typedef struct {
    Node  *head;
    size_t size;
} List;

List *list_create(void) {
    List *l = calloc(1, sizeof(List));
    return l;
}

int list_prepend(List *l, int value) {
    Node *n = malloc(sizeof(Node));
    if (!n) return -1;
    n->value = value;
    n->next  = l->head;
    l->head  = n;
    l->size++;
    return 0;
}

void list_remove_head(List *l) {
    if (!l->head) return;
    Node *old = l->head;
    l->head = old->next;
    free(old);
    l->size--;
}

void list_free(List *l) {
    Node *n = l->head;
    while (n) {
        Node *next = n->next;
        free(n);
        n = next;
    }
    free(l);
}
```

**Complexity:** Prepend: O(1). Access by index: O(n). Search: O(n). Memory: non-contiguous — poor cache behavior.

Use linked lists when: frequent insertion/removal at known positions, no random access needed.

## 28.3 Ring Buffer (Circular Queue)

Efficient FIFO queue with fixed capacity — no allocation per operation:

```c
typedef struct {
    int   *data;
    size_t capacity;
    size_t head;   /* next read position */
    size_t tail;   /* next write position */
    size_t size;
} RingBuffer;

RingBuffer *rb_create(size_t capacity) {
    RingBuffer *rb = malloc(sizeof(RingBuffer));
    if (!rb) return NULL;
    rb->data = malloc(capacity * sizeof(int));
    if (!rb->data) { free(rb); return NULL; }
    rb->capacity = capacity;
    rb->head = rb->tail = rb->size = 0;
    return rb;
}

int rb_push(RingBuffer *rb, int value) {
    if (rb->size == rb->capacity) return -1;  /* full */
    rb->data[rb->tail] = value;
    rb->tail = (rb->tail + 1) % rb->capacity;
    rb->size++;
    return 0;
}

int rb_pop(RingBuffer *rb, int *out) {
    if (rb->size == 0) return -1;  /* empty */
    *out = rb->data[rb->head];
    rb->head = (rb->head + 1) % rb->capacity;
    rb->size--;
    return 0;
}
```

**Complexity:** Push and pop: O(1). Fixed memory: allocated once.

---

# Chapter 29 — Trees, Heaps, and Hash Tables

## 29.1 Binary Search Tree

```c
typedef struct BST_Node {
    int key;
    struct BST_Node *left;
    struct BST_Node *right;
} BST_Node;

BST_Node *bst_insert(BST_Node *root, int key) {
    if (!root) {
        BST_Node *n = malloc(sizeof(BST_Node));
        if (!n) return NULL;
        n->key = key; n->left = n->right = NULL;
        return n;
    }
    if (key < root->key)      root->left  = bst_insert(root->left,  key);
    else if (key > root->key) root->right = bst_insert(root->right, key);
    /* duplicate keys: ignore */
    return root;
}

int bst_search(const BST_Node *root, int key) {
    if (!root) return 0;
    if (key == root->key) return 1;
    if (key < root->key) return bst_search(root->left, key);
    return bst_search(root->right, key);
}

void bst_free(BST_Node *root) {
    if (!root) return;
    bst_free(root->left);
    bst_free(root->right);
    free(root);
}
```

**Complexity:** Insert/search/delete: O(log n) average; O(n) worst case (degenerate tree from sorted input).

## 29.2 Binary Heap (Priority Queue)

A complete binary tree stored in an array where every parent is smaller than its children (min-heap):

```c
typedef struct {
    int   *data;
    size_t size;
    size_t capacity;
} MinHeap;

static void swap(int *a, int *b) { int t = *a; *a = *b; *b = t; }

static void sift_up(MinHeap *h, size_t i) {
    while (i > 0) {
        size_t parent = (i - 1) / 2;
        if (h->data[parent] <= h->data[i]) break;
        swap(&h->data[parent], &h->data[i]);
        i = parent;
    }
}

static void sift_down(MinHeap *h, size_t i) {
    while (1) {
        size_t smallest = i;
        size_t left  = 2*i + 1;
        size_t right = 2*i + 2;
        if (left  < h->size && h->data[left]  < h->data[smallest]) smallest = left;
        if (right < h->size && h->data[right] < h->data[smallest]) smallest = right;
        if (smallest == i) break;
        swap(&h->data[i], &h->data[smallest]);
        i = smallest;
    }
}

int heap_push(MinHeap *h, int val) {
    if (h->size == h->capacity) {
        int *nd = realloc(h->data, h->capacity * 2 * sizeof(int));
        if (!nd) return -1;
        h->data = nd; h->capacity *= 2;
    }
    h->data[h->size++] = val;
    sift_up(h, h->size - 1);
    return 0;
}

int heap_pop(MinHeap *h, int *out) {
    if (h->size == 0) return -1;
    *out = h->data[0];
    h->data[0] = h->data[--h->size];
    if (h->size > 0) sift_down(h, 0);
    return 0;
}
```

**Complexity:** Push: O(log n). Pop-min: O(log n). Build from array: O(n).

## 29.3 Hash Table (Open Addressing)

```c
typedef struct {
    char *key;     /* NULL = empty slot */
    int   value;
    int   deleted; /* tombstone for deleted entries */
} HT_Entry;

typedef struct {
    HT_Entry *entries;
    size_t    capacity;
    size_t    size;
} HashTable;

static uint32_t hash_str(const char *key) {
    /* FNV-1a hash */
    uint32_t h = 2166136261u;
    for (; *key; key++) {
        h ^= (unsigned char)*key;
        h *= 16777619u;
    }
    return h;
}

HashTable *ht_create(size_t capacity) {
    HashTable *ht = malloc(sizeof(HashTable));
    if (!ht) return NULL;
    ht->entries = calloc(capacity, sizeof(HT_Entry));
    if (!ht->entries) { free(ht); return NULL; }
    ht->capacity = capacity;
    ht->size = 0;
    return ht;
}

int ht_set(HashTable *ht, const char *key, int value) {
    if (ht->size * 2 >= ht->capacity) return -1;  /* load factor > 0.5 */
    
    uint32_t idx = hash_str(key) % ht->capacity;
    while (ht->entries[idx].key != NULL && !ht->entries[idx].deleted) {
        if (strcmp(ht->entries[idx].key, key) == 0) {
            ht->entries[idx].value = value;   /* update existing */
            return 0;
        }
        idx = (idx + 1) % ht->capacity;   /* linear probing */
    }
    
    ht->entries[idx].key   = strdup(key);
    ht->entries[idx].value = value;
    ht->entries[idx].deleted = 0;
    ht->size++;
    return 0;
}

int ht_get(const HashTable *ht, const char *key, int *out) {
    uint32_t idx = hash_str(key) % ht->capacity;
    while (ht->entries[idx].key != NULL) {
        if (!ht->entries[idx].deleted &&
            strcmp(ht->entries[idx].key, key) == 0) {
            *out = ht->entries[idx].value;
            return 0;
        }
        idx = (idx + 1) % ht->capacity;
    }
    return -1;  /* not found */
}
```

**Complexity:** Insert/lookup: O(1) average; O(n) worst case (all keys collide). Load factor < 0.7 keeps performance good.

---

# PART XIII — TOOLING AND DEVELOPMENT PRACTICES

---

# Chapter 30 — Debugging: GDB, Valgrind, and Sanitizers

## 30.1 GDB: Essential Commands

Always compile with debug symbols:
```bash
gcc -g -O0 myprogram.c -o myprogram
gdb myprogram
```

Core GDB commands:

```
(gdb) run                   # Start the program
(gdb) run arg1 arg2         # Run with arguments
(gdb) break main            # Set breakpoint at main
(gdb) break myfile.c:42     # Set breakpoint at line 42
(gdb) continue              # Continue execution
(gdb) next                  # Next line (step over functions)
(gdb) step                  # Next line (step into functions)
(gdb) finish                # Run until current function returns
(gdb) print x               # Print value of variable x
(gdb) print *ptr            # Print value pointed to by ptr
(gdb) print arr[0]@10       # Print 10 elements of arr
(gdb) info locals           # All local variables
(gdb) backtrace             # Call stack (where am I?)
(gdb) frame 2               # Switch to stack frame 2
(gdb) watch x               # Break when x changes
(gdb) info breakpoints      # List breakpoints
(gdb) delete 1              # Delete breakpoint 1
(gdb) quit
```

## 30.2 Debugging a Segfault

```bash
# Enable core dumps:
ulimit -c unlimited

# Run the program (it crashes):
./myprogram
# Segmentation fault (core dumped)

# Load the core dump:
gdb myprogram core
(gdb) backtrace   # See where it crashed
(gdb) frame 0     # Go to crash frame
(gdb) info locals # See variables at crash
(gdb) print ptr   # Why is ptr NULL/invalid?
```

## 30.3 AddressSanitizer (ASan)

ASan is the first line of defense against memory errors:

```bash
gcc -fsanitize=address -g -O1 myprogram.c -o myprogram
./myprogram
```

ASan detects and reports:
- Stack buffer overflows
- Heap buffer overflows
- Use-after-free
- Use-after-return
- Double free
- Memory leaks (with LeakSanitizer)

Example output:
```
==12345==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x602000000114
READ of size 4 at 0x602000000114 thread T0
    #0 0x401234 in f myprogram.c:15
    #1 0x401289 in main myprogram.c:25
```

## 30.4 UBSan: Undefined Behavior Sanitizer

```bash
gcc -fsanitize=undefined -g myprogram.c -o myprogram
./myprogram
```

Detects: signed overflow, null dereference, misaligned access, invalid shift, divide by zero, and many more UB cases.

## 30.5 Valgrind

Valgrind is slower (10-50× overhead) but catches more subtle issues including uninitialized reads:

```bash
valgrind --leak-check=full \
         --show-leak-kinds=all \
         --track-origins=yes \
         --error-exitcode=1 \
         ./myprogram
```

Categories of Valgrind reports:
- **Definitely lost:** Memory with no pointers to it — true leak.
- **Indirectly lost:** Memory pointed to only by lost memory.
- **Possibly lost:** Pointers to the middle of allocations.
- **Still reachable:** Memory not freed at exit (often intentional).

## 30.6 Static Analysis

```bash
# Clang static analyzer:
scan-build gcc -c myfile.c

# cppcheck:
cppcheck --enable=all --suppress=missingInclude myfile.c

# clang-tidy:
clang-tidy myfile.c -- -std=c11
```

Static analyzers find bugs without running the program: null dereferences, resource leaks, integer overflows. They have false positives but are worth running in CI.

---

# Chapter 31 — Build Systems, Testing, and CI

## 31.1 Makefile Best Practices

```makefile
CC      = gcc
CFLAGS  = -std=c11 -Wall -Wextra -Wpedantic -g
LDFLAGS = 
SRCS    = $(wildcard src/*.c)
OBJS    = $(SRCS:src/%.c=build/%.o)
TARGET  = myprogram

.PHONY: all clean test asan

all: $(TARGET)

$(TARGET): $(OBJS)
	$(CC) $(LDFLAGS) $^ -o $@

build/%.o: src/%.c | build
	$(CC) $(CFLAGS) -c $< -o $@

build:
	mkdir -p build

asan: CFLAGS += -fsanitize=address,undefined -O1
asan: $(TARGET)

clean:
	rm -rf build $(TARGET)

test: $(TARGET)
	./run_tests.sh
```

Key Makefile features:
- `$@` — target name
- `$<` — first prerequisite
- `$^` — all prerequisites
- `$(wildcard ...)` — glob expansion
- `$(patsubst ...)` or `:=` substitution for transforming file lists
- `.PHONY` — targets that aren't file names

## 31.2 Testing C Code

C lacks a built-in test framework, but simple ones are easy to write:

```c
/* test.h — minimal test framework */
#include <stdio.h>
#include <stdlib.h>

static int test_count = 0;
static int test_failures = 0;

#define TEST(name) static void name(void)
#define RUN(name) do { \
    test_count++; \
    printf("Running " #name "... "); \
    name(); \
    printf("OK\n"); \
} while (0)

#define EXPECT_EQ(a, b) do { \
    if ((a) != (b)) { \
        printf("FAIL: expected %lld, got %lld at %s:%d\n", \
               (long long)(b), (long long)(a), __FILE__, __LINE__); \
        test_failures++; \
    } \
} while (0)

#define TEST_SUMMARY() do { \
    printf("\n%d tests, %d failures\n", test_count, test_failures); \
    return test_failures > 0 ? 1 : 0; \
} while (0)
```

```c
/* test_vec.c */
#include "test.h"
#include "vec.h"

TEST(test_push_and_get) {
    Vec *v = vec_create();
    vec_push(v, 10);
    vec_push(v, 20);
    EXPECT_EQ(vec_get(v, 0), 10);
    EXPECT_EQ(vec_get(v, 1), 20);
    EXPECT_EQ(v->size, 2);
    vec_free(v);
}

int main(void) {
    RUN(test_push_and_get);
    TEST_SUMMARY();
}
```

For larger projects, use Unity, Check, or CMocka.

## 31.3 Code Coverage with gcov

```bash
gcc --coverage -g -O0 myprogram.c -o myprogram
./myprogram
gcov myprogram.c
# produces myprogram.c.gcov showing which lines were executed
lcov --capture --directory . --output-file coverage.info
genhtml coverage.info --output-directory coverage_html
```

---

# PART XIV — ADVANCED TOPICS

---

# Chapter 32 — Portability and the C Abstract Machine

## 32.1 What Portability Means

A portable C program produces the same *observable behavior* on all conforming implementations. Observable behavior is defined narrowly: reads/writes to volatile objects, I/O, and the final termination status.

Portability is not automatic. You must avoid:
- Assuming specific sizes for `int`, `long`, etc.
- Assuming specific byte order
- Using platform-specific APIs without guards
- Relying on undefined or implementation-defined behavior

## 32.2 Endianness

*Endianness* describes the byte order of multi-byte values in memory.

- **Little-endian:** Least significant byte first (x86, x86-64, most ARM)
- **Big-endian:** Most significant byte first (SPARC, MIPS, older IBM)

```c
uint32_t value = 0x01020304;

/* On little-endian: */
/* Memory: 04 03 02 01 */

/* On big-endian: */
/* Memory: 01 02 03 04 */
```

Detecting endianness at runtime:

```c
static inline int is_little_endian(void) {
    uint32_t n = 1;
    return *(unsigned char *)&n == 1;
}
```

Converting between host and network byte order:
```c
#include <arpa/inet.h>
uint32_t host = 0x01020304;
uint32_t net  = htonl(host);  /* always big-endian */
uint32_t back = ntohl(net);   /* back to host order */
```

## 32.3 Data Models

| Model | Platform | int | long | pointer |
|---|---|---|---|---|
| ILP32 | 32-bit Linux, Windows | 32 | 32 | 32 |
| LP64 | 64-bit Linux, macOS | 32 | 64 | 64 |
| LLP64 | 64-bit Windows | 32 | 32 | 64 |
| ILP64 | Some 64-bit Unix | 64 | 64 | 64 |

**Safe portable code:**
- Use `int32_t`, `int64_t` when exact sizes matter.
- Use `size_t` for sizes and counts.
- Use `intptr_t` / `uintptr_t` for integers that need to hold a pointer.
- Use `ptrdiff_t` for pointer differences.
- Never assume `sizeof(long) == sizeof(void *)`.
- Never assume `sizeof(int) == 4`.

## 32.4 Type Punning Portably

To inspect the bit representation of a floating-point value:

```c
/* Undefined behavior (strict aliasing violation): */
float f = 3.14f;
uint32_t bits = *(uint32_t *)&f;  /* UB */

/* Correct: use memcpy */
float f = 3.14f;
uint32_t bits;
memcpy(&bits, &f, sizeof(bits));  /* defined: copies bytes */

/* Also correct: use union (legal in C, not in C++) */
union { float f; uint32_t i; } u;
u.f = 3.14f;
uint32_t bits = u.i;
```

---

# Chapter 33 — Compiler Internals and Optimization

## 33.1 Compiler Architecture

A modern C compiler has three major components:

```
Source code (.c)
     │
     ▼ Front end
   Parsing, type-checking, AST construction
     │
     ▼ Middle end (optimizer)
   IR optimization passes:
     - Constant propagation
     - Dead code elimination
     - Inlining
     - Loop transformations
     - Alias analysis
     │
     ▼ Back end
   Instruction selection, register allocation, code scheduling
     │
     ▼ Assembly / object code
```

GCC uses GIMPLE and RTL as intermediate representations. Clang uses LLVM IR.

## 33.2 The "As-If" Rule and Optimization Legality

The compiler may perform any transformation that does not change the observable behavior of a correctly-written C program. This is the "as-if" rule.

Observable behavior includes:
- Reads and writes to volatile objects
- I/O operations
- The program's termination status

The compiler is NOT required to preserve:
- The number of times a computation is performed
- The order of non-observable computations
- Whether a variable is in a register or in memory
- Whether a function call is actually made (if the result is unused)

## 33.3 Key Optimizations Explained

**Inlining:** The compiler replaces a function call with the function body. This eliminates call overhead and enables further optimization (the caller can now see inside the function).

```c
static inline int square(int x) { return x * x; }

int result = square(5);
/* After inlining: int result = 5 * 5; */
/* After constant folding: int result = 25; */
```

**Dead code elimination:** Code that can never affect observable behavior is removed.

```c
int x = compute();  /* compiler: is x used? */
/* if x is never read: the entire computation may be eliminated */
```

To prevent the compiler from eliminating a computation (for benchmarking):

```c
volatile int sink = result;  /* forces the value to be "observed" */
```

**Loop unrolling:** The loop body is duplicated to reduce loop overhead:

```c
/* Original: */
for (int i = 0; i < 4; i++) arr[i] = 0;

/* Unrolled: */
arr[0] = 0; arr[1] = 0; arr[2] = 0; arr[3] = 0;
```

**Auto-vectorization:** Simple loops over arrays are replaced with SIMD instructions:

```c
/* This loop: */
for (int i = 0; i < n; i++) c[i] = a[i] + b[i];

/* May become (conceptually): */
for (int i = 0; i < n; i += 8) {
    /* process 8 floats at once using AVX2 */
}
```

To help vectorization:
- Use `restrict` to declare non-aliasing pointers
- Use regular access patterns (no conditionals inside the loop)
- Use fixed-size types

## 33.4 Controlling Optimization

```c
/* GCC/Clang: optimize this function differently */
__attribute__((optimize("O0")))  /* no optimization */
void debug_function(void) { ... }

/* Always inline: */
__attribute__((always_inline))
static inline void fast_path(void) { ... }

/* Never inline: */
__attribute__((noinline))
void slow_debug(void) { ... }

/* Mark function as pure (no side effects, result depends only on args): */
__attribute__((pure))
int hash(const char *s) { ... }

/* Mark function as const (like pure but also doesn't read global memory): */
__attribute__((const))
int square(int x) { return x * x; }
```

---

# Chapter 34 — C23: The Modern Standard

## 34.1 What's New in C23

C23 (ISO/IEC 9899:2023) is the most significant update since C11. Key additions:

**`typeof` and `typeof_unqual`:**
```c
int x = 5;
typeof(x) y = 10;          /* y is int */
typeof_unqual(const int) z; /* z is int (qualifiers stripped) */
```

**`nullptr` and `nullptr_t`:**
```c
#include <stddef.h>
int *p = nullptr;      /* null pointer constant, type nullptr_t */
void *q = nullptr;     /* implicit conversion to any pointer type */
```

**`[[attributes]]`:**
```c
[[nodiscard]] int compute(void);    /* warn if return value ignored */
[[noreturn]] void die(void);        /* function never returns */
[[deprecated("use new_api instead")]] void old_api(void);
[[maybe_unused]] int debug_flag;    /* suppress unused warnings */
[[fallthrough]];                    /* intentional switch fall-through */
```

**`_BitInt(N)` — bit-precise integers:**
```c
_BitInt(7) small = 63;     /* 7-bit signed integer */
unsigned _BitInt(128) huge; /* 128-bit unsigned integer */
```

**`#embed` — embedding binary data:**
```c
const unsigned char icon[] = {
    #embed "icon.png"
};
/* Equivalent to writing out each byte as a literal */
```

**`<stdbit.h>` — bit manipulation:**
```c
#include <stdbit.h>
stdc_count_ones(x)       /* count set bits */
stdc_leading_zeros(x)    /* count leading zero bits */
stdc_trailing_zeros(x)   /* count trailing zero bits */
stdc_bit_width(x)        /* minimum bits to represent x */
```

**`<stdckdint.h>` — checked arithmetic:**
```c
#include <stdckdint.h>
int result;
if (ckd_add(&result, a, b)) {
    /* overflow occurred */
}
if (ckd_mul(&result, a, b)) {
    /* overflow occurred */
}
```

**Improved `_Static_assert`:**
```c
/* C11: requires message */
_Static_assert(sizeof(int) == 4, "need 32-bit int");
/* C23: message is optional */
static_assert(sizeof(int) == 4);
```

**`#elifdef` / `#elifndef`:**
```c
#ifdef A
    /* A defined */
#elifdef B       /* equivalent to #elif defined(B) */
    /* B defined */
#elifndef C      /* equivalent to #elif !defined(C) */
    /* C not defined */
#endif
```

---

# PART XV — SOFTWARE ENGINEERING IN C

---

# Chapter 35 — API Design, Testing, and Production C

## 35.1 What Makes a Good C API

A great C API is:

**Consistent:** Every function follows the same conventions — naming, parameter order, error reporting. Users can guess how a new function works based on existing ones.

**Minimal but complete:** Expose only what users need. Every public function is a maintenance burden (you can't remove it without breaking users). But users must be able to do everything they need without working around the API.

**Safe by default:** The API should make it easy to do the right thing and hard to do the wrong thing. If the user must provide a length, make it the second parameter (they'll notice it's required). If a function can fail, return an error code.

**Documented:** Every public function has a contract: what it requires (preconditions), what it promises (postconditions), what it owns vs borrows.

## 35.2 Naming Conventions

Consistent naming is non-negotiable:

```c
/* Library prefix: prevents name collisions */
/* Noun_verb or prefix_noun_verb */

/* All public functions: */
buf_create()
buf_append()
buf_clear()
buf_free()

/* Constants: */
BUF_MAX_CAPACITY
BUF_DEFAULT_SIZE

/* Types: */
typedef struct Buffer Buffer;
typedef enum BufferError BufferError;
```

## 35.3 Parameter Order Conventions

The convention in C (followed by the standard library): destination first, then source.

```c
/* Like memcpy, strcpy: destination, source */
int string_copy(char *dst, size_t dst_size, const char *src);

/* Context/object first: */
int buffer_append(Buffer *buf, const void *data, size_t len);
```

## 35.4 Error Handling in APIs

Three patterns, each with tradeoffs:

**Return code (most common):**
```c
int buf_append(Buffer *b, const char *data, size_t len);
/* returns 0 on success, negative error code on failure */
```

**Out-parameter error:**
```c
Buffer *buf_create(size_t cap, int *err);
/* *err set to error code; return NULL on failure */
```

**Error struct:**
```c
typedef struct { int code; const char *message; } Error;
Error buf_append(Buffer *b, const char *data, size_t len);
```

Document your choice and use it consistently throughout your library.

## 35.5 Ownership Documentation

Every pointer parameter should document ownership:

```c
/**
 * Append data to the buffer.
 * 
 * @param buf  The buffer to append to (non-NULL, owned by caller).
 * @param data The data to append (borrowed: must remain valid for the duration of the call).
 * @param len  Number of bytes to append.
 * @returns    0 on success, ENOMEM if the buffer cannot grow.
 */
int buf_append(Buffer *buf, const void *data, size_t len);

/**
 * Create a new string by concatenating a and b.
 *
 * @returns  A newly allocated string (caller must free).
 *           NULL on allocation failure.
 */
char *str_concat(const char *a, const char *b);
```

## 35.6 Reviewing C Code: A Checklist

When reviewing C code, check for:

**Memory:**
- [ ] Every `malloc`/`calloc`/`realloc` checked for NULL
- [ ] Every allocated object has exactly one owner
- [ ] No dangling pointers (locals returned, objects used after free)
- [ ] No double free
- [ ] No use-after-free
- [ ] Arrays always paired with their length

**Strings:**
- [ ] No `strcpy`/`strcat`/`sprintf` — use `strlcpy`/`snprintf`
- [ ] Buffer sizes computed before writing
- [ ] No user-controlled format strings

**Integers:**
- [ ] No signed overflow in arithmetic
- [ ] No out-of-range comparisons of signed and unsigned
- [ ] No integer overflow in `count * size` calculations

**Control flow:**
- [ ] Every code path returns a value in non-void functions
- [ ] All switch cases have `break` or explicit `/* fallthrough */`
- [ ] Error paths free all resources before returning

**Concurrency:**
- [ ] All shared data protected by a lock or atomic
- [ ] Lock order is consistent
- [ ] No reads/writes to non-atomic shared variables from signal handlers

---

## Summary

Production C requires discipline: consistent API design, thorough error handling, clear ownership, and rigorous code review. Testing with sanitizers before every release catches memory bugs before users see them. Static analysis and code review catch logical errors. Clear documentation of contracts makes APIs maintainable over time.


---

# APPENDICES

---

# Appendix A — C Operator Precedence and Associativity (Complete Table)

Operators are listed from highest precedence (1) to lowest (15). When two operators have the same precedence, associativity determines grouping.

| Level | Operators | Associativity | Description |
|---|---|---|---|
| 1 | `()` `[]` `.` `->` `++` `--` (postfix) | Left-to-right | Postfix, member access |
| 2 | `++` `--` (prefix) `+` `-` (unary) `!` `~` `*` (deref) `&` (addr) `sizeof` `_Alignof` `(type)` | Right-to-left | Unary, prefix |
| 3 | `*` `/` `%` | Left-to-right | Multiplicative |
| 4 | `+` `-` | Left-to-right | Additive |
| 5 | `<<` `>>` | Left-to-right | Bitwise shift |
| 6 | `<` `<=` `>` `>=` | Left-to-right | Relational |
| 7 | `==` `!=` | Left-to-right | Equality |
| 8 | `&` | Left-to-right | Bitwise AND |
| 9 | `^` | Left-to-right | Bitwise XOR |
| 10 | `\|` | Left-to-right | Bitwise OR |
| 11 | `&&` | Left-to-right | Logical AND |
| 12 | `\|\|` | Left-to-right | Logical OR |
| 13 | `?:` | Right-to-left | Conditional (ternary) |
| 14 | `=` `+=` `-=` `*=` `/=` `%=` `&=` `^=` `\|=` `<<=` `>>=` | Right-to-left | Assignment |
| 15 | `,` | Left-to-right | Comma |

**Critical traps to memorize:**

```c
/* Bitwise operators are LOWER than comparisons: */
if (x & MASK == 0)   /* WRONG: parsed as x & (MASK == 0) */
if ((x & MASK) == 0) /* correct */

/* Logical operators are very low: */
a || b && c    /* parsed as a || (b && c), not (a || b) && c */

/* Unary minus vs subtraction: */
-a - b         /* parsed as (-a) - b */

/* Address-of vs bitwise AND — both are & but different contexts: */
int *p = &x;   /* unary &: address-of */
x & 0xFF;      /* binary &: bitwise AND */
```

---

# Appendix B — C Standard Library Headers: Quick Reference

| Header | Key Contents |
|---|---|
| `<assert.h>` | `assert(expr)`, `static_assert` |
| `<complex.h>` | Complex arithmetic: `creal`, `cimag`, `cabs`, `cexp` |
| `<ctype.h>` | `isalpha`, `isdigit`, `isspace`, `toupper`, `tolower` |
| `<errno.h>` | `errno`, `ENOMEM`, `EINVAL`, `ENOENT`, error codes |
| `<float.h>` | `FLT_MAX`, `DBL_EPSILON`, floating-point limits |
| `<inttypes.h>` | `PRId32`, `PRIu64` format macros for `<stdint.h>` types |
| `<limits.h>` | `INT_MAX`, `UINT_MAX`, `CHAR_BIT`, integer limits |
| `<locale.h>` | `setlocale`, `localeconv` |
| `<math.h>` | `sin`, `cos`, `sqrt`, `pow`, `log`, `fabs`, `ceil`, `floor` |
| `<setjmp.h>` | `setjmp`, `longjmp`, `jmp_buf` |
| `<signal.h>` | `signal`, `sigaction`, `raise`, `kill`, signal constants |
| `<stdarg.h>` | `va_list`, `va_start`, `va_arg`, `va_end` |
| `<stdbool.h>` | `bool`, `true`, `false` |
| `<stddef.h>` | `size_t`, `ptrdiff_t`, `NULL`, `offsetof` |
| `<stdint.h>` | `int8_t`…`int64_t`, `uint8_t`…`uint64_t`, `intptr_t` |
| `<stdio.h>` | `printf`, `scanf`, `fopen`, `fclose`, `fread`, `fwrite` |
| `<stdlib.h>` | `malloc`, `free`, `exit`, `qsort`, `strtol`, `rand` |
| `<string.h>` | `strcpy`, `strlen`, `strcmp`, `memcpy`, `memmove`, `memset` |
| `<threads.h>` | `thrd_create`, `mtx_lock`, `cnd_wait` (C11) |
| `<time.h>` | `time`, `clock_gettime`, `strftime`, `struct tm` |
| `<stdatomic.h>` | `_Atomic`, `atomic_load`, `atomic_store`, `atomic_fetch_add` (C11) |
| `<stdbit.h>` | `stdc_count_ones`, `stdc_leading_zeros` (C23) |
| `<stdckdint.h>` | `ckd_add`, `ckd_sub`, `ckd_mul` (C23) |
| `<uchar.h>` | `char16_t`, `char32_t`, Unicode conversion (C11) |

---

# Appendix C — Undefined Behavior: The Complete Catalog

This is the definitive reference list. Each entry states the UB, a minimal example, and the safe alternative.

| # | Undefined Behavior | Example | Safe Alternative |
|---|---|---|---|
| 1 | Signed integer overflow | `INT_MAX + 1` | Use `ckd_add` (C23) or unsigned arithmetic |
| 2 | Null pointer dereference | `*(int*)NULL` | Check for NULL before dereferencing |
| 3 | Out-of-bounds array access | `arr[n]` where valid range is 0..n-1 | Bounds check; use AddressSanitizer |
| 4 | Use-after-free | `free(p); *p = 1;` | Set `p = NULL` after free |
| 5 | Double free | `free(p); free(p);` | Set `p = NULL` after free |
| 6 | Uninitialized automatic variable read | `int x; return x;` | Initialize all variables |
| 7 | Strict aliasing violation | `*(float*)&my_int` | Use `memcpy` or union for type punning |
| 8 | Shift count negative | `x << -1` | Check shift count is non-negative |
| 9 | Shift count >= type width | `1 << 32` (32-bit int) | Use width-aware shift: `1u << 31` |
| 10 | Left-shift of negative value | `-1 << 1` | Use unsigned types for bit manipulation |
| 11 | Pointer arithmetic out of bounds | `arr + 10` on a 5-element array | Stay within `[arr, arr+n]` |
| 12 | Dereferencing one-past-end pointer | `*(arr + n)` | Only compare, never dereference past-end |
| 13 | Modifying a string literal | `char *s = "hi"; s[0] = 'H';` | Use `char s[] = "hi";` |
| 14 | Modifying a `const` object | `const int x = 1; *(int*)&x = 2;` | Don't cast away `const` to write |
| 15 | Data race | Two threads write the same int | Use `_Atomic` or mutex |
| 16 | Calling function via incompatible pointer | `void (*f)(int) = some_func; f(1.0);` | Match function pointer type exactly |
| 17 | `va_arg` type mismatch | `va_arg(ap, int)` when argument is `double` | Match types; use correct format strings |
| 18 | Integer-to-pointer with unaligned result | `(int*)(ptr + 1)` on 4-byte-aligned arch | Ensure alignment matches type requirement |
| 19 | Sequence point violation | `i = i++` | Separate into two statements |
| 20 | Division by zero (integer) | `x / 0` | Check denominator before dividing |
| 21 | `realloc` with NULL result used | `ptr = realloc(ptr, n)` | Use a temp: `tmp = realloc(ptr, n)` |
| 22 | Non-`void` function falls off end | `int f() { /* no return */ }` | Ensure all paths return a value |
| 23 | `memcpy` with overlapping regions | `memcpy(dst, dst+1, n)` | Use `memmove` for overlapping regions |
| 24 | Read from `write`-only `FILE *` | `fread(buf, 1, n, f)` when f opened `"w"` | Open with correct mode |
| 25 | Trap representation access | Platform-specific trap values for `char` | Use `unsigned char` for byte operations |

---

# Appendix D — GDB Command Reference

## Starting GDB

```bash
gdb ./myprogram                    # basic start
gdb ./myprogram core               # load core dump
gdb --args ./myprogram arg1 arg2   # with arguments
gdb -p 12345                       # attach to running process
```

## Breakpoints

```
break main                  # break at function entry
break myfile.c:42           # break at file:line
break *0x401234             # break at address
tbreak main                 # temporary breakpoint (auto-deletes)
rbreak ^str                 # break on all functions matching regex
condition 1 x > 5           # only break when condition is true
ignore 1 5                  # ignore breakpoint 1 five times
info breakpoints            # list all breakpoints
disable 1                   # disable breakpoint 1
enable 1                    # enable breakpoint 1
delete 1                    # delete breakpoint 1
delete                      # delete all breakpoints
```

## Execution Control

```
run                         # start program
run arg1 arg2               # start with arguments
continue   (or c)           # continue until next breakpoint
next       (or n)           # step over (don't enter functions)
step       (or s)           # step into functions
finish                      # run until current function returns
until 50                    # run until line 50
advance myfile.c:100        # advance to line 100
jump 42                     # jump to line 42 (dangerous)
return 0                    # force function to return with value 0
```

## Examining Data

```
print x                     # print value of x
print *ptr                  # dereference pointer
print arr[3]                # array element
print arr[0]@10             # print 10 elements from arr[0]
print /x x                  # print in hex
print /d x                  # print as signed decimal
print /u x                  # print as unsigned decimal
print /b x                  # print as binary
print /f x                  # print as float
display x                   # print x at every step (auto-display)
undisplay 1                 # stop auto-displaying
info locals                 # all local variables
info args                   # function arguments
whatis x                    # type of x
ptype struct Point          # full type definition
```

## Stack Inspection

```
backtrace      (or bt)      # full call stack
backtrace 5                 # top 5 frames only
frame 2                     # switch to frame 2
up                          # go one frame up (toward caller)
down                        # go one frame down (toward callee)
info frame                  # current frame details
```

## Memory Inspection

```
x/10xb ptr               # examine 10 bytes in hex
x/4xw ptr                # examine 4 words (4-byte) in hex
x/4xg ptr                # examine 4 giant-words (8-byte) in hex
x/s ptr                  # examine as string
x/i ptr                  # examine as instruction (disassemble)
dump binary memory f.bin addr1 addr2  # dump memory to file
```

## Watchpoints

```
watch x                  # break when x is written
rwatch x                 # break when x is read
awatch x                 # break when x is read or written
info watchpoints
```

## GDB Scripting

```
define my_cmd
  backtrace
  info locals
end

# Run commands at breakpoint:
commands 1
  print x
  continue
end
```

## Useful .gdbinit patterns

```
set print pretty on          # pretty-print structs
set print array on           # print arrays more readably
set print array-indexes on   # show array indices
set pagination off           # don't pause output
set confirm off              # don't ask for confirmation
```

---

# Appendix E — Sanitizer Flags Quick Reference

## AddressSanitizer (ASan)

```bash
gcc -fsanitize=address -g -O1 -fno-omit-frame-pointer myprogram.c -o myprogram
```

Detects:
- Stack buffer overflow / underflow
- Heap buffer overflow / underflow
- Global buffer overflow
- Use-after-free
- Use-after-return (`-fsanitize-address-use-after-return=always`)
- Double free
- Invalid free (freeing non-heap memory)

Environment variables:
```bash
ASAN_OPTIONS=detect_leaks=1:halt_on_error=0:log_path=asan.log ./myprogram
```

## UBSan (Undefined Behavior Sanitizer)

```bash
gcc -fsanitize=undefined -g myprogram.c -o myprogram
```

Detects: signed overflow, shift out of bounds, null dereference, misaligned access, divide by zero, invalid conversions, VLA bound violation, and many more.

Individual checks:
```bash
-fsanitize=signed-integer-overflow
-fsanitize=shift
-fsanitize=null
-fsanitize=alignment
-fsanitize=bounds
-fsanitize=float-divide-by-zero
-fsanitize=integer-divide-by-zero
```

## MemorySanitizer (MSan) — Clang only

```bash
clang -fsanitize=memory -g -O1 myprogram.c -o myprogram
```

Detects reads from uninitialized memory. MSan cannot be combined with ASan.

## ThreadSanitizer (TSan)

```bash
gcc -fsanitize=thread -g -O1 myprogram.c -o myprogram -lpthread
```

Detects data races. Cannot be combined with ASan or MSan.

## LeakSanitizer (LSan)

```bash
gcc -fsanitize=leak -g myprogram.c -o myprogram
# or enable within ASan:
ASAN_OPTIONS=detect_leaks=1 ./myprogram
```

## Recommended Development Build

```bash
gcc -std=c11 -Wall -Wextra -Wpedantic \
    -g -O1 -fno-omit-frame-pointer \
    -fsanitize=address,undefined \
    -o myprogram myprogram.c
```

## Recommended CI Build

```bash
# Build 1: ASan + UBSan
gcc -std=c11 -Wall -Wextra -Werror -g -O1 \
    -fsanitize=address,undefined \
    -o myprogram_asan myprogram.c && ./myprogram_asan

# Build 2: TSan (separate: incompatible with ASan)
gcc -std=c11 -Wall -Wextra -Werror -g -O1 \
    -fsanitize=thread \
    -o myprogram_tsan myprogram.c && ./myprogram_tsan

# Build 3: Valgrind (catches MSan-like issues on any compiler)
gcc -std=c11 -g -O0 -o myprogram_valgrind myprogram.c
valgrind --error-exitcode=1 --leak-check=full ./myprogram_valgrind
```

---

# Appendix F — Common Compiler Warnings Explained

These are the warnings you will see most often. Every one of them indicates a real problem.

| Warning | Meaning | Common Fix |
|---|---|---|
| `implicit declaration of function 'f'` | Used `f()` without a prototype. UB in C99+. | Add `#include` for the right header or declare `f` before use. |
| `control reaches end of non-void function` | Function might return without a value. | Add `return` on all paths, or add `__attribute__((noreturn))`. |
| `unused variable 'x'` | Variable declared but never read. | Remove it, or cast to `(void)` to suppress intentionally. |
| `unused parameter 'x'` | Function parameter not used. | Cast: `(void)x;` at the start of the function. |
| `comparison between signed and unsigned` | Mixing `int` and `size_t` in a comparison. | Cast appropriately, or change variable types. |
| `format '%d' expects argument of type 'int', but argument 2 has type 'long'` | Wrong printf format specifier. | Use `%ld` for `long`, `%zu` for `size_t`. |
| `pointer targets in passing argument differ in signedness` | Passing `char *` where `unsigned char *` expected (or vice versa). | Add explicit cast, or fix the type. |
| `integer overflow in expression` | A constant expression overflows at compile time. | Check the arithmetic; use larger types. |
| `suggest parentheses around assignment used as truth value` | `if (x = y)` may be a typo for `if (x == y)`. | Use `if ((x = y) != 0)` if intentional; else fix the typo. |
| `implicit fallthrough` | `switch` case has no `break`. | Add `break`, or annotate with `[[fallthrough]]` (C23). |
| `returning address of local variable` | Returning a pointer to a stack variable. | Allocate with `malloc`, or rethink the design. |
| `array subscript is above array bounds` | Compile-time detectable OOB access. | Fix the index arithmetic. |
| `'%s' is deprecated` | Using a function marked deprecated. | Switch to the recommended replacement. |
| `defined but not used` | `static` function declared but never called. | Remove it, or remove `static` if it should be external. |
| `conversion from 'double' to 'int' may alter its value` | Implicit narrowing conversion. | Use explicit cast: `(int)d`. |

### Enabling warnings:

```bash
gcc -Wall        # Most important warnings
    -Wextra      # Additional warnings
    -Wpedantic   # Strict ISO C compliance
    -Wshadow     # Variable shadows outer scope variable
    -Wconversion # Implicit type conversions
    -Wformat=2   # Stricter format string checking
    -Wnull-dereference  # Potential null dereference paths
    -Wdouble-promotion  # float implicitly promoted to double
    -Wstrict-prototypes # Old-style K&R function declarations
    -Wmissing-prototypes # Functions without prior declarations
    -Werror      # Treat all warnings as errors (use in CI)
```

---

# Appendix G — ASCII, Latin-1, and Unicode Basics for C Programmers

## ASCII (0–127)

Every C programmer should know ASCII values for the characters they work with:

```
Dec  Hex  Char    Dec  Hex  Char    Dec  Hex  Char
  0  00   NUL       32  20   SPC      64  40   @
  7  07   BEL       33  21   !        65  41   A
  8  08   BS        34  22   "        90  5A   Z
  9  09   TAB       39  27   '        97  61   a
 10  0A   LF        47  2F   /       122  7A   z
 13  0D   CR        48  30   0       127  7F   DEL
                    57  39   9
```

Important: `'0'` is 48, not 0. To convert digit character to integer: `c - '0'`. To convert uppercase to lowercase: `c + 32` (or use `tolower()`).

## Strings and Character Encoding in C

C strings are sequences of bytes terminated by `'\0'`. The interpretation of those bytes as characters depends on the *locale* and *encoding*.

In ASCII and UTF-8 (the universal encoding on modern Linux/macOS), all ASCII characters (0–127) are single bytes. UTF-8 encodes non-ASCII Unicode characters as 2–4 byte sequences, all with high bits set (values 128–255). This means:

- `strlen()` counts bytes, not characters. For UTF-8, `strlen("café")` is 5 (the é takes 2 bytes), not 4.
- `str[i]` accesses the i-th byte, not the i-th character.
- `<ctype.h>` functions (`isalpha`, `toupper`, etc.) work correctly only for ASCII characters. For non-ASCII, use locale-aware functions or a Unicode library (like ICU or utf8proc).

## Working With UTF-8

For basic UTF-8 processing:

```c
/* Count Unicode code points in a UTF-8 string: */
size_t utf8_strlen(const char *s) {
    size_t count = 0;
    while (*s) {
        /* Skip continuation bytes (10xxxxxx = 0x80–0xBF) */
        if ((*s & 0xC0) != 0x80) count++;
        s++;
    }
    return count;
}
```

For production use, link against a library rather than re-implementing Unicode handling.

---

# Appendix H — POSIX API Quick Reference

## Process Management

```c
#include <unistd.h>
#include <sys/wait.h>

pid_t getpid(void);               /* current process ID */
pid_t getppid(void);              /* parent process ID */
pid_t fork(void);                 /* create child process */
int   execvp(const char *file, char *const argv[]); /* replace process */
pid_t waitpid(pid_t pid, int *status, int options); /* wait for child */

/* Status macros: */
WIFEXITED(status)    /* did child exit normally? */
WEXITSTATUS(status)  /* exit code if WIFEXITED */
WIFSIGNALED(status)  /* did child die from signal? */
WTERMSIG(status)     /* signal number if WIFSIGNALED */
```

## File I/O

```c
#include <fcntl.h>
#include <unistd.h>

int     open(const char *path, int flags, mode_t mode);
ssize_t read(int fd, void *buf, size_t count);
ssize_t write(int fd, const void *buf, size_t count);
int     close(int fd);
off_t   lseek(int fd, off_t offset, int whence); /* SEEK_SET, SEEK_CUR, SEEK_END */
int     fstat(int fd, struct stat *statbuf);
int     fsync(int fd);      /* flush to disk */
int     fdatasync(int fd);  /* flush data only */
```

## Memory Mapping

```c
#include <sys/mman.h>

void *mmap(void *addr, size_t length, int prot, int flags, int fd, off_t offset);
int   munmap(void *addr, size_t length);
int   mprotect(void *addr, size_t len, int prot);
int   msync(void *addr, size_t length, int flags);

/* Protection flags: */
PROT_READ | PROT_WRITE | PROT_EXEC | PROT_NONE

/* Map flags: */
MAP_PRIVATE   /* copy-on-write: changes not written back to file */
MAP_SHARED    /* changes written back to file */
MAP_ANONYMOUS /* not backed by a file (use fd=-1, offset=0) */
```

## Signals

```c
#include <signal.h>

int sigaction(int sig, const struct sigaction *act, struct sigaction *oldact);
int kill(pid_t pid, int sig);
int raise(int sig);
int sigprocmask(int how, const sigset_t *set, sigset_t *oldset);
/* how: SIG_BLOCK, SIG_UNBLOCK, SIG_SETMASK */

/* sigset_t operations: */
sigemptyset(&set);
sigfillset(&set);
sigaddset(&set, SIGINT);
sigdelset(&set, SIGTERM);
sigismember(&set, SIGHUP);
```

## Sockets

```c
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <netdb.h>

int socket(int domain, int type, int protocol);
int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
int listen(int sockfd, int backlog);
int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);
int connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
ssize_t send(int sockfd, const void *buf, size_t len, int flags);
ssize_t recv(int sockfd, void *buf, size_t len, int flags);
int setsockopt(int sockfd, int level, int optname, const void *optval, socklen_t optlen);
int getaddrinfo(const char *node, const char *service,
                const struct addrinfo *hints, struct addrinfo **res);
void freeaddrinfo(struct addrinfo *res);
```

## Threading (pthreads)

```c
#include <pthread.h>

int pthread_create(pthread_t *thread, const pthread_attr_t *attr,
                   void *(*start)(void *), void *arg);
int pthread_join(pthread_t thread, void **retval);
int pthread_detach(pthread_t thread);

/* Mutex: */
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;
int pthread_mutex_lock(pthread_mutex_t *mutex);
int pthread_mutex_trylock(pthread_mutex_t *mutex);
int pthread_mutex_unlock(pthread_mutex_t *mutex);
int pthread_mutex_destroy(pthread_mutex_t *mutex);

/* Condition variable: */
pthread_cond_t cond = PTHREAD_COND_INITIALIZER;
int pthread_cond_wait(pthread_cond_t *cond, pthread_mutex_t *mutex);
int pthread_cond_timedwait(pthread_cond_t *cond, pthread_mutex_t *mutex,
                            const struct timespec *abstime);
int pthread_cond_signal(pthread_cond_t *cond);
int pthread_cond_broadcast(pthread_cond_t *cond);

/* Thread-local storage: */
pthread_key_t key;
pthread_key_create(&key, destructor);
pthread_setspecific(key, value);
void *value = pthread_getspecific(key);
```

---

# Appendix I — Checklist: Shipping Production C Code

Use this checklist before any production release.

## Code Quality

- [ ] All compiler warnings resolved (`-Wall -Wextra -Wpedantic`)
- [ ] No `TODO`, `FIXME`, or `HACK` comments in release paths
- [ ] Every function has a clear contract (preconditions, postconditions, ownership)
- [ ] All public API functions are documented
- [ ] All complex logic has explanatory comments

## Memory Safety

- [ ] Every `malloc`/`calloc`/`realloc` return value checked for `NULL`
- [ ] Every allocated object has exactly one owner
- [ ] No pointer used after `free` (dangling pointer)
- [ ] No double free (pointers set to `NULL` after free, or ownership clearly transferred)
- [ ] No out-of-bounds array access (arrays paired with lengths; `snprintf` used instead of `sprintf`)
- [ ] No stack-allocated arrays larger than ~64 KB
- [ ] No return of pointers to local variables

## String Safety

- [ ] `gets` never used (removed in C11)
- [ ] `strcpy`/`strcat`/`sprintf` never used
- [ ] `snprintf` used for all formatted writes to buffers
- [ ] `fgets` with explicit size used for line input
- [ ] All user-controlled strings sanitized before use as format strings

## Integer Safety

- [ ] No unchecked signed integer arithmetic where overflow is possible
- [ ] No `count * size` without overflow check before allocation
- [ ] No mixing signed and unsigned in comparisons without explicit casts
- [ ] Shift counts checked to be non-negative and less than the type width

## Error Handling

- [ ] Every error-returning function call is checked
- [ ] Error paths clean up all resources before returning
- [ ] `errno` checked immediately after failure, before any other library call
- [ ] No silent error suppression (ignoring return values)

## Security

- [ ] No user-controlled format strings
- [ ] All buffer writes bounded
- [ ] Integer values from untrusted sources validated before use as lengths/indices
- [ ] Compiled with `-fstack-protector-strong -D_FORTIFY_SOURCE=2 -fPIE -pie`

## Concurrency

- [ ] All shared data accesses protected by mutex or atomic operations
- [ ] Locks acquired in consistent order (to prevent deadlock)
- [ ] No non-async-signal-safe functions called from signal handlers
- [ ] ThreadSanitizer run on all tests

## Testing

- [ ] Unit tests for all public API functions
- [ ] Edge cases tested (empty input, max values, NULL pointers)
- [ ] Tests run with ASan + UBSan enabled
- [ ] Tests run under Valgrind
- [ ] Negative tests: what happens on invalid input?

## Build

- [ ] Debug and release builds both produce zero warnings
- [ ] Build is reproducible (no timestamps in output that affect behavior)
- [ ] All external dependencies documented with version requirements
- [ ] Clean build from scratch succeeds

---

# Appendix J — Glossary of Terms

**Abstract machine:** The imaginary computer defined by the C standard against which the behavior of C programs is specified. Real compilers implement the abstract machine via the "as-if" rule.

**ABI (Application Binary Interface):** The interface between compiled code and the operating system, or between compiled modules. Defines calling conventions, data layout, name mangling, and binary compatibility.

**Alignment:** The constraint that an object's address must be a multiple of a given power of two. `int` typically requires 4-byte alignment; `double` requires 8-byte alignment.

**Arena allocator:** A memory allocator that allocates from a large pre-allocated block. Individual objects cannot be freed; the entire arena is freed at once.

**ASan (AddressSanitizer):** A compiler-based tool that instruments code to detect memory errors at runtime.

**AoS / SoA:** Array-of-Structs vs Struct-of-Arrays. Two ways to organize collections of objects, with different cache behavior characteristics.

**BSS segment:** The segment of a process's address space holding uninitialized or zero-initialized global and static variables. Zeroed by the OS before program start.

**Cache line:** The minimum unit of memory transfer between RAM and CPU cache. Typically 64 bytes on x86. Accessing any byte in a cache line loads the entire line.

**Calling convention:** The protocol by which function calls are made — which registers hold arguments, how the stack is managed, and who cleans up arguments.

**Dangling pointer:** A pointer that refers to memory that has been freed or whose lifetime has ended. Dereferencing a dangling pointer is undefined behavior.

**Data race:** A concurrency error where two threads access the same memory location concurrently, at least one access is a write, and the accesses are not synchronized. A data race is undefined behavior in C11.

**Data segment:** The segment of a process's address space holding initialized global and static variables.

**Decay:** The implicit conversion of an array expression to a pointer to its first element. Occurs in almost all expression contexts except `sizeof`, `&`, and `_Alignof`.

**Double free:** Calling `free` twice on the same pointer. Undefined behavior that often corrupts the allocator's free list.

**Endianness:** The order in which bytes of a multi-byte value are stored in memory. Little-endian stores the least significant byte first; big-endian stores the most significant byte first.

**External linkage:** A name with external linkage is visible across all translation units. The default for non-`static` functions and global variables.

**Forward declaration:** A declaration that introduces a name without providing a complete definition. For structs, enables opaque pointer patterns.

**Free list:** The data structure maintained by a memory allocator to track available blocks of memory.

**Heap:** The memory region from which dynamic allocations (`malloc`) are served. Managed by the allocator; grows upward.

**Implementation-defined behavior:** Behavior that the C standard allows to vary between implementations, but requires the implementation to document.

**Include guard:** A preprocessor pattern (`#ifndef HEADER_H / #define HEADER_H / ... / #endif`) that prevents a header file from being included more than once in the same translation unit.

**Internal linkage:** A name with internal linkage is visible only within its translation unit. Applied by `static` at file scope.

**Lvalue:** An expression that designates an object (has a location in memory). Can appear on the left side of an assignment.

**Memory model:** The rules governing how memory accesses in a concurrent program appear to different threads. C11 defines a formal memory model based on sequenced-before and happens-before relations.

**NULL pointer:** A pointer with a null pointer value. Guaranteed to compare equal to zero. Dereferencing it is undefined behavior.

**Object:** A region of data storage whose contents can represent values. Has a type, address, and lifetime.

**Opaque pointer:** A pointer to a struct type whose definition is not visible to the user of the API. Enables encapsulation in C.

**One Definition Rule (ODR):** In C, a name with external linkage may be *declared* many times but must be *defined* exactly once across all translation units.

**Padding:** Bytes inserted by the compiler between struct members or after the last member to satisfy alignment requirements.

**Pool allocator:** A memory allocator optimized for fixed-size objects. Maintains a free list of same-sized blocks; allocation and deallocation are O(1).

**Provenance:** The concept, increasingly formalized in C standards, that pointers carry information about which object they are allowed to point to. Relevant to strict aliasing and future C memory models.

**Relocation:** The process by which the linker patches addresses in machine code to reflect the final positions of symbols in the executable.

**Restrict:** A type qualifier that promises the compiler a pointer is the only way to access a particular object in the relevant scope. Enables aggressive optimization.

**Rvalue:** An expression that produces a value but does not designate a persistent object. Cannot appear on the left side of an assignment.

**Sequence point:** A point in program execution at which all side effects of previous evaluations are complete. Replaced in C11 by the more precise "sequenced-before" relation.

**Stack frame:** The region of the stack allocated for a single function call. Contains local variables, saved registers, and the return address.

**Static storage duration:** Objects that exist for the entire lifetime of the program. Initialized before `main` begins.

**Strict aliasing:** The rule that a program may only access an object through a pointer to a compatible type (or a character type). Violations are undefined behavior and enable aggressive compiler optimizations.

**Symbol table:** A table in an object file listing the names defined and referenced by that file, along with their addresses/offsets and attributes (global/local, defined/undefined).

**Text segment:** The segment of a process's address space containing the compiled machine code and read-only data (string literals, `const` globals).

**Translation unit:** A single source file after preprocessing. The fundamental unit of C compilation.

**Type punning:** Accessing the stored bytes of one type as if they were another type. Legal in C using `memcpy` or union; illegal via pointer casting (strict aliasing violation).

**UB (Undefined Behavior):** Behavior for which the C standard imposes no requirements. The compiler may assume UB does not occur, and may optimize based on this assumption. See Chapter 21 and Appendix C.

**Unspecified behavior:** Behavior where the C standard allows several outcomes but requires one of them to occur (unlike UB, which allows anything). The program is correct; the result is simply unpredictable.

**Use-after-free:** Accessing memory after calling `free` on it. The memory may have been reallocated; the access is undefined behavior and often exploitable.

**VLA (Variable-Length Array):** An array whose size is determined at runtime (C99). Allocated on the stack. Optional in C11 and later.

**Volatile:** A type qualifier telling the compiler not to optimize away accesses to the qualified object, because the object may change outside the compiler's knowledge (hardware registers, signal handlers, `setjmp`).

---

# MINI PROJECTS: COMPLETE IMPLEMENTATIONS

The following section provides the complete implementation of each mini-project from the curriculum. Each project builds on the prior ones and demonstrates the concepts of its associated chapters.

---

## Mini Project 1: Command-Line Calculator

*Chapters 1–6. Demonstrates: functions, control flow, stdio, recursion.*

```c
/* calc.c — simple command-line calculator
   Usage: calc "3 + 4 * 2"
   Supports: + - * / ( ) integer arithmetic
*/
#include <stdio.h>
#include <stdlib.h>
#include <ctype.h>
#include <string.h>

/* --- Lexer --- */
typedef enum { TOK_NUM, TOK_PLUS, TOK_MINUS, TOK_MUL, TOK_DIV,
               TOK_LPAREN, TOK_RPAREN, TOK_END, TOK_ERR } TokType;
typedef struct { TokType type; long value; } Token;

static const char *g_input;
static Token      g_current;

static void skip_ws(void)      { while (isspace((unsigned char)*g_input)) g_input++; }
static void next_token(void) {
    skip_ws();
    if (*g_input == '\0')      { g_current.type = TOK_END; return; }
    if (isdigit((unsigned char)*g_input)) {
        char *end;
        g_current.value = strtol(g_input, &end, 10);
        g_input = end;
        g_current.type = TOK_NUM;
        return;
    }
    switch (*g_input) {
        case '+': g_current.type = TOK_PLUS;   break;
        case '-': g_current.type = TOK_MINUS;  break;
        case '*': g_current.type = TOK_MUL;    break;
        case '/': g_current.type = TOK_DIV;    break;
        case '(': g_current.type = TOK_LPAREN; break;
        case ')': g_current.type = TOK_RPAREN; break;
        default:  g_current.type = TOK_ERR;    break;
    }
    g_input++;
}

/* --- Recursive Descent Parser ---
   Grammar:
   expr    := term   (('+' | '-') term)*
   term    := factor (('*' | '/') factor)*
   factor  := NUMBER | '(' expr ')' | '-' factor
*/
static long parse_expr(void);
static long parse_term(void);
static long parse_factor(void);

static long parse_factor(void) {
    if (g_current.type == TOK_MINUS) {
        next_token();
        return -parse_factor();
    }
    if (g_current.type == TOK_LPAREN) {
        next_token();
        long val = parse_expr();
        if (g_current.type != TOK_RPAREN) {
            fprintf(stderr, "Error: expected ')'\n"); exit(1);
        }
        next_token();
        return val;
    }
    if (g_current.type != TOK_NUM) {
        fprintf(stderr, "Error: expected number\n"); exit(1);
    }
    long val = g_current.value;
    next_token();
    return val;
}

static long parse_term(void) {
    long val = parse_factor();
    while (g_current.type == TOK_MUL || g_current.type == TOK_DIV) {
        TokType op = g_current.type;
        next_token();
        long rhs = parse_factor();
        if (op == TOK_MUL) val *= rhs;
        else {
            if (rhs == 0) { fprintf(stderr, "Error: division by zero\n"); exit(1); }
            val /= rhs;
        }
    }
    return val;
}

static long parse_expr(void) {
    long val = parse_term();
    while (g_current.type == TOK_PLUS || g_current.type == TOK_MINUS) {
        TokType op = g_current.type;
        next_token();
        long rhs = parse_term();
        if (op == TOK_PLUS) val += rhs; else val -= rhs;
    }
    return val;
}

int main(int argc, char *argv[]) {
    if (argc != 2) {
        fprintf(stderr, "Usage: %s \"expression\"\n", argv[0]);
        return 1;
    }
    g_input = argv[1];
    next_token();
    long result = parse_expr();
    if (g_current.type != TOK_END) {
        fprintf(stderr, "Error: unexpected token\n");
        return 1;
    }
    printf("%ld\n", result);
    return 0;
}
```

---

## Mini Project 2: String Tokenizer Library

*Chapters 6–9. Demonstrates: pointers, strings, arrays, header design.*

```c
/* tokenizer.h */
#ifndef TOKENIZER_H
#define TOKENIZER_H
#include <stddef.h>

/*
 * Tokenizer: splits a string into tokens by delimiter characters.
 * Does NOT modify the original string (unlike strtok).
 * Thread-safe.
 */
typedef struct Tokenizer Tokenizer;

/* Create a tokenizer for 'str', splitting on chars in 'delimiters'.
 * Returns NULL on allocation failure.
 * Caller must call tokenizer_free when done. */
Tokenizer *tokenizer_create(const char *str, const char *delimiters);

/* Get the next token. Returns a null-terminated copy (caller must free),
 * or NULL if no more tokens. */
char *tokenizer_next(Tokenizer *t);

/* How many tokens remain (computed on first call). */
size_t tokenizer_count(Tokenizer *t);

void tokenizer_free(Tokenizer *t);
#endif
```

```c
/* tokenizer.c */
#include "tokenizer.h"
#include <stdlib.h>
#include <string.h>

struct Tokenizer {
    char       *str;        /* own copy of input string */
    const char *delimiters;
    char       *pos;        /* current scan position */
};

Tokenizer *tokenizer_create(const char *str, const char *delimiters) {
    Tokenizer *t = malloc(sizeof(Tokenizer));
    if (!t) return NULL;
    t->str = strdup(str);
    if (!t->str) { free(t); return NULL; }
    t->delimiters = delimiters;
    t->pos = t->str;
    return t;
}

char *tokenizer_next(Tokenizer *t) {
    /* skip leading delimiters */
    while (*t->pos && strchr(t->delimiters, *t->pos)) t->pos++;
    if (!*t->pos) return NULL;  /* no more tokens */

    char *start = t->pos;
    /* advance to next delimiter or end */
    while (*t->pos && !strchr(t->delimiters, *t->pos)) t->pos++;

    size_t len = (size_t)(t->pos - start);
    char *tok = malloc(len + 1);
    if (!tok) return NULL;
    memcpy(tok, start, len);
    tok[len] = '\0';
    return tok;
}

size_t tokenizer_count(Tokenizer *t) {
    /* save and restore position */
    char *saved = t->pos;
    t->pos = t->str;
    size_t count = 0;
    char *tok;
    while ((tok = tokenizer_next(t)) != NULL) { free(tok); count++; }
    t->pos = saved;
    return count;
}

void tokenizer_free(Tokenizer *t) {
    if (t) { free(t->str); free(t); }
}
```

---

## Mini Project 3: Memory Pool Allocator

*Chapters 7–10. Demonstrates: dynamic memory, alignment, custom allocators.*

```c
/* pool.h */
#ifndef POOL_H
#define POOL_H
#include <stddef.h>

/*
 * Pool allocator: fast fixed-size object allocation.
 * All objects in a pool are the same size.
 * Allocation and deallocation are O(1).
 */
typedef struct Pool Pool;

Pool  *pool_create(size_t object_size, size_t objects_per_chunk);
void  *pool_alloc(Pool *p);
void   pool_free(Pool *p, void *obj);
void   pool_destroy(Pool *p);
size_t pool_capacity(const Pool *p);
size_t pool_used(const Pool *p);
#endif
```

```c
/* pool.c */
#include "pool.h"
#include <stdlib.h>
#include <string.h>
#include <assert.h>

/* Each free slot holds a pointer to the next free slot. */
typedef struct FreeNode { struct FreeNode *next; } FreeNode;

typedef struct Chunk {
    struct Chunk *next;
    char          data[];   /* flexible array: objects stored here */
} Chunk;

struct Pool {
    size_t    obj_size;          /* size of each object (>= sizeof(FreeNode)) */
    size_t    objs_per_chunk;    /* number of objects per chunk */
    Chunk    *chunks;            /* linked list of allocated chunks */
    FreeNode *free_list;         /* singly-linked list of free objects */
    size_t    total_allocated;   /* total objects allocated across all chunks */
    size_t    in_use;            /* currently in-use objects */
};

static size_t max_sz(size_t a, size_t b) { return a > b ? a : b; }

Pool *pool_create(size_t object_size, size_t objects_per_chunk) {
    assert(objects_per_chunk > 0);
    Pool *p = calloc(1, sizeof(Pool));
    if (!p) return NULL;
    /* each slot must be large enough to hold a FreeNode pointer */
    p->obj_size = max_sz(object_size, sizeof(FreeNode));
    p->objs_per_chunk = objects_per_chunk;
    return p;
}

static int pool_grow(Pool *p) {
    size_t chunk_data_size = p->obj_size * p->objs_per_chunk;
    Chunk *c = malloc(sizeof(Chunk) + chunk_data_size);
    if (!c) return -1;
    c->next   = p->chunks;
    p->chunks = c;

    /* Link new slots onto the free list */
    char *slot = c->data;
    for (size_t i = 0; i < p->objs_per_chunk; i++) {
        FreeNode *node = (FreeNode *)slot;
        node->next  = p->free_list;
        p->free_list = node;
        slot += p->obj_size;
    }
    p->total_allocated += p->objs_per_chunk;
    return 0;
}

void *pool_alloc(Pool *p) {
    if (!p->free_list && pool_grow(p) != 0) return NULL;
    FreeNode *node = p->free_list;
    p->free_list = node->next;
    p->in_use++;
    /* Zero the returned memory (like calloc) */
    memset(node, 0, p->obj_size);
    return node;
}

void pool_free(Pool *p, void *obj) {
    if (!obj) return;
    FreeNode *node = (FreeNode *)obj;
    node->next   = p->free_list;
    p->free_list = node;
    p->in_use--;
}

void pool_destroy(Pool *p) {
    if (!p) return;
    Chunk *c = p->chunks;
    while (c) {
        Chunk *next = c->next;
        free(c);
        c = next;
    }
    free(p);
}

size_t pool_capacity(const Pool *p) { return p->total_allocated; }
size_t pool_used(const Pool *p)     { return p->in_use; }
```

---

## Mini Project 4: Generic Dynamic Array with Macros

*Chapters 8–11, 13. Demonstrates: pointers, macros, struct layout, generics.*

```c
/* vec.h — type-safe generic dynamic array via macros */
#ifndef VEC_H
#define VEC_H
#include <stdlib.h>
#include <string.h>
#include <assert.h>

/*
 * Usage:
 *   VEC_DEFINE(IntVec, int)
 *   IntVec v = VEC_INIT;
 *   vec_push(&v, 42);
 *   printf("%d\n", v.data[0]);
 *   vec_free(&v);
 */

#define VEC_DEFINE(TypeName, T)                                             \
typedef struct {                                                             \
    T      *data;                                                            \
    size_t  size;                                                            \
    size_t  capacity;                                                        \
} TypeName;                                                                  \
                                                                             \
static inline int TypeName##_push(TypeName *v, T value) {                   \
    if (v->size == v->capacity) {                                            \
        size_t new_cap = v->capacity ? v->capacity * 2 : 8;                 \
        T *nd = realloc(v->data, new_cap * sizeof(T));                       \
        if (!nd) return -1;                                                  \
        v->data = nd; v->capacity = new_cap;                                 \
    }                                                                        \
    v->data[v->size++] = value;                                              \
    return 0;                                                                \
}                                                                            \
                                                                             \
static inline T TypeName##_get(const TypeName *v, size_t i) {               \
    assert(i < v->size);                                                     \
    return v->data[i];                                                       \
}                                                                            \
                                                                             \
static inline void TypeName##_free(TypeName *v) {                           \
    free(v->data);                                                           \
    v->data = NULL; v->size = v->capacity = 0;                               \
}

#define VEC_INIT { NULL, 0, 0 }

/* Convenience shorthand: */
#define vec_push(v, val)   _Generic((v),                                    \
    IntVec*:    IntVec_push,                                                 \
    default:    IntVec_push)(v, val)  /* caller uses typed version directly */

#endif /* VEC_H */
```

```c
/* example usage */
#include "vec.h"
#include <stdio.h>

VEC_DEFINE(IntVec, int)
VEC_DEFINE(DblVec, double)

int main(void) {
    IntVec iv = VEC_INIT;
    for (int i = 0; i < 20; i++) IntVec_push(&iv, i * i);
    for (size_t i = 0; i < iv.size; i++)
        printf("%d ", IntVec_get(&iv, i));
    printf("\n");
    IntVec_free(&iv);

    DblVec dv = VEC_INIT;
    DblVec_push(&dv, 3.14);
    DblVec_push(&dv, 2.71);
    printf("pi=%.2f e=%.2f\n", DblVec_get(&dv, 0), DblVec_get(&dv, 1));
    DblVec_free(&dv);
    return 0;
}
```

---

## Mini Project 5: INI Configuration File Parser

*Chapters 9, 13, 17, 20. Demonstrates: file I/O, strings, error handling.*

```c
/* ini.h */
#ifndef INI_H
#define INI_H
#include <stddef.h>

#define INI_MAX_KEY    128
#define INI_MAX_VALUE  512
#define INI_MAX_SECTION 128

typedef void (*IniCallback)(const char *section,
                             const char *key,
                             const char *value,
                             void       *userdata);

/* Parse an INI file. Calls cb for each key=value found.
 * Returns 0 on success, negative on error (errno set). */
int ini_parse_file(const char *path, IniCallback cb, void *userdata);

/* Parse INI from a string. */
int ini_parse_string(const char *str, IniCallback cb, void *userdata);

#endif
```

```c
/* ini.c */
#include "ini.h"
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#include <errno.h>

static char *trim(char *s) {
    while (isspace((unsigned char)*s)) s++;
    if (!*s) return s;
    char *end = s + strlen(s) - 1;
    while (end > s && isspace((unsigned char)*end)) *end-- = '\0';
    return s;
}

static int ini_parse_lines(char *buf, IniCallback cb, void *ud) {
    char section[INI_MAX_SECTION] = "";
    char *line = buf;

    while (*line) {
        char *eol = strchr(line, '\n');
        if (eol) *eol = '\0';

        char *p = trim(line);

        if (*p == '\0' || *p == ';' || *p == '#') {
            /* empty or comment */
        } else if (*p == '[') {
            char *end = strchr(p + 1, ']');
            if (end) {
                *end = '\0';
                snprintf(section, sizeof(section), "%s", p + 1);
            }
        } else {
            char *eq = strchr(p, '=');
            if (eq) {
                *eq = '\0';
                char *key = trim(p);
                char *val = trim(eq + 1);
                /* strip inline comment */
                char *comment = strchr(val, ';');
                if (comment) { *comment = '\0'; trim(val); }
                cb(section, key, val, ud);
            }
        }

        if (!eol) break;
        line = eol + 1;
    }
    return 0;
}

int ini_parse_file(const char *path, IniCallback cb, void *ud) {
    int rc = 0;
    FILE *f = NULL;
    char *buf = NULL;

    f = fopen(path, "r");
    if (!f) { rc = -1; goto done; }

    if (fseek(f, 0, SEEK_END) != 0) { rc = -2; goto done; }
    long sz = ftell(f);
    if (sz < 0)                      { rc = -3; goto done; }
    if (fseek(f, 0, SEEK_SET) != 0) { rc = -4; goto done; }

    buf = malloc((size_t)sz + 1);
    if (!buf) { rc = -5; goto done; }

    size_t read = fread(buf, 1, (size_t)sz, f);
    buf[read] = '\0';

    rc = ini_parse_lines(buf, cb, ud);

done:
    free(buf);
    if (f) fclose(f);
    return rc;
}

int ini_parse_string(const char *str, IniCallback cb, void *ud) {
    char *copy = strdup(str);
    if (!copy) return -1;
    int rc = ini_parse_lines(copy, cb, ud);
    free(copy);
    return rc;
}
```

---

## Mini Project 6: Thread-Safe Logging Library

*Chapters 12–14, 17, 20, 24. Demonstrates: modular design, file I/O, concurrency.*

```c
/* log.h */
#ifndef LOG_H
#define LOG_H

typedef enum {
    LOG_LEVEL_DEBUG = 0,
    LOG_LEVEL_INFO,
    LOG_LEVEL_WARN,
    LOG_LEVEL_ERROR,
    LOG_LEVEL_FATAL
} LogLevel;

/* Initialize. If path is NULL, log to stderr. Returns 0 on success. */
int  log_init(const char *path, LogLevel min_level);
void log_write(LogLevel level, const char *file, int line,
               const char *fmt, ...);
void log_flush(void);
void log_close(void);

#define LOG_DEBUG(...) log_write(LOG_LEVEL_DEBUG, __FILE__, __LINE__, __VA_ARGS__)
#define LOG_INFO(...)  log_write(LOG_LEVEL_INFO,  __FILE__, __LINE__, __VA_ARGS__)
#define LOG_WARN(...)  log_write(LOG_LEVEL_WARN,  __FILE__, __LINE__, __VA_ARGS__)
#define LOG_ERROR(...) log_write(LOG_LEVEL_ERROR, __FILE__, __LINE__, __VA_ARGS__)
#define LOG_FATAL(...) do { log_write(LOG_LEVEL_FATAL, __FILE__, __LINE__, \
                            __VA_ARGS__); log_flush(); abort(); } while(0)

#endif
```

```c
/* log.c */
#include "log.h"
#include <stdio.h>
#include <stdlib.h>
#include <stdarg.h>
#include <time.h>
#include <pthread.h>
#include <string.h>

static FILE          *g_file      = NULL;
static LogLevel       g_min_level = LOG_LEVEL_INFO;
static pthread_mutex_t g_lock     = PTHREAD_MUTEX_INITIALIZER;

static const char *level_str[] = {
    "DEBUG", "INFO ", "WARN ", "ERROR", "FATAL"
};

int log_init(const char *path, LogLevel min_level) {
    pthread_mutex_lock(&g_lock);
    if (g_file && g_file != stderr) fclose(g_file);
    g_min_level = min_level;
    if (path) {
        g_file = fopen(path, "a");
        if (!g_file) { g_file = stderr; pthread_mutex_unlock(&g_lock); return -1; }
    } else {
        g_file = stderr;
    }
    pthread_mutex_unlock(&g_lock);
    return 0;
}

void log_write(LogLevel level, const char *file, int line,
               const char *fmt, ...) {
    if (level < g_min_level) return;

    struct timespec ts;
    clock_gettime(CLOCK_REALTIME, &ts);
    struct tm tm;
    localtime_r(&ts.tv_sec, &tm);

    char timebuf[32];
    strftime(timebuf, sizeof(timebuf), "%Y-%m-%d %H:%M:%S", &tm);

    /* Strip directory from file path */
    const char *basename = strrchr(file, '/');
    basename = basename ? basename + 1 : file;

    pthread_mutex_lock(&g_lock);
    FILE *out = g_file ? g_file : stderr;
    fprintf(out, "%s.%03ld [%s] %s:%d: ",
            timebuf, ts.tv_nsec / 1000000,
            level_str[level], basename, line);
    va_list args;
    va_start(args, fmt);
    vfprintf(out, fmt, args);
    va_end(args);
    fputc('\n', out);
    if (level >= LOG_LEVEL_ERROR) fflush(out);  /* flush errors immediately */
    pthread_mutex_unlock(&g_lock);
}

void log_flush(void) {
    pthread_mutex_lock(&g_lock);
    if (g_file) fflush(g_file);
    pthread_mutex_unlock(&g_lock);
}

void log_close(void) {
    pthread_mutex_lock(&g_lock);
    if (g_file && g_file != stderr) { fclose(g_file); g_file = NULL; }
    pthread_mutex_unlock(&g_lock);
}
```

---

## Mini Project 7: Thread-Safe Queue

*Chapters 10, 11, 24. Demonstrates: concurrency, dynamic memory, synchronization.*

```c
/* tsqueue.h — thread-safe FIFO queue */
#ifndef TSQUEUE_H
#define TSQUEUE_H
#include <stddef.h>

typedef struct TSQueue TSQueue;

/* Create a queue. max_size = 0 means unbounded. */
TSQueue *tsq_create(size_t max_size);
void     tsq_destroy(TSQueue *q);

/* Push: blocks if queue is full (bounded). Returns 0 on success, -1 on shutdown. */
int      tsq_push(TSQueue *q, void *item);

/* Pop: blocks until an item is available or the queue is shut down.
 * Returns 0 on success; -1 if shut down and empty. */
int      tsq_pop(TSQueue *q, void **item);

/* Signal all waiters that the queue is shutting down. */
void     tsq_shutdown(TSQueue *q);
size_t   tsq_size(TSQueue *q);
#endif
```

```c
/* tsqueue.c */
#include "tsqueue.h"
#include <stdlib.h>
#include <pthread.h>

typedef struct Node { void *item; struct Node *next; } Node;

struct TSQueue {
    Node            *head, *tail;
    size_t           size;
    size_t           max_size;
    int              shutdown;
    pthread_mutex_t  lock;
    pthread_cond_t   not_empty;
    pthread_cond_t   not_full;
};

TSQueue *tsq_create(size_t max_size) {
    TSQueue *q = calloc(1, sizeof(TSQueue));
    if (!q) return NULL;
    q->max_size = max_size;
    pthread_mutex_init(&q->lock, NULL);
    pthread_cond_init(&q->not_empty, NULL);
    pthread_cond_init(&q->not_full, NULL);
    return q;
}

void tsq_destroy(TSQueue *q) {
    if (!q) return;
    Node *n = q->head;
    while (n) { Node *next = n->next; free(n); n = next; }
    pthread_mutex_destroy(&q->lock);
    pthread_cond_destroy(&q->not_empty);
    pthread_cond_destroy(&q->not_full);
    free(q);
}

int tsq_push(TSQueue *q, void *item) {
    Node *node = malloc(sizeof(Node));
    if (!node) return -1;
    node->item = item; node->next = NULL;

    pthread_mutex_lock(&q->lock);
    while (!q->shutdown && q->max_size > 0 && q->size >= q->max_size)
        pthread_cond_wait(&q->not_full, &q->lock);

    if (q->shutdown) {
        pthread_mutex_unlock(&q->lock);
        free(node);
        return -1;
    }

    if (q->tail) q->tail->next = node; else q->head = node;
    q->tail = node;
    q->size++;
    pthread_cond_signal(&q->not_empty);
    pthread_mutex_unlock(&q->lock);
    return 0;
}

int tsq_pop(TSQueue *q, void **item) {
    pthread_mutex_lock(&q->lock);
    while (!q->shutdown && q->size == 0)
        pthread_cond_wait(&q->not_empty, &q->lock);

    if (q->size == 0) {
        pthread_mutex_unlock(&q->lock);
        return -1;  /* shutdown and empty */
    }

    Node *node = q->head;
    q->head = node->next;
    if (!q->head) q->tail = NULL;
    q->size--;
    *item = node->item;
    pthread_cond_signal(&q->not_full);
    pthread_mutex_unlock(&q->lock);
    free(node);
    return 0;
}

void tsq_shutdown(TSQueue *q) {
    pthread_mutex_lock(&q->lock);
    q->shutdown = 1;
    pthread_cond_broadcast(&q->not_empty);
    pthread_cond_broadcast(&q->not_full);
    pthread_mutex_unlock(&q->lock);
}

size_t tsq_size(TSQueue *q) {
    pthread_mutex_lock(&q->lock);
    size_t s = q->size;
    pthread_mutex_unlock(&q->lock);
    return s;
}
```

---

## Mini Project 8: Minimal HTTP/1.0 Server

*Chapters 25–27. Demonstrates: sockets, processes, I/O, networking.*

```c
/* httpd.c — a minimal single-threaded HTTP/1.0 server
   Serves static files from the current directory.
   Usage: ./httpd <port>
*/
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <errno.h>
#include <signal.h>

#define BUF_SIZE   4096
#define MAX_PATH   512

static void send_response(int client, int status, const char *status_msg,
                           const char *content_type, const void *body, size_t body_len) {
    char header[512];
    int hlen = snprintf(header, sizeof(header),
        "HTTP/1.0 %d %s\r\n"
        "Content-Type: %s\r\n"
        "Content-Length: %zu\r\n"
        "Connection: close\r\n"
        "\r\n",
        status, status_msg, content_type, body_len);
    write(client, header, (size_t)hlen);
    if (body && body_len > 0) write(client, body, body_len);
}

static void handle_request(int client) {
    char buf[BUF_SIZE];
    ssize_t n = read(client, buf, sizeof(buf) - 1);
    if (n <= 0) return;
    buf[n] = '\0';

    /* Parse first line: METHOD /path HTTP/x.x */
    char method[16], path[MAX_PATH], proto[16];
    if (sscanf(buf, "%15s %511s %15s", method, path, proto) != 3) {
        send_response(client, 400, "Bad Request", "text/plain", "Bad Request", 11);
        return;
    }

    if (strcmp(method, "GET") != 0) {
        send_response(client, 405, "Method Not Allowed", "text/plain", "Method Not Allowed", 18);
        return;
    }

    /* Sanitize path: reject anything with ".." */
    if (strstr(path, "..")) {
        send_response(client, 403, "Forbidden", "text/plain", "Forbidden", 9);
        return;
    }

    /* Default document */
    const char *file_path = (strcmp(path, "/") == 0) ? "./index.html" : path + 1;

    int fd = open(file_path, O_RDONLY);
    if (fd < 0) {
        const char *msg = "Not Found";
        send_response(client, 404, "Not Found", "text/plain", msg, strlen(msg));
        return;
    }

    struct stat st;
    fstat(fd, &st);

    /* Determine content type from extension */
    const char *ct = "application/octet-stream";
    if (strstr(file_path, ".html")) ct = "text/html";
    else if (strstr(file_path, ".css")) ct = "text/css";
    else if (strstr(file_path, ".js"))  ct = "application/javascript";
    else if (strstr(file_path, ".txt")) ct = "text/plain";

    /* Send headers */
    char header[256];
    int hlen = snprintf(header, sizeof(header),
        "HTTP/1.0 200 OK\r\nContent-Type: %s\r\nContent-Length: %ld\r\n\r\n",
        ct, (long)st.st_size);
    write(client, header, (size_t)hlen);

    /* Send file body */
    char fbuf[BUF_SIZE];
    ssize_t rb;
    while ((rb = read(fd, fbuf, sizeof(fbuf))) > 0)
        write(client, fbuf, (size_t)rb);
    close(fd);
}

int main(int argc, char *argv[]) {
    if (argc != 2) { fprintf(stderr, "Usage: %s <port>\n", argv[0]); return 1; }
    int port = atoi(argv[1]);
    if (port <= 0 || port > 65535) { fprintf(stderr, "Invalid port\n"); return 1; }

    /* Ignore SIGPIPE: write to closed socket returns EPIPE instead of killing process */
    signal(SIGPIPE, SIG_IGN);

    int server = socket(AF_INET, SOCK_STREAM, 0);
    if (server < 0) { perror("socket"); return 1; }

    int opt = 1;
    setsockopt(server, SOL_SOCKET, SO_REUSEADDR, &opt, sizeof(opt));

    struct sockaddr_in addr = {
        .sin_family      = AF_INET,
        .sin_port        = htons((uint16_t)port),
        .sin_addr.s_addr = INADDR_ANY
    };
    if (bind(server, (struct sockaddr *)&addr, sizeof(addr)) < 0) {
        perror("bind"); return 1;
    }
    if (listen(server, 10) < 0) { perror("listen"); return 1; }

    printf("Serving on http://localhost:%d/\n", port);

    for (;;) {
        struct sockaddr_in client_addr;
        socklen_t client_len = sizeof(client_addr);
        int client = accept(server, (struct sockaddr *)&client_addr, &client_len);
        if (client < 0) {
            if (errno == EINTR) continue;
            perror("accept"); break;
        }
        handle_request(client);
        close(client);
    }
    close(server);
    return 0;
}
```

---

## Mini Project 9: Complete Hash Table with Resizing

*Chapters 10, 28–29. Demonstrates: dynamic memory, data structures, load factor management.*

```c
/* ht.h — full-featured hash table: string keys, void* values */
#ifndef HT_H
#define HT_H
#include <stddef.h>

typedef struct HT HT;
typedef void (*HT_FreeValue)(void *value);

HT    *ht_create(size_t initial_capacity);
void   ht_destroy(HT *ht, HT_FreeValue free_val);

/* Returns 0 on success, -1 on allocation failure.
 * If key already exists, replaces the value (old value freed via free_val). */
int    ht_set(HT *ht, const char *key, void *value, HT_FreeValue free_val);

/* Returns value or NULL if not found. */
void  *ht_get(const HT *ht, const char *key);

/* Returns 1 if key existed and was removed, 0 otherwise. */
int    ht_delete(HT *ht, const char *key, HT_FreeValue free_val);

size_t ht_size(const HT *ht);       /* number of entries */
size_t ht_capacity(const HT *ht);   /* total bucket count */

/* Iterate: return 0 from cb to stop */
typedef int (*HT_IterFn)(const char *key, void *value, void *userdata);
void   ht_iter(const HT *ht, HT_IterFn cb, void *userdata);
#endif
```

```c
/* ht.c */
#include "ht.h"
#include <stdlib.h>
#include <string.h>

#define LOAD_FACTOR_MAX 0.70

typedef struct Entry {
    char  *key;
    void  *value;
    int    deleted;
} Entry;

struct HT {
    Entry  *entries;
    size_t  capacity;
    size_t  size;
    size_t  tombstones;
};

static uint32_t fnv1a(const char *s) {
    uint32_t h = 2166136261u;
    for (; *s; s++) { h ^= (unsigned char)*s; h *= 16777619u; }
    return h;
}

HT *ht_create(size_t cap) {
    if (cap < 8) cap = 8;
    /* round up to next power of two */
    size_t p = 1;
    while (p < cap) p <<= 1;
    HT *ht = malloc(sizeof(HT));
    if (!ht) return NULL;
    ht->entries = calloc(p, sizeof(Entry));
    if (!ht->entries) { free(ht); return NULL; }
    ht->capacity   = p;
    ht->size       = 0;
    ht->tombstones = 0;
    return ht;
}

static size_t probe(const HT *ht, const char *key) {
    size_t idx = fnv1a(key) & (ht->capacity - 1);  /* capacity is power of 2 */
    while (ht->entries[idx].key != NULL) {
        if (!ht->entries[idx].deleted && strcmp(ht->entries[idx].key, key) == 0)
            return idx;   /* found */
        idx = (idx + 1) & (ht->capacity - 1);
    }
    return idx;  /* empty slot */
}

static int ht_resize(HT *ht, size_t new_cap, HT_FreeValue free_val);

int ht_set(HT *ht, const char *key, void *value, HT_FreeValue free_val) {
    /* Resize if needed */
    if ((double)(ht->size + ht->tombstones + 1) / (double)ht->capacity > LOAD_FACTOR_MAX) {
        size_t new_cap = ht->capacity * 2;
        if (ht_resize(ht, new_cap, NULL) != 0) return -1;
    }

    size_t idx = fnv1a(key) & (ht->capacity - 1);
    size_t first_tomb = (size_t)-1;

    for (;;) {
        Entry *e = &ht->entries[idx];
        if (e->key == NULL) {
            /* Empty slot: insert here (or at first tombstone) */
            size_t ins = (first_tomb != (size_t)-1) ? first_tomb : idx;
            ht->entries[ins].key     = strdup(key);
            if (!ht->entries[ins].key) return -1;
            ht->entries[ins].value   = value;
            ht->entries[ins].deleted = 0;
            ht->size++;
            if (first_tomb != (size_t)-1) ht->tombstones--;
            return 0;
        }
        if (e->deleted) {
            if (first_tomb == (size_t)-1) first_tomb = idx;
        } else if (strcmp(e->key, key) == 0) {
            /* Update existing */
            if (free_val && e->value) free_val(e->value);
            e->value = value;
            return 0;
        }
        idx = (idx + 1) & (ht->capacity - 1);
    }
}

void *ht_get(const HT *ht, const char *key) {
    size_t idx = fnv1a(key) & (ht->capacity - 1);
    for (;;) {
        Entry *e = &ht->entries[idx];
        if (e->key == NULL) return NULL;   /* not found */
        if (!e->deleted && strcmp(e->key, key) == 0) return e->value;
        idx = (idx + 1) & (ht->capacity - 1);
    }
}

int ht_delete(HT *ht, const char *key, HT_FreeValue free_val) {
    size_t idx = fnv1a(key) & (ht->capacity - 1);
    for (;;) {
        Entry *e = &ht->entries[idx];
        if (e->key == NULL) return 0;  /* not found */
        if (!e->deleted && strcmp(e->key, key) == 0) {
            if (free_val && e->value) free_val(e->value);
            free(e->key);
            e->key     = (char *)1;  /* sentinel: non-null so probing doesn't stop */
            e->value   = NULL;
            e->deleted = 1;
            ht->size--;
            ht->tombstones++;
            return 1;
        }
        idx = (idx + 1) & (ht->capacity - 1);
    }
}

static int ht_resize(HT *ht, size_t new_cap, HT_FreeValue free_val) {
    (void)free_val;
    Entry *old_entries = ht->entries;
    size_t old_cap     = ht->capacity;

    ht->entries = calloc(new_cap, sizeof(Entry));
    if (!ht->entries) { ht->entries = old_entries; return -1; }
    ht->capacity   = new_cap;
    ht->size       = 0;
    ht->tombstones = 0;

    for (size_t i = 0; i < old_cap; i++) {
        Entry *e = &old_entries[i];
        if (e->key && !e->deleted) {
            ht_set(ht, e->key, e->value, NULL);
            free(e->key);
        }
    }
    free(old_entries);
    return 0;
}

void ht_destroy(HT *ht, HT_FreeValue free_val) {
    if (!ht) return;
    for (size_t i = 0; i < ht->capacity; i++) {
        if (ht->entries[i].key && !ht->entries[i].deleted) {
            if (free_val && ht->entries[i].value) free_val(ht->entries[i].value);
            free(ht->entries[i].key);
        }
    }
    free(ht->entries);
    free(ht);
}

void ht_iter(const HT *ht, HT_IterFn cb, void *ud) {
    for (size_t i = 0; i < ht->capacity; i++) {
        Entry *e = &ht->entries[i];
        if (e->key && !e->deleted)
            if (!cb(e->key, e->value, ud)) return;
    }
}

size_t ht_size(const HT *ht)     { return ht->size; }
size_t ht_capacity(const HT *ht) { return ht->capacity; }
```

---

# FINAL NOTES AND FURTHER READING

## Essential Resources

**The C Programming Language, 2nd Edition** — Kernighan and Ritchie. The original. Concise, authoritative, and still worth reading for its prose and philosophy.

**C11 Standard (ISO/IEC 9899:2011)** — The definitive reference. Not easy reading, but the ultimate authority. Available via ISO or drafts freely online (search "n1570").

**SEI CERT C Coding Standard** — Free online. Security-focused rules for every aspect of C. Essential for safety-critical development.

**Computer Systems: A Programmer's Perspective** — Bryant and O'Hallaron. The best book for understanding how programs interact with hardware and the OS.

**Hacking: The Art of Exploitation** — Erickson. Deep dive into how memory corruption vulnerabilities work. Excellent for understanding what you're defending against.

**Linux Programming Interface** — Kerrisk. The definitive reference for POSIX/Linux systems programming. 1500 pages of exhaustive coverage.

## Tools to Learn

- **GDB**: your primary debugger
- **Valgrind**: memory error detection
- **ASan + UBSan**: fast sanitizer suite for development
- **perf**: Linux performance profiling
- **clang-tidy**: automated code analysis
- **scan-build**: Clang static analyzer
- **gcov + lcov**: code coverage
- **Compiler Explorer (godbolt.org)**: see what the compiler generates for any snippet

## Principles to Internalize

1. **Read the warnings.** Every warning is a likely bug. Zero warnings, always.

2. **There is no "probably fine."** Undefined behavior is either correct or it isn't. If you invoke it, the program is wrong.

3. **Measure before optimizing.** The bottleneck is almost never where intuition says.

4. **Own your pointers.** Know exactly who allocates and who frees every object.

5. **Distrust input.** Every byte from outside your process is potentially malicious.

6. **Test with sanitizers.** Code that passes all tests without sanitizers enabled has not been tested.

7. **The compiler is not your enemy.** When it does something surprising, learn why — it is almost certainly exploiting something you didn't expect.

8. **Simplicity is a feature.** Complex code is harder to read, harder to test, and harder to secure.

---

*End of The Definitive C Programming Handbook.*


---

# CHAPTER COMPLETION: SUMMARIES, KEY TAKEAWAYS, EXERCISES, AND INTERVIEW QUESTIONS

*The following section completes every chapter that was missing its closing material — summaries, key takeaways, pitfalls, exercises, and interview questions. These are organized by chapter number.*

---

## Chapter 4 — Operators and Expressions: Completion

### Common Pitfalls

- Using `=` instead of `==` in a condition: `if (x = 0)` sets x to 0 and is always false. Enable `-Wall` to catch this.
- Forgetting that `&` (bitwise AND) has lower precedence than `==`: `x & 0xFF == 0` parses as `x & (0xFF == 0)`.
- Integer overflow: `int x = 100000 * 100000` overflows silently if `int` is 32 bits.
- Relying on evaluation order: `f() + g()` — the order `f` and `g` are called is unspecified.
- Signed right-shift: `x >> n` on negative `x` is implementation-defined (may or may not sign-extend).

### Interview Questions

**Beginner:** What is the difference between `i++` and `++i` in the expression `y = i++`?

**Intermediate:** Why is `INT_MIN / -1` undefined behavior in C?

**Advanced:** Given `unsigned int u = UINT_MAX; int s = -1;`, what does `u > s` evaluate to and why? What should you do instead?

---

## Chapter 5 — Control Flow: Completion

### Common Pitfalls

- Forgetting `break` in a `switch` case leads to fall-through — one of the most common C bugs.
- `if (a); { b(); }` — the semicolon after `if` creates an empty body; `b()` always runs.
- Infinite loops from using `unsigned int` as a countdown: `for (unsigned int i = n; i >= 0; i--)` never terminates because unsigned can never be negative.
- Using `goto` to jump backward, creating implicit loops with no apparent structure.

### Interview Questions

**Beginner:** What is the output of `for (int i = 0; i < 3; i++) printf("%d ", i);`?

**Intermediate:** Why does `for (unsigned int i = 10; i >= 0; i--)` create an infinite loop? How do you fix it?

**Advanced:** The Linux kernel uses `goto` for error handling. Write a function that opens three files and closes all that were successfully opened even if a later open fails, using the `goto`-cleanup pattern.

---

## Chapter 6 — Functions: Completion

### Common Pitfalls

- Returning a pointer to a local variable. The local is destroyed on return; the pointer is dangling.
- Calling a variadic function with the wrong type: `printf("%d", 3.14)` — the `double` is passed but `%d` reads an `int`, producing garbage.
- Using `int f()` instead of `int f(void)`. In C, `int f()` means "takes unspecified arguments" — any call is legal. `int f(void)` means "takes no arguments".
- Assuming tail calls are optimized — TCO is not guaranteed in C. Deep recursion can still stack-overflow.
- Function pointer type mismatch: calling a function through a pointer of incompatible type is undefined behavior.

### Interview Questions

**Beginner:** What is the difference between `int f()` and `int f(void)` in C?

**Intermediate:** Implement `void *my_memset(void *s, int c, size_t n)` without calling any library functions. How does the signature use `void *`?

**Advanced:** Explain calling conventions on x86-64 Linux (System V ABI). How many integer arguments can be passed in registers? What happens with the 7th argument?

---

## Chapter 7 — The Memory Model: Completion

### Interview Questions

**Beginner:** What are the five memory regions of a C process? What lives in each one?

**Intermediate:** A global variable `int g;` and a local variable `int l;` inside `main` — where does each live, when are they initialized, and when are they destroyed?

**Advanced:** Explain the difference between the C abstract machine memory model and the actual memory model of a modern x86-64 processor. What does "sequenced-before" mean in C11, and why does it matter for multithreaded code?

---

## Chapter 8 — Pointers: Completion

### Interview Questions

**Beginner:** What is the value of `p` after `int *p = NULL; p++;`? (Answer: undefined behavior — pointer arithmetic on null pointer is UB.)

**Intermediate:** Given `int a[5] = {1,2,3,4,5};`, explain why `a[3]` and `*(a+3)` and `3[a]` are all identical. What does this tell you about the subscript operator?

**Advanced:** What is the strict aliasing rule? Give a concrete example where violating it causes the compiler to generate wrong code at `-O2`. Show the fix using `memcpy`.

---

## Chapter 9 — Arrays and Strings: Completion

### Common Pitfalls

- Off-by-one in string buffers: `char buf[5]; strcpy(buf, "hello");` — "hello" needs 6 bytes including `'\0'`.
- Using `sizeof` on a pointer parameter to get array length: it gives the pointer size, not the array size.
- `strncpy` not null-terminating when source is longer than n.
- Comparing strings with `==`: this compares addresses (always false for separate literals), not content.
- Iterating a multidimensional array in column-major order when row-major is needed for cache performance.

### Interview Questions

**Beginner:** What is wrong with `char *s = "hello"; s[0] = 'H';`?

**Intermediate:** Implement `char *str_reverse(const char *s)` that returns a newly allocated reversed string. Who is responsible for freeing it?

**Advanced:** Explain why `char buf[8]; memcpy(buf, "hello", 5);` produces a valid C string on some platforms but not others. What invariant is required?

---

## Chapter 10 — Dynamic Memory: Completion

### Interview Questions

**Beginner:** What are the four dynamic memory functions and what does each do?

**Intermediate:** What is wrong with `int *p = malloc(n); p = realloc(p, 2*n);`? Rewrite it correctly.

**Advanced:** Explain how a use-after-free vulnerability can lead to arbitrary code execution. What does AddressSanitizer do differently from Valgrind to detect it, and why is one faster than the other?

---

## Chapter 11 — Structs, Unions, and Bitfields: Completion

### Interview Questions

**Beginner:** Given `struct { char a; int b; };`, what is `sizeof` this struct on a platform where `int` is 4 bytes and requires 4-byte alignment?

**Intermediate:** Explain the difference between `union` and `struct`. Why can reading from a union member other than the last written one be useful in C (but not C++)?

**Advanced:** You are designing a protocol message header that must be exactly 8 bytes and work identically on both big-endian and little-endian systems. What pitfalls do bitfields introduce? What is the correct approach?

---

## Chapter 12 — Scope, Linkage, and Storage Duration: Completion

### Interview Questions

**Beginner:** What does `static` mean when applied to a local variable inside a function?

**Intermediate:** A function-local `static` variable is not thread-safe. Demonstrate the bug with a counter function called from two threads simultaneously. Fix it with `_Atomic`.

**Advanced:** Explain the One Definition Rule in C. What is a tentative definition? Under what circumstances can two translation units define the same name without a linker error?

---

## Chapter 13 — The Preprocessor and Macros: Completion

### Interview Questions

**Beginner:** Why is `#define SQUARE(x) x*x` wrong? Write the correct version.

**Intermediate:** Use an X-macro to generate both an enum and a string-name lookup array for HTTP status codes (200 OK, 404 Not Found, 500 Internal Server Error). Show how adding a new status code requires editing only one place.

**Advanced:** Explain why `#define SAFE_DIV(a,b) ((b) != 0 ? (a)/(b) : 0)` still has a double-evaluation problem. Construct a call that evaluates `b` twice with a side effect.

---

## Chapter 14 — Header Files and Modular Design: Completion

### Key Takeaways

- A header file contains declarations, not definitions (with exceptions: `static inline`, `typedef`, `#define`).
- Include guards or `#pragma once` prevent multiple-inclusion errors.
- The opaque pointer pattern hides implementation details entirely.
- Self-contained headers can be included in any order without depending on other headers being included first.
- Circular header dependencies are resolved with forward declarations.

### Interview Questions

**Beginner:** What is the difference between `#include <stdio.h>` and `#include "myheader.h"`?

**Intermediate:** Explain the opaque pointer pattern. Why can a user call functions on a `Buffer *` without knowing what fields `struct Buffer` contains?

**Advanced:** What does it mean for a library to be ABI-stable? Name three changes to a struct that break ABI compatibility and three changes that do not.

---

## Chapter 15 — Compilation and Linking: Completion

### Key Takeaways

- Object files contain code, data, symbols, and relocation entries.
- The linker resolves symbols, patches addresses, and combines objects.
- Static libraries embed code; shared libraries load at runtime.
- Link-time optimization (LTO) enables cross-TU inlining and dead code elimination.
- Symbol visibility (`static`, `__attribute__((visibility("hidden")))`) controls what is exported.

### Interview Questions

**Beginner:** What is a linker error "undefined reference to `foo`"? How do you fix it?

**Intermediate:** Explain the difference between a static library (`.a`) and a shared library (`.so`). When would you prefer each?

**Advanced:** What is position-independent code (PIC)? Why is `-fPIC` required when building shared libraries? How does the Global Offset Table (GOT) enable PIC?

---

## Chapters 16–19 — Standard Library: Completion

### Chapter 16 Key Takeaways

- The standard library is divided into headers, each covering a functional area.
- `errno` signals errors from library functions; check it immediately after failure.
- `assert` is disabled in release builds (`-DNDEBUG`); use it only for invariants.
- Never check `errno` unless a function has documented that it sets it on failure.

### Chapter 17 Key Takeaways

- `printf` format strings must match argument types exactly — mismatches are undefined behavior.
- Never use user-controlled strings as format arguments: use `printf("%s", user_str)`.
- `snprintf` is always safer than `sprintf` — it bounds the output.
- `fgets` is always safer than `gets` — `gets` is removed in C11.
- Stdio streams are buffered; use `fflush` or close files to ensure data is written.

### Chapter 18 Key Takeaways

- Use `strtol`/`strtod` instead of `atoi`/`atof` — the latter have no error detection.
- `memcpy` requires non-overlapping regions; `memmove` handles overlaps.
- `qsort` comparators must return negative, zero, or positive — never use subtraction for overflow safety.
- `exit` flushes stdio buffers and calls `atexit` handlers; `_Exit` does not.

### Chapter 19 Key Takeaways

- Use `CLOCK_MONOTONIC` for timing elapsed duration; use `CLOCK_REALTIME` only for wall-clock time.
- `setjmp`/`longjmp` do not call destructors or stack unwinding — resource leaks are possible.
- `<ctype.h>` functions require `unsigned char` argument to avoid undefined behavior with negative `char` values.

### Interview Questions (Chapters 16–19 Combined)

**Beginner:** What is the difference between `printf` and `fprintf`? Between `printf` and `sprintf`?

**Intermediate:** Why is `int c = getchar()` correct but `char c = getchar()` is wrong?

**Advanced:** `errno` is documented to be thread-local on POSIX. Why is this necessary? What would go wrong if it were a simple global `int`?

---

## Chapter 20 — Error Handling: Completion

### Key Takeaways

- C error handling is based on return codes and `errno` — there are no exceptions.
- The `goto`-cleanup pattern is idiomatic for functions with multiple resources.
- `assert` checks invariants and is disabled in release; use explicit validation for external input.
- Document every function's error behavior: what it returns on failure, what `errno` is set to.
- Logging should include timestamp, level, file, and line for every message.

### Interview Questions

**Beginner:** What does `perror("fopen")` print when `fopen` fails because the file doesn't exist?

**Intermediate:** Explain the goto-cleanup pattern. Write a function that opens a file, allocates a buffer, reads into it, and closes the file — with correct cleanup at every failure point.

**Advanced:** Design a thread-safe logging library that: accepts a log level and message, writes atomically to a file, never blocks the caller longer than a configurable timeout, and drops messages gracefully when the log file is unavailable.

---

## Chapter 21 — Undefined Behavior: Completion

### Interview Questions

**Beginner:** Is `unsigned int x = UINT_MAX; x++;` undefined behavior? Why or why not?

**Intermediate:** A loop `for (int i = 0; i < n && arr[i] != 0; i++)` — under what conditions might the compiler prove `arr[i] != 0` is always true and eliminate the check? How does signed overflow play a role?

**Advanced:** What is the difference between undefined behavior and implementation-defined behavior? Give two examples of each. Why does the C standard have UB at all instead of simply mandating crash behavior?

---

## Chapter 22 — Security: Completion

### Key Takeaways

- Buffer overflows can overwrite return addresses, enabling arbitrary code execution.
- Integer overflow in size calculations causes under-allocation, leading to heap overflows.
- Format string vulnerabilities allow reading and writing arbitrary memory.
- Use-after-free and double-free corrupt allocator metadata and are exploitable.
- Compiler mitigations (stack canaries, ASLR, NX, RELRO, PIE) make exploitation harder but not impossible.
- Testing with sanitizers (`-fsanitize=address,undefined`) and fuzzing are essential for security.

### Interview Questions

**Beginner:** What makes `gets(buf)` dangerous? What should you use instead?

**Intermediate:** A function calls `printf(user_input)` where `user_input` comes from the network. Describe three different exploits possible via format string attack.

**Advanced:** Explain how a heap use-after-free can become a code execution vulnerability. What allocator behavior allows an attacker to control what gets placed in the freed region? How does ASLR affect exploitability?

---

## Chapter 23 — Performance: Completion

### Key Takeaways

- Profile before optimizing — the bottleneck is rarely where you expect.
- Cache efficiency dominates: sequential access is 10–50× faster than random access for large data.
- SoA (struct-of-arrays) enables vectorization and better cache utilization for batch operations.
- Compiler optimizations at `-O2` are extensive; `restrict` and predictable control flow help further.
- Custom allocators eliminate `malloc` overhead in hot paths.
- Benchmark with `CLOCK_MONOTONIC`, many iterations, and report the median.

### Interview Questions

**Beginner:** Why is iterating a 2D array row-by-row faster than column-by-column?

**Intermediate:** What is false sharing? Write an example where two threads accessing different variables in the same struct cause performance degradation, and fix it.

**Advanced:** Explain how the compiler uses `-O2` to eliminate a redundant load. Write a function where the compiler cannot eliminate a load due to a missing `restrict` qualifier, and show how adding `restrict` allows the optimization.

---

## Chapter 24 — Concurrency: Completion

### Key Takeaways

- A data race is undefined behavior in C11 — it does not merely produce wrong values, it invalidates the program.
- Mutexes provide mutual exclusion; condition variables provide signaling.
- `pthread_cond_wait` must always be inside a `while` loop — spurious wakeups are real.
- Memory ordering in atomics controls visibility across threads: `seq_cst` is safe but slower; `relaxed` is fastest but provides no ordering.
- ThreadSanitizer detects data races efficiently and should run in CI.
- Lock ordering must be consistent across all code paths to prevent deadlock.

### Interview Questions

**Beginner:** What is a data race? Give a minimal example with two threads.

**Intermediate:** Implement a thread-safe reference-counted pointer in C. What atomicity guarantees does the reference count need? Why is `memory_order_release` on decrement and `memory_order_acquire` on the deallocation check necessary?

**Advanced:** Explain the ABA problem in lock-free programming. Why does a compare-and-swap succeed when it should fail? What is the tagged-pointer technique for addressing it?

---

## Chapter 25 — Processes and Signals: Completion

### Key Takeaways

- `fork` creates an exact copy of the calling process; the child gets a fresh copy of all memory.
- `exec` replaces the process image; use `fork`+`exec` to run a new program.
- `waitpid` reaps children; without it, children become zombies.
- Signal handlers can only safely call async-signal-safe functions.
- Use `sigaction` instead of `signal` — the latter has implementation-defined behavior.
- The `sig_atomic_t` flag pattern (set in handler, checked in main loop) is the standard way to handle signals safely.

### Exercises

**Easy:** Write a program that forks a child, has the child sleep for 2 seconds, then has the parent use `waitpid` with `WNOHANG` to poll for completion every 100ms, printing a dot each time it polls.

**Medium:** Write a program that installs a `SIGTERM` handler to perform graceful shutdown (flushing a log file and printing "shutting down") rather than immediate termination.

**Hard:** Implement a simple job-control shell that can run background processes (`./prog &`), list them (`jobs`), and wait for them (`wait`). Handle `SIGCHLD` to reap completed background jobs automatically.

### Interview Questions

**Beginner:** What happens when a child process exits but the parent never calls `wait`?

**Intermediate:** Why can't you call `printf` from a signal handler? What can you do instead to communicate from a signal handler to the main program?

**Advanced:** Explain the self-pipe trick for safely integrating signal handling with an event loop using `select`/`epoll`.

---

## Chapter 26 — Files, Pipes, and IPC: Completion

### Key Takeaways

- POSIX file descriptors are the OS-level I/O primitive; `FILE *` wraps them with buffering.
- Pipes are unidirectional byte streams for IPC between related processes.
- `mmap` maps files or anonymous memory into the address space — efficient for large files and shared memory.
- `epoll` (Linux) and `kqueue` (BSD) are O(1) I/O multiplexing, superior to O(n) `select`/`poll` for many FDs.
- Always close unused pipe ends after `fork` to ensure proper EOF signaling.

### Interview Questions

**Beginner:** What are the three standard file descriptors and their numbers?

**Intermediate:** Write a shell pipe `ls | wc -l` using `fork`, `pipe`, `dup2`, and `exec`. Explain each step.

**Advanced:** Compare `read`/`write` I/O with `mmap` I/O for reading a 1 GB file. Under what access patterns does each outperform the other, and why?

---

## Chapter 27 — Sockets and Networking: Completion

### Key Takeaways

- The socket API is the same across UDP, TCP, IPv4, IPv6, and UNIX domain sockets.
- Always use `getaddrinfo` for name resolution — it handles IPv4/IPv6 correctly.
- Network byte order is big-endian; use `htons`/`htonl`/`ntohs`/`ntohl` for multi-byte fields.
- Set `SO_REUSEADDR` on server sockets to avoid `EADDRINUSE` on restart.
- Handle `SIGPIPE` (or use `MSG_NOSIGNAL`) when writing to closed sockets.
- For production servers, use non-blocking sockets with `epoll`/`kqueue` for scalability.

### Interview Questions

**Beginner:** What is the difference between TCP and UDP? When would you use each?

**Intermediate:** What does `SO_REUSEADDR` do, and why do production servers always set it?

**Advanced:** Explain the C10K problem. What architectural choices must a server make to handle 10,000 concurrent connections? Compare blocking-per-thread, non-blocking with `epoll`, and thread-pool approaches.

---

## Chapter 28 — Linear Data Structures: Completion

### Key Takeaways

- Dynamic arrays are the default choice: O(1) amortized push, O(1) random access, excellent cache behavior.
- Linked lists have O(1) insertion at a known node but O(n) access and poor cache behavior.
- Ring buffers are ideal for fixed-capacity FIFOs (producer-consumer queues) with zero allocation per operation.
- Generic containers in C use `void *`, macros, or code generation — each with tradeoffs.
- Always store arrays with their length; a pointer alone is incomplete.

### Interview Questions

**Beginner:** What is the amortized time complexity of pushing to a dynamic array that doubles when full? Explain why.

**Intermediate:** Implement a stack using a singly linked list. Which end of the list should be the top of the stack and why?

**Advanced:** Implement an intrusive linked list (where the `next` pointer is embedded in the user's struct) using `offsetof` and `container_of`. Explain why this avoids per-node allocation overhead and is used extensively in the Linux kernel.

---

## Chapter 29 — Trees and Hash Tables: Completion

### Key Takeaways

- BSTs have O(log n) average operations but O(n) worst case on sorted input — balance is required.
- Binary heaps support O(log n) insert and O(log n) extract-min, stored in a contiguous array.
- Hash tables provide O(1) average lookup with appropriate hash functions and load factors.
- FNV-1a and SipHash are good general-purpose hash functions for string keys.
- Keep hash table load factor below 0.7 for open addressing; resize by doubling.
- Tombstones (deleted markers) are required for correct open-addressing delete.

### Interview Questions

**Beginner:** What is the worst-case time complexity of a BST search? When does this worst case occur?

**Intermediate:** Why does a hash table need a tombstone when deleting with open addressing? What goes wrong if you just clear the slot?

**Advanced:** Implement consistent hashing for a distributed cache. How does it minimize remapping when nodes are added or removed?

---

## Chapter 30 — Debugging Tools: Completion

### Key Takeaways

- Compile with `-g -O0` for debugging; `-g -O2` for profiling (but beware inlining hiding frames).
- GDB's `backtrace`, `frame`, `info locals`, and `print` are the daily-use commands.
- ASan catches memory errors at ~2× overhead — use it always in development.
- UBSan catches undefined behavior at runtime — combine with ASan.
- Valgrind's Memcheck detects uninitialized reads that ASan misses; use it for thorough testing.
- `strace` reveals system call behavior; `ltrace` reveals library call behavior — invaluable for debugging "why is this slow/hanging" questions.

### Interview Questions

**Beginner:** How do you set a breakpoint in GDB and inspect a variable's value?

**Intermediate:** A program segfaults. Walk through exactly how you would diagnose it using GDB and a core dump.

**Advanced:** AddressSanitizer reports a use-after-free. Describe how ASan instruments the code to catch this. Why does it use "shadow memory" and what does the shadow memory encode?

---

## Chapter 31 — Build Systems, Testing, and CI: Completion

### Key Takeaways

- Makefiles use tabs (not spaces) for recipe indentation — this is non-negotiable.
- Automatic variables (`$@`, `$<`, `$^`) and pattern rules make Makefiles maintainable.
- CMake generates platform-appropriate build files; prefer it over raw Makefiles for cross-platform projects.
- Unit testing in C requires discipline: isolate functions, mock dependencies, test error paths.
- Code coverage below 80% leaves too many untested paths; aim for 90%+ for critical modules.
- CI should build with ASan+UBSan, run tests, and fail on any sanitizer report.

### Interview Questions

**Beginner:** What does `make` do when you run it with no arguments?

**Intermediate:** Explain the difference between a unit test and an integration test. Why is it harder to write unit tests for C code that calls `malloc` directly?

**Advanced:** Design a CI pipeline for a C library. What build configurations, test suites, static analysis tools, and sanitizer runs should it include? In what order should they run to fail fast?

---

## Chapter 32 — Portability: Completion

### Key Takeaways

- Never assume `sizeof(int) == 4`, `sizeof(long) == 8`, or `sizeof(void *) == sizeof(long)`.
- Use `<stdint.h>` types for exact-width integers; use `size_t` for sizes.
- Endianness affects multi-byte values in files, network protocols, and memory-mapped structures.
- Data models (LP64, LLP64, ILP32) determine sizes of `long` and pointers on different platforms.
- Feature-test macros (`_POSIX_C_SOURCE`, `_GNU_SOURCE`) select available POSIX features.
- Cross-compilation requires matching sysroot, toolchain, and library versions.

### Interview Questions

**Beginner:** Why might `sizeof(long)` differ between Linux and Windows on 64-bit systems?

**Intermediate:** Write a function that serializes a `uint32_t` to a byte array in little-endian order, portably (i.e., without relying on the host byte order).

**Advanced:** What is the C ABI? If two translation units are compiled by different compilers (or even the same compiler with different flags), under what conditions can they safely call each other's functions?

---

## Chapter 33 — Compiler Internals: Completion

### Key Takeaways

- Compilers work on intermediate representations (IR), not directly on C source.
- The "as-if" rule allows any transformation preserving observable behavior.
- Constant folding, propagation, inlining, and dead-code elimination happen before register allocation.
- Alias analysis is central to optimization: the compiler needs to know whether two pointers can point to the same memory.
- LTO enables cross-module optimization at link time — significant for large codebases.
- `__attribute__((pure))` and `__attribute__((const))` provide the compiler alias and side-effect information enabling more optimization.

### Interview Questions

**Beginner:** What does the `-O2` flag do? Name three optimizations it enables.

**Intermediate:** Why does adding `restrict` to a pointer parameter sometimes make a function significantly faster? Write a concrete example.

**Advanced:** Explain how a compiler performs constant propagation through a function call boundary with LTO enabled. What information must be present in the `.o` file for LTO to work?

---

## Chapter 34 — C23: Completion

### Key Takeaways

- C23 standardizes `typeof`, `nullptr`, `static_assert` without message, and `[[attributes]]`.
- `_BitInt(N)` enables arbitrary-precision-width integers without libraries.
- `#embed` eliminates the need for external tools to embed binary resources.
- `<stdbit.h>` standardizes bit manipulation (count ones, leading zeros, etc.).
- `<stdckdint.h>` provides checked arithmetic (`ckd_add`, `ckd_mul`) to detect overflow.
- Compiler support as of 2024: GCC 14+ and Clang 18+ cover most C23 features.

### Interview Questions

**Beginner:** What does `[[nodiscard]]` do in C23? Give an example of when you'd use it.

**Intermediate:** How does `ckd_add(&result, a, b)` differ from checking `a > INT_MAX - b` manually? What advantage does the checked arithmetic API provide?

**Advanced:** `#embed` is designed to allow zero-overhead embedding of binary resources. Explain why the traditional alternatives (xxd-generated arrays, linker tricks with `.incbin`) are worse, and what the C23 specification guarantees about `#embed` evaluation.

---

## Chapter 35 — API Design and Production C: Completion

### Key Takeaways

- A good C API is minimal, consistent, complete, and safe by default.
- Document ownership semantics explicitly: every pointer parameter is either owned or borrowed.
- Test with sanitizers before every release; make sanitizer-clean a CI requirement.
- Code review checklists catch the most common bugs systematically.
- Logging must be structured (timestamp, level, file, line) and thread-safe.
- API stability requires careful versioning — once published, removing a symbol breaks users.

### Interview Questions

**Beginner:** What is the difference between a library's public API and its implementation?

**Intermediate:** You are designing a C API for a connection pool. How do you represent a connection? How do you handle the case where no connections are available? How do you ensure the caller returns connections properly?

**Advanced:** Explain semantic versioning for C libraries (MAJOR.MINOR.PATCH). What changes require a MAJOR bump (ABI break)? What changes can be MINOR (backward-compatible addition)? How do you use symbol versioning in GNU ld to maintain multiple ABI versions simultaneously?

---

# COMPLETE CHAPTER CHECKLIST

Every chapter in this handbook covers the following for each major concept:

| Section | Purpose |
|---|---|
| **What** | Precise definition |
| **Why** | Design rationale and problem solved |
| **Intuition** | Mental model and analogy |
| **How It Works** | Runtime, language, and compiler behavior |
| **Syntax** | Formal syntax and variations |
| **Examples** | Minimal → practical → real-world → production |
| **Memory Model** | Stack/heap layout with ASCII diagrams where relevant |
| **Compiler Perspective** | Optimization opportunities and generated behavior |
| **Performance Analysis** | Cost model, tradeoffs, cache behavior |
| **Common Mistakes** | Beginner, intermediate, and professional pitfalls |
| **Debugging** | Symptoms, root cause, fix, detection tools |
| **Best Practices** | Modern C usage and production guidelines |

---

# QUICK-REFERENCE CARD

*Cut out and keep at your desk.*

## The Five Memory Regions

```
Stack   — local variables, function frames, grows down
Heap    — malloc/free, grows up
BSS     — uninitialized/zero globals
Data    — initialized globals
Text    — code and read-only data (string literals)
```

## Pointer Quick Reference

```c
int  x   = 42;
int *p   = &x;      // p holds address of x
int  v   = *p;      // dereference: v = 42
*p       = 100;     // write through pointer: x = 100
p++;                // advance by sizeof(int)

int * const cp   = &x;   // const pointer: p fixed, *p mutable
const int * pc   = &x;   // pointer to const: p mutable, *p fixed
const int * const cpc = &x; // both fixed
```

## Undefined Behavior: Never Do This

```c
int *p = NULL; *p = 1;         // null deref
int arr[5]; arr[5] = 1;        // out of bounds
int x; use(x);                 // uninitialized read
int i = INT_MAX; i++;          // signed overflow
char *s = "hi"; s[0] = 'H';   // modify string literal
free(p); *p = 1;               // use after free
free(p); free(p);              // double free
int i = 0; i = i++;           // sequence point violation
*(float*)&my_int;              // strict aliasing violation
x << 32;  // (int is 32 bits) // shift >= width
```

## Essential Compiler Flags

```bash
-std=c11 -Wall -Wextra -Wpedantic    # always
-g -O0                                # debug build
-O2 -DNDEBUG                          # release build
-fsanitize=address,undefined          # development
-fstack-protector-strong              # security
-D_FORTIFY_SOURCE=2                   # security
```

## Memory Safety Rules

```
1. Every malloc → one free (no more, no less)
2. Never use a pointer after free
3. Set pointer to NULL after free
4. Always check malloc for NULL
5. Never return pointer to local variable
6. Always bounds-check before writing to a buffer
7. Use snprintf, never sprintf
8. Use fgets, never gets
```

## Printf Format Specifiers

```
%d   int              %u   unsigned int
%ld  long             %lu  unsigned long
%lld long long        %llu unsigned long long
%zu  size_t           %td  ptrdiff_t
%f   double           %e   scientific
%g   shorter of %f/%e %s   char *
%p   void *           %c   char
%x   hex lowercase    %X   hex uppercase
%%   literal %
```

## GDB One-Liners

```bash
gdb ./prog                    # start debugger
run                           # run program
break main                    # breakpoint at main
break file.c:42               # breakpoint at line
next / step / finish          # step over/into/out
print x / info locals         # inspect values
backtrace                     # call stack
continue                      # resume
quit                          # exit
```

## Sanitizer Commands

```bash
# Compile:
gcc -fsanitize=address,undefined -g -O1 prog.c -o prog
# Run normally — errors printed automatically

# ThreadSanitizer (separate, incompatible with ASan):
gcc -fsanitize=thread -g prog.c -o prog -lpthread

# Valgrind (no recompile needed):
valgrind --leak-check=full ./prog
```

---

*This handbook is complete. From the abstract machine to production systems programming, every concept in C has been covered in depth. The best next step is to write code — start with the mini-projects, read the Linux kernel source, and study real codebases that use what you have learned here.*

*Good luck, and may your pointers always be valid.*

