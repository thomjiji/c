<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**

- [4.4 Basic of functions](#44-basic-of-functions)
- [4.2 Functions returning non-integers](#42-functions-returning-non-integers)
- [4.3 External variables](#43-external-variables)
- [4.4 Scope rules](#44-scope-rules)
- [4.6 Static variables](#46-static-variables)
- [4.7 Register variables](#47-register-variables)
- [4.8 Block structure](#48-block-structure)
- [4.9 Initialization](#49-initialization)
- [4.11 The C preprocessor](#411-the-c-preprocessor)
    - [4.11.1 File inclusion](#4111-file-inclusion)
    - [4.11.2 Macro substitution](#4112-macro-substitution)
- [Exercises](#exercises)
    - [Exercise 4-1](#exercise-4-1)
        - [Question](#question)
    - [Exercise 4-2](#exercise-4-2)
        - [Question](#question-1)

<!-- markdown-toc end -->


# 4.4 Basic of functions

If the return type is omitted, `int` is assumed.

如果一个函数的返回值没有写的话，默认为返回 `int`。

It is not illegal, but probably a sign of trouble, if a function returns a value from one place and no value from another.

如果一个函数在一些情况下会返回值，而在另一些情况下不返回值，虽然这种情况是合法的，但往往意味着程序有一些问题。所以不要这么做。例如与其写成这样：

```c
// Function that inconsistently returns a value
int inconsistentFunction(int x) {
    if (x > 0) {
        return x;  // Returns a value if x is positive
    }
    // No return statement for other cases (x <= 0)
}
```

不如：

```c
int consistentFunction(int x) {
    if (x > 0) {
        return x;
    }
    return -1;  // Default value for non-positive x
}
```

# 4.2 Functions returning non-integers

The calling routine must know that `atof` returns a non-`int` value. One way to ensure this is to declare `atof` explicitly in the calling routine.

如果一个函数返回的值不是 `int`，那么调用它的 caller 必须要知道这个函数究竟返回什么。确保这一点的一个方法是，在 caller routine 里再声明一次这个返回值不是 `int` 的函数。比如在如下的代码中：

```c
#include <ctype.h>
#include <stdio.h>

#define MAXLINE 100

double atof(char s[]) {...}

int getline_s(char s[], int lim) {...}

/* rudimentary calculator */
int main()
{
    double sum, atof(char[]);
    char line[MAXLINE];
    int getline_s(char[], int);

    sum = 0;
    while (getline_s(line, MAXLINE) > 0)
        printf("\t%g\n", sum += atof(line));

    return 0;
}
```

`main` 函数当中的 `double atof(char[])` 这一行代码声明了 `atof` 函数，明确的告诉 `main` 它的返回值是 `double` 而不是默认的 `int`。

The function `atof` must be declared and defined consistently. If `atof` itself and the call to it in `main` have inconsistent types in the same source file, the error will be detected by the compiler. But if (as is more likely) `atof` were compiled separately, the mismatch would not be detected, `atof` would return a `double` that `main` would treat as an `int`, and meaningless answers would result.

这一段在我理解里，更加说明了在 `main` 中使用 `atof` 函数之前，去特别的声明一下 `atof` 的重要性：如果 `atof` 被写在与 `main` 函数不同的文件里，这时它不与 `main` 函数同时编译。因为 `main` 函数在被编译的当下，在它的视角看来 `atof` 是不存在的。所以当它在自己的 function body 中调用 `atof` 的时候，`atof` 被隐性的声明（请看分隔符下面的章节，紧接着讲到这一点）。而函数被隐性的声明的时候，它被默认的认为是返回 `int` 的。这时候 mismatch 就出现了：`main` 函数认为 `atof` 是返回 `int` 的，但 `atof` 实际上返回的是 `double`。

---

In the light of what we have said about how declarations must match definitions, this might seem surprising. The reason a mismatch can happen is that **if there is no function prototype, a function is implicitly declared by its first appearance in an expression**, such as

```c
sum += atof(line)
```

If a name that has not been previously declared occurs in an expression and is followed by a left parenthesis, it is declared by context to be a function name, the function is assumed to return an `int`, and nothing is assumed about its arguments.

如果没有 function prototype，一个函数在它第一次出现在表达式中的时候被*隐性的*声明。

If the function takes arguments, declare them; if it takes no arguments, use `void`.

如果一个函数明确不接受任何参数，应该在函数声明的括号内明确写上 `void`：

```c
int func(void);
```

# 4.3 External variables

External variables are defined outside of any function, and are thus potentially available to many functions.

Functions themselves are always external, because C does not allow functions to be defined inside other functions.

External variables, are permanent, they retain values from one function invocation to the next.

External variable 和 `extern` 还不是一个东西。

# 4.4 Scope rules

The *scope* of a name is the part of the program within which the name can be used. For an automatic variable declared at the beginning of a function, the scope is the function in which the name is declared. Local variable of the same name in different functions are unrelated.

The scope of an external variable or a function lasts from the point at which it is declared to the end of the file being compiled. For example, if `main`, `sp`, `val`, `push`, and `pop` are defined in one file, in the order shown above, that is,

```c
main() { ... }

int sp = 0;
double val[MAXVAL];

void push(double f) { ...  }

double pop(void) { ...  }
```

then the variables `sp` and `val` may be used in `push` and `pop` simply by naming them; no further declarations are needed. But these names are not visible in `main`, nor are `push` and `pop` themselves.

那么你可以直接在 `push` 和 `pop` 中直接使用变量 `sp` 和 `val`，不需要进一步的声明。但这些名称在 `main` 中是不可见的，`push` 和 `pop` 函数对于 `main` 也是不可见的。

If an external variable is to be referred to before it is defined, or if it is defined in a different source file from the one where it is being used, then an `extern` declaration is mandatory.

关于 `extern` 的一些其他的点，原书中讲的不是很系统，具体可见页码 80 的末尾和页码 81 的前半段。以下是 ChatGPT 对 `extern` 究竟是个什么东西的介绍：

> In C, the `extern` keyword is used to declare a variable or function in a way that informs the compiler that the actual definition of that variable or function is provided elsewhere, typically in another source file. It serves as a declaration without providing the actual storage or implementation details.

# 4.6 Static variables

The `static` declaration, applied to an external variable or function, limits the scope of that object to the rest of the source file being compiled.

Static storage is specified by prefixing the normal declaration with the word `static`.

Normally, function names are global, visible to any part of the entire program. If a function is declared `static`, however, its name is invisible outside of the file in which it is declared.

Internal static variables are local to a particular function just as automatic variables are, but unlike automatics, they remain in existence rather than coming and going each time the function is activated. This means that internal `static` variables provide *private, permanent storage* within a single function.

# 4.7 Register variables

A register declaration advises the compiler that the variable in question will be heavily used. The idea is that register variables are to be placed in machine registers, which may result in smaller and faster programs.

```c
register int x;
register char x;
```

The register declaration can only be applied to automatic variables and to the formal parameters of a function. In this latter case, it looks like

```c
f(register unsigned m, register long n)
{
    resiter int i;
    ...
}
```

# 4.8 Block structure

An automatic variable declared and initialized in a block is initialized each time the block is entered. A `static` variable is initialized only the fist time the block is entered.

# 4.9 Initialization

In the absence of explicit initialization, external and static variables are guaranteed to be initialized to zero; automatic and register variables have undefined (i.e., garbage) initial values.

For external and static variables, the initializer must be a constant expression; the initialization is done once, conceptually before the program begins execution. For automatic and register variables, it is done each time the function or block is entered.

# 4.11 The C preprocessor

## 4.11.1 File inclusion

Any source line of the form

```c
#include "filename"
```

or

```c
#include <filename>
```

is replaced by the **contents** of the file *filename*. If the *filename* is quoted, searching for the file typically begins where the source program was found.

## 4.11.2 Macro substitution

Normally the replacement text is the rest of the line, but a long definition may be continued onto several lines by placing a `\` at the end of each line to be continued.

The scope of a name defined with `#define` is from its point of definition to the end of the source file being compiled.

Any name may be defined with any replacement text. For example

```c
#define forever for (;;) // infinite loop
```

defines a new word, `forever`, for an infinite loop.

---

It's also possible to define macros with arguments, so the replacement text can be different for different calls of the macro.

```c
#define max(A, B) ((A) > (B) ? (A) : (B))
```

Although it looks like a function call, a use of `max` *expands into in-line code*.

---

Names may be undefined with `#undef`, usually to ensure that a routine is really a function, not a macro:

```c
#undef getchar

int getchar(void) { ...  }
```

Formal parameters are not replaced within quoted strings. If, however, a parameter name is preceded by a # in the replacement text, the combination will be expanded into a quoted string with the parameter replaced by the actual argument. 

# Exercises

## Exercise 4-1

### Question

Write the function `strrindex(s, t)`, which returns the position of the *rightmost* occurrence of `t` in `s`, or `-1` if there is none.

## Exercise 4-2

### Question

Extend `atof` to handle scientific notation of the form

```
123.45e-6
```

Where a floating-point number may be followed be `e` or `E` and an optionally signed exponent.
