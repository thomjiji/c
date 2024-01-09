<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**

- [1.2 Variables and arithmetic expressions](#12-variables-and-arithmetic-expressions)
- [1.4 Symbolic constants](#14-symbolic-constants)
- [1.5 Character input and output](#15-character-input-and-output)
    - [1.5.1 File copying](#151-file-copying)
    - [1.5.2 Character counting](#152-character-counting)
    - [1.5.3 Line counting](#153-line-counting)
    - [1.5.4 Word counting](#154-word-counting)
- [1.6 Arrays](#16-arrays)
- [1.7 Functions](#17-functions)
- [1.8 Argument — Call by value](#18-argument--call-by-value)
- [1.9 Character arrays](#19-character-arrays)
- [1.10 External variables and scope](#110-external-variables-and-scope)
- [Exercises](#exercises)
    - [Exercise 1-9](#exercise-1-9)
        - [Question](#question)
        - [Answer](#answer)
    - [Exercise 1-10](#exercise-1-10)
        - [Question](#question-1)
        - [Answer](#answer-1)
    - [Exercise 1-13](#exercise-1-13)
        - [Question](#question-2)
    - [Exercise 1-14](#exercise-1-14)
        - [Question](#question-3)
    - [Exercise 1-15](#exercise-1-15)
        - [Question](#question-4)
        - [Answer](#answer-2)
    - [Exercise 1-16](#exercise-1-16)
        - [Question](#question-5)
    - [Exercise 1-17](#exercise-1-17)
        - [Question](#question-6)

<!-- markdown-toc end -->

# 1.2 Variables and arithmetic expressions

```c
#include <stdio.h>

/*
 * print Fahrenheit-Celsius table for fahr = 0, 20, ..., 300;
 * floating-point version.
 */
int main()
{
    float fahr, celsius;
    int lower, upper, step;

    lower = 0;   // lower limit of temperature table
    upper = 300; // upper limit
    step = 20;   // step size

    fahr = lower;
    printf("F\tC\n");
    while (fahr <= upper)
    {
        celsius = (5.0 / 9.0) * (fahr - 32.0);
        printf("%3.0f %6.1f\n", fahr, celsius);
        fahr = fahr + step;
    }
}
```

| Specifier | Description                                                        |
| :-------- | :----------------------------------------------------------------- |
| `%d`      | print as decimal integer                                           |
| `%6d`     | print as decimal integer, at least 6 characters wide               |
| `%f`      | print as floating point                                            |
| `%6f`     | print as floating point, as least 6 characters wide                |
| `%.2f`    | print as floating point, 2 characters after decimal point          |
| `%6.2f`   | print as floating point, as least 6 wide and 2 after decimal point |

# 1.4 Symbolic constants

A `#define` line defines a *symbolic name* or *symbolic constant* to be a particular string of characters.

```c
#define name replacement_text // Here, "name" is so-called symbolic constant
```

# 1.5 Character input and output

Text input or output, regardless of where is originates or where is goes to, is dealt with as **streams of characters**.

A *text stream* is a sequence of characters divided into lines; each line consists of zero or more character followed by a newline character.

## 1.5.1 File copying

```c
#include <stdio.h>

// copy input to output; 1st version
int main()
{
    int c;

    c = getchar();
    while (c != EOF)
    {
        putchar(c);
        c = getchar();
    }
}
```

The problem is distinguishing the end of the input from valid data. The solution is that `getchar` returns a distinctive value when there is no more input, a value that cannot be confused with any real character. This value is called `EOF`, for "end of file". We must declare `c` to be a type big enough to `EOF` in addition to any possible `char`. Therefor we use `int`.

`EOF` means "end of file". It's an integer defined in `<stdio.h>`.

In C, any assignment, such as

```c
c = getchar()
```

is an expression and **has a value**, which is the value of the left hand side after the assignment. This means that an assignment can appear as part of a larger expression.

```c
#include <stdio.h>

// copy input to output; 2nd version
int main()
{
    int c;

    while ((c = getchar()) != EOF) // <= assignment can appear as part of a larger expression
    {
        putchar(c);
    }
}
```

## 1.5.2 Character counting

```c
#include <stdio.h>

// count characters in input; 2nd version
int main()
{
    double nc;

    for (nc = 0; getchar() != EOF; ++nc)
        ;   // <= null statement

    printf(".0f\n", nc);
}
```

The grammatical rules of C require that a `for` statement have a body, so there is a *null statement* `;` to satisfy that requirement.

## 1.5.3 Line counting

A character written between single quotes represents an integer value equal to the numerical value of the character in the machine's character set. This is called a *character constant*, although **it is just another way to write a small integer.**

For example, 'A' is a character constant; in the ASCII character set its value is 65. the internal representation of the character A.

## 1.5.4 Word counting

```c
#include <stdio.h>

#define IN 1  // inside a word
#define OUT 0 // outside a word

// count lines, words, and characters in input
int main()
{
    int c, nl, nw, nc, state;

    state = OUT;
    nl = nw = nc = 0;

    while ((c = getchar()) != EOF)
    {
        ++nc;
        if (c == '\n')
            ++nl;
        if (c == ' ' || c == '\n' || c == '\t')
            state = OUT;
        else if (state == OUT)
        {
            state = IN;
            ++nw;
        }
    }

    printf("%d %d %d\n", nl, nw, nc);
}
```

The line

```c
nl = nw = nc = 0;
```

sets all three variables to zero. This is not a special case, but a consequence of the fact that **an assignment is an expression with a value and assignments associate from right to left.** It's as if we had written

```c
nl = (nw = (nc = 0));
```

# 1.6 Arrays

```c
#include <stdio.h>

// count digits, whitespace, others
int main()
{
    int c, i, nwhite, nother;
    int ndigit[10];

    nwhite = nother = 0;
    for (i = 0; i < 10; ++i)
        ndigit[i] = 0;

    while ((c = getchar()) != EOF)
    {
        if (c >= '0' && c <= '9')
            ++ndigit[c - '0'];
        else if (c == ' ' || c == '\n' || c == '\t')
            ++nwhite;
        else
            ++nother;
    }

    printf("digits = ");
    for (i = 0; i < 10; ++i)
    {
        printf(" %d", ndigit[i]);
    }

    printf(", white space = %d, other = %d\n", nwhite, nother);
}
```

```c
        if (c >= '0' && c <= '9')
```

determines whether the character in `c` is a digit. If it is, the numeric value of that digit is

```c
c - '0'
```

This works only if '0', '1', ..., '9' have consecutive increasing values.

我们的目标是把这些 character ('0', '1', ..., '9' ) 变成其对应的 digits：例如 '0' => 0, '1' => 1。怎么转换呢？

首先我们要知道，这些 character 看起来是 `char`，但实际上还是 digits/integers。'0', '1', ..., '9' 对应的 ASCII 值为 48、49、50……

- '0' => 48
- '1' => 49
- '2' => 50
- '3' => 51
- '4' => 52
- '5' => 53
- '6' => 54
- '7' => 55
- '8' => 56
- '9' => 57

> Be definition, `char`s are just small integers.

那么要得到 '0' => 0, '1' => 1，让 c 减去 '0' 就可以了：`c - '0'`。比如：`'7' - '0' = 55 - 48 = 7`。

---

```c
if (condition1)
    statement1
else if (condition2)
    statement2
...
    ...
else
    statement n
```

If none of the conditions is satisfied, the _statement_ after the final `else` is executed if it is present.

There can be any number of

```c
else if (condition)
    statement
```

groups between the initial `if` and the final `else`.

The `switch` statement is suitable when the condition is whether some integer or character expression matches one of a set of constants.

# 1.7 Functions

We will generally use *parameter* for a variable named in the parenthesized list in a function definition, and *argument* for the value used in a call of the function.

*Function prototype*

Parameter names are optional in a function prototype.

So instead

```c
int power(int m, int n);
```

You could just write

```c
int power(int, int);
```

# 1.8 Argument — Call by value

In C, all function arguments are passed "by value".

When necessary, it is possible to arrange for a function to modify a variable in a calling routine. The caller must provide the *address* of the variable to be set (technically a *pointer* to the variable), and the called function must **declare** the parameter to be a pointer and access the variable indirectly through it.

# 1.9 Character arrays

*null character*: Its size is zero. It's used to mark the end of **string** of characters. 

When a *string constant* like

```c
"hello\n"
```

appears in a C program, it is stored as an array of characters containing the characters of the string and terminated with a `'\0'` to mark the end.

# 1.10 External variables and scope

Each local variable in a function comes into existence only when the function is called, and disappears when the function is existed. This is so-called *automatic* variables.

As an alternative to automatic variables, it is possible to define variables that are *external* to all functions, that is, variables that can be accessed by name by any function.

An external variable must be *defined*, exactly once, outside of any function; this sets aside storage for it.

The variable must also be *declared* in each function that wants to access it; this states the type of the variable.

---

But for compatibility with *older C programs* the standard takes an empty list as an old-style declaration, and turns off all argument list checking; the word void must be used for an explicitly empty list.

```c
int getline(void);
```

在不接受任何参数的函数声明的括号中添加关键词 `void` 是为了 compatibility 的考量。

"Definition" refers to the place where the variable is created or assigned storage; "Declaration" refers to places where the nature of the variable is stated but no storage is allocated.

# Exercises

## Exercise 1-9

### Question

Write a program to copy its input to its output, replacing each string of one or more blanks by a single blank.

### Answer

My version:

```c
#include <stdio.h>

#define MAXLINE 1000

// 通过跟踪空格 whitespace 的数量来决定是否要 output 当前遇到的空格
int main()
{
    int c;
    int whitespace; // 声明一个变量 whitespace
    int loopc;

    whitespace = 0; // 初始化成 0
    loopc = 0;

    while ((c = getchar()) != EOF)
    {
        if (c == ' ') // 如果 c 是空格，那么在 output 这个空格之前，
        {
            if (whitespace == 0 && loopc > 0) // 我需要 check 目前已经输出的空格的数量，如果还没有输出过空格字符，
            {
                putchar(c); // 那么输出当前遇到的这个空格字符。同时，我还需要检查目前 loop 的次数，如果目前迭代次数为 0，那么当前的这个 loop 是第一次迭代。第一次迭代就遇到空格字符的话，
            }
            else // 那么直接跳过，什么都不做。因为这是在输入的字符流最开头的空格字符。我们需要把它们 trim 掉。
            {
                continue;
            }

            whitespace += 1; // 输出完了需要在 whitespace 上加 1，来记录已经输出的空格的数量。
            loopc += 1;
        }
        else if (c != ' ' && whitespace > 0) // 如果 c 不是空格，同时 whitespace 又大于 1 的话（意味着不久之前已经输出过一个空格字符），
        {
            whitespace = 0; // 那么把 whitespace 设为 0。这里主要的思路是：如果当前的字符 c 不是空格字符，同时 whitespace 大于 1，意味着这时来到了一系列空格字符的末尾，和下一个 word 的开头：_ _ w。
                            // 那么此时需要把 whitespace 归零，准备下一个连续空格字符的检测。
            putchar(c);     // 同时输出当前这个字符（因为它不是空格字符，那直接输出就行了）。
            loopc += 1;
        }
        else
        {
            putchar(c); // 如果以上条件都不满足：c 既不是空格，whitespace 也是 0 的状态。那么意味着此时在 word 当中：正在一个一个的处理 word 中的字符：w o r d。那么单纯输出这些字符就行了。
            loopc += 1;
        }
    }
}
```


## Exercise 1-10

### Question

Write a program to copy its input to its output, replacing each tab by `\t`, each backspace by `\b`, and each backslash by `\\`. This makes tabs and backspaces visible in an unambiguous way.

### Answer

```c
#include <stdio.h>

int main()
{
    int c;

    while ((c = getchar()) != EOF)
    {
        if (c == '\t')
        {
            putchar('\\');
            putchar('t');
        }
        else if (c == '\b')
        {
            putchar('\\');
            putchar('b');
        }
        else if (c == '\\')
        {
            putchar('\\');
            putchar('\\');
        }
        else
        {
            putchar(c);
        }
    }

    return 0;
}
```


## Exercise 1-13

### Question

Write a program to print a histogram of the lengths of words in its input. It is easy to draw the histogram with the bars horizontal; a vertical orientation is more challenging.

## Exercise 1-14

### Question

Write a program to print a histogram of the frequencies of different characters in its input.

## Exercise 1-15

### Question

Rewrite the temperature conversion program of Section 1.2 to use a function for conversion.

### Answer

```c
#include <stdio.h>

float ftoc(int f);

/*
 * print Fahrenheit-Celsius table for fahr = 0, 20, ..., 300;
 * floating-point version + function version.
 */
int main()
{
    float fahr, celsius;
    int lower, upper, step;

    lower = 0;   // lower limit of temperature table
    upper = 300; // upper limit
    step = 20;   // step size

    fahr = lower;
    printf("F\tC\n");
    while (fahr <= upper)
    {
        printf("%3.0f %6.1f\n", fahr, ftoc(fahr));
        fahr = fahr + step;
    }
}

float ftoc(int f)
{
    float celsius = (5.0 / 9.0) * (f - 32.0);
    return celsius;
}
```

## Exercise 1-16

### Question

Revise the main routine of the longest-line program so it will correctly print the length of arbitrarily long input lines, and as much as possible of the text.

## Exercise 1-17

### Question

Write a program to print all input lines that are longer than 80 characters.

```c
#include <stdio.h>
#define MAXLINE 1000  // maximum input line size
#define MIN_LENGTH 80 // minimum line length to be printed

int getline_m(char line[], int maxline); // avoid conflict with stdlib

int main()
{
    int len;            // current line length
    char line[MAXLINE]; // current input line

    while ((len = getline_m(line, MAXLINE)) > 0)
    {
        if (len > MIN_LENGTH)
        {
            printf("-------------\n");
            printf("\tPrinting line (length %d): %s", len, line);
        }
        else
        {
            printf("-------------\n");
            printf("\tLine not printed (length %d): %s", len, line);
        }
    }

    return 0;
}

// getline: read a line into s, return length
int getline_m(char s[], int lim)
{
    int c, i;

    for (i = 0; i < lim - 1 && (c = getchar()) != EOF && c != '\n'; ++i)
    {
        s[i] = c;
    }

    if (c == '\n')
    {
        s[i] = c;
        ++i;
    }

    s[i] = '\0';

    return i;
}
```
