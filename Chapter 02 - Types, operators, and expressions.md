<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**

- [2.1 Variable names](#21-variable-names)
- [2.2 Data types and sizes](#22-data-types-and-sizes)
- [2.3 Constants](#23-constants)
- [2.4 Declarations](#24-declarations)
- [2.7 Type conversions](#27-type-conversions)
- [2.8 Increment and decrement operators](#28-increment-and-decrement-operators)
- [2.12 Precedence and order of evaluation](#212-precedence-and-order-of-evaluation)
- [Exercises](#exercises)
    - [Exercise 2-3](#exercise-2-3)
        - [Question](#question)
    - [Exercise 2-4](#exercise-2-4)
        - [Question](#question-1)
        - [Answer](#answer)
    - [Exercise 2-5](#exercise-2-5)
        - [Question](#question-2)
- [Footnotes](#footnotes)
    - [^2](#2)
    - [^3](#3)
    - [^4](#4)

<!-- markdown-toc end -->

Variables and constants are the basic data objects manipulated in a program. Declarations list the variables to be used, and state what type they have and perhaps what their initial values are. Operators specify what is to be done to them. Expressions combine variables and constants to produce new values. The type of an object determines the set of values it can have and what operations can be performed on it.

## 2.1 Variable names

Don't begin variable names with underscore, however, since library routines often use such names.

比较特别的是，你不能在变量名的开头使用 underscore (low line) `_`。

## 2.2 Data types and sizes

The are only a few basic data types in C:

| Data type | Description                                                                        |
|:----------|:-----------------------------------------------------------------------------------|
| `char`    | a single byte, capable of holding one character in the local character set.        |
| `int`     | an integer, typically reflecting the natural size of integers on the host machine. |
| `float`   | single-precision floating point.                                                   |
| `double`  | double-precision **floating point**.                                               |

---

There are a number of *qualifiers* that can be applied to these basic types:

- Qualifiers `short`, `long`, `signed` and `unsigned` for `int`:

| Data type | Qualifier        | Example             | Notes                                               |
|:----------|:-----------------|---------------------|-----------------------------------------------------|
| `int`     |                  |                     |                                                     |
|           | `short int`      | `short int sh;`     | The word `int` can be omitted in such declarations. |
|           | `long int`       | `long int counter;` | The word `int` can be omitted in such declarations. |
|           |                  |                     |                                                     |
|           | `signed int`     |                     |                                                     |
|           | `unsigned int`   |                     |                                                     |
|           |                  |                     |                                                     |
|           | `signed short`   |                     |                                                     |
|           | `unsigned short` |                     |                                                     |
|           |                  |                     |                                                     |
|           | `signed long`    |                     |                                                     |
|           | `unsigned long`  |                     |                                                     |

- Qualifier `signed` and `unsigned` for `char`:

| Data type | Qualifier       |
|:----------|:----------------|
| `char`    |                 |
|           | `signed char`   |
|           | `unsigned char` |

Whether plain `char` are signed or unsigned is machine-dependent, but printable characters are *always* positive.

- The type `long double` specifies extended-precision floating point.

`float`, `double`, `long double` could represent one, two or three distinct sizes.

---

`short` is often **16 bits**.
`long` is often **32 bits**.
`int`: Natural size for a particular machine. **Either 16 or 32 bits**.

要求是 `short` *至少*为 16 bits，`int` 至少为 16 bits。`long` 至少为 32 bits。然后 `short` <=（小于等于） `int` <= `long`。

# 2.3 Constants

A *character constant* is an integer, written as one character within single quotes, such as `'x'`.

The value of a character constant is the numeric value of the character in the machine's character set. For example, in the ASCII character set the character constant `'0'` has the value 48, which is unrelated to the numeric value 0.

---

The character constant `'\0'` represents the character with value zero, the *null character*.

---

A *constant expression* is an expression that involves only constants. Such expressions my be evaluated during compilation rather than run-time. For example:

```c
#define MAXLINE 1000
char line[MAXLINE + 1];
```

---

*string constant*, or *string literal*, is a sequence of zero or more characters surrounded by double quotes:

```c
"I am a string"
```

The internal representation of a string has a *null character* `'\0'` at the end, so the physical storage required is one more than the number of characters written between the quotes.

---

**Note**: Distinguish between a character constant and a string that contains a single character:

| Distinguish                             | Showing   |
|:----------------------------------------|-----------|
| character constant                      | `'x'`[^1] |
| string that contains a single character | `"x"`[^2] |

[^1]: It's actually an integer, used to produce the numeric value of the letter `x` in the machine's character set.
[^2]: It's an array of characters that contains one character (the letter `x`) and a `'\0'`.

---

*Enumeration constant*: A list of constant integer values. Such as

```c
enum boolean { NO, YES };
```

Enumerations provide a convenient way to associate constant values with names, an alternative to `#define` with the advantage that the values can be generated for you.

# 2.4 Declarations

```c
char esc = '\\';
int i = 0;
int limit = MAXLINE + 1;
float eps = 1.0e-5;
```

等号右边的 expression 叫作 initializer。

> If the variable in question is not automatic, the initialization is done once only, conceptually before the program starts executing, and the initializer must be a constant expression. An explicitly initialized automatic variable is initialized each time the function or block it is in is entered; the initializer may be any expression. External and static variables are initialized to zero by default. Automatic variables for which there is no explicit initializer have undefined (i.e., garbage) values.

上面这段话总结起来有这么几点：

- For non-automatic variables (external or static), if no explicit initializer is provided, they are automatically initialized to zero.
    - Example: `static int count;` initializes `count` to zero.
- Automatic variables without explicit initializers have undefined (garbage) values.
    - Example: `int total;` leaves `total` with an undefined (garbage) value.
- Automatic variables with explicit initializers are initialized each time the function or block is entered, and the initializer can be any expression.
    - Example: `int sum = 0;` initializes `sum` to zero every time the block or function is entered.
- Initialization for non-automatic variables happens once before the program starts, and the initializer must be a constant expression.
    - Example: `const int MAX_SIZE = 100;` initializes `MAX_SIZE` to 100 before the program starts executing.

The qualifier `const` can be applied to the declaration of **any variable** to specify that its value will not be changed. `const` 可以被用在任意变量前面来声明这个变量的值不会改变。例如：

```c
const double e = 2.71828182845905;
const char msg[] = "warning: ";
```

```c
int strlen(const char[]);
```

# 2.7 Type conversions

When an operator has operands of different types, they are converted to common type according to a small number of rules.

In general, the only automatic conversion are those that convert a "narrower" operand into a "wider" one without losing information, such as converting an integer to floating point in an expression like `f + i`. `float + integer` 将把 integer 装化成 float 再做加法。

A `char` is just a small integer, so `char`s may be freely used in arithmetic expressions.

---

In general, if an operator like `+` or `*` takes two operands (a binary operator) has operands of different types, the "lower" type is *promoted* to the "higher" type before the operation proceeds[^3]. The result is of the higher type.

[^3]: 低精度的类型将会被装换到高精度的类型。例如，如果 `+` 两边的任意一个 operand 是 `float`，那即使另外一边的 operand 不是 `float` 也会被转换到 `float`。

The informal set of rules:

1. If either operand is `long double`, convert the other to `long double`.
2. Otherwise, if either operand is `double`, convert the other to `double`.
3. Otherwise, if either operand is `float`, convert the other to `float`.
4. Otherwise, convert `char` and `short` to int.
5. Then if either operand is `long`, convert the other to `long`.

---

Conversions take place across assignments; the value of the right side is **converted to the type of the left**, which is the type of the result.

---

Explicit type conversions can be forced ("coerce") in any expression, with a unary operator called a *cast*. In the construction

```c
(type-name) expression
```

the expression is converted to the named type by the conversion rules above. The precise meaning of a cast is as if the *expression* were assigned to a variable of the specified type, which is then used in place of the whole construction.

```c
sqrt((double) n)
```

to convert the value of `n` to `double` before passing it to `sqrt`. Note that the cast produces the *value* of `n` in the proper type; `n` itself is not altered.

---

If arguments are declared by a function prototype, as they normally should be, the declaration causes automatic coercion of any arguments when the function is called. Thus, given a function prototype for `sqrt`:

```c
double sqrt(double);
```

the call

```c
root2 = sqrt(2);
```

coerces the integer `2` into the `double` value `2.0` without any need for a cast.

# 2.8 Increment and decrement operators

The unusual aspect is that `++` and `--` may be used either as prefix opeartors (before the variable, as in `++n`), or postfix (after the variable `n++`). In both cases, the effect is to increment `n`. But the expression `++n` increments `n` *before* its value is used, while `n++` increments `n` *after* its value has been used. This means that in a context where the value is being used, not just the effect[^4], `++n` and `n++` are different.

[^4]: 这里的 effect 指的是 ”side effect“。`n` 的 increment 被认为是一个 side effect。说的是，在下面的例子中，`x = n++`，不仅仅 `n` 得到了 increment，increment 之后的值还被使用了——赋给了 `x`。

`++n` 是先增加再用。`n++` 是先用再增加。例如：

```c
x = n++;
```

sets `x` to 5, but

```c
x = ++n;
```

sets `x` to 6.

---

The increment and decrement operators can only be applied to **variables**.

In a context where no value is wanted, just the incrementing effect, as in

```c
if (c == '\n')
    nl++;
```

prefix and postfix are the same.

这段的意思是，如果 increment 或 decrement 的值不被使用的话，那么 `++` 写在变量前面还是后面都没差。

---

示例 1：

```c
// squeeze: delete all c from s
void squeeze(char s[], int c)
{
    int i, j;

    for (i = j = 0; s[i] != '\0'; i++)
    {
        if (s[i] != c)
        {
            s[j++] = s[i];
        }
    }
    s[j] = '\0';
}
```

这一段

```c
        if (s[i] != c)
        {
            s[j++] = s[i];
        }
```

其实可以写成

```c
        if (s[i] != c)
        {
            s[j] = s[i];
            j++;
        }
```

意思是一样的，都是先用再增加。由于这两种写法是一样的，那么从第二种写法中可以得知第一种写法 `s[j++] = s[i];` 实际的作用顺序是：先赋值（“先用”），再增加。

---

示例 2:

```c
// strcat: concatenate t to the end of s; s must be big enough
void strcat(char s[], char t[])
{
    int i, j;

    i = j = 0;
    while (s[i] != '\0') // find end of s
        i++;
    while ((s[i++] = t[j++]) != '\0') // copy t
        ;
}
```

要把 `t` 附加到 `s` 上的话，首先最核心的动作是把 `t` 这个 character array 里的元素一个个的赋值给 `s` 对应的位置。那要实现“附加”，首先我们通过第一个 while loop 找到 `s` 的末尾：while loop 不断迭代，直到遇到 null character。然后得到这个 index number `i`。在第二个 while loop 刚开始的时候，`t` 的 indexer `j` 的值为 0，但 `s` 的 indexer `i` 经过第一个 while loop 已经不是 0 了，是指向的 `s` 的末尾。

# 2.12 Precedence and order of evaluation

C, like most languages, **does not specify the order in which the operands of an operator are evaluated.** (The exceptions are `&&`, `||`, `?:`, and `,`.) For example, in a statement like

```c
x = f() + g()
```

`f` may be evaluated before `q` or vice versa. `f()` 和 `g()` 哪个先被 eval，不一定。所以不能依赖这个顺序。

---

Similarly, the order in which function arguments are evaluated is not specified, so the statement

```c
printf("%d %d\n", ++n, power(2, n)); // WRONG
```

can produce different results with different compilers, depending on whether is incremented before `power` is called. The solution, of course, is to write

```c
++n;
printf("%d %d\n", n, power(2, n));
```

---

The moral is that writing code that depends on order of evaluation is a bad programming practice in any language.

# Exercises

## Exercise 2-3

### Question

Write the function `htoi(s)`, which converts a string of hexadecimal digits (including an optional `Ox` or `0X`) into its equivalent integer value. The allowable digits are 0 through 9, `a` through `f`, and `A` through `F`.

## Exercise 2-4

### Question

Write an alternate version of `squeeze(s1, s2)` that deletes each character in `s1` that matches any character in the string `s2`.

### Answer

首先让我们分析一下原 `squeeze()` 函数是怎么运行的：
 
```c
#include <stdio.h>

void squeeze(char s[], int c);

int main()
{
    char word[] = "apple";
    squeeze(word, 'p');

    printf("%s", word);
}

// squeeze: delete all c from s
void squeeze(char s[], int c)
{
    int i, j;

    for (i = j = 0; s[i] != '\0'; i++)
    {
        if (s[i] != c)
        {
            s[j++] = s[i];
        }
    }
    s[j] = '\0';
}
```

如果 base word 为 “apple”，所有的字符 `'p'` 要被从 “apple” 中删去。效果有点像是抓着字符 `a` 和 `"le"` 往中间挤压，把 `p` 挤压没了。这就是函数叫“挤压”的由来吧。OK，看起来是这个效果，实际实现起来是这样的逻辑：

最基础的一个动作就是：用 `s` 中的每个字符赋值回 `s`。像这样：

```c
s[0] = s[0];
s[1] = s[1];
s[2] = s[2];
s[3] = s[3];
s[4] = s[4];
```

在 for loop 中，每一次迭代，`i` 都会增加 1。但 `j` 不一定增加（需要满足 `s[i] != c`）。可能 `i` 增加到了 3，而 `j` 还在 1。这就造成了在赋值的动作当中，把处在 `s[3]` 位置的字符赋值到了 `s[1]`。在之后 loop 当中的赋值也都会是错位的。但这种错位正是我们想要的。用 `s[3]` 位置的字符 `'l'` 把 `s[1]` 位置的字符 `'p'` 覆盖掉，这样 `'p'` 就成功从 `s` 当中移除了。

```c
s[0] = s[0];
s[1] = s[3];
s[2] = s[4];
```

最后的输出结果就是 `ale`。`apple` => `ale`。

---

`squeeze_v2`:

```c
#include <stdio.h>

// Function to delete characters in s1 that match any character in s2
void squeeze(char s1[], char s2[])
{
    int i, j, k;
    int match; // Flag to indicate if a character in s1 matches any character in s2

    // Iterate through each character in s1
    for (i = j = 0; s1[i] != '\0'; i++)
    {
        match = 0; // Reset match flag for each character in s1

        // Iterate through each character in s2 to check for a match
        for (k = 0; s2[k] != '\0'; k++)
        {
            // If a match is found, set the match flag and break from the loop
            if (s1[i] == s2[k])
            {
                match = 1;
                break;
            }
        }

        // If no match is found, retain the character in s1 and update the write index
        if (!match)
        {
            s1[j++] = s1[i];
        }
    }

    // Add a null terminator to the modified string
    s1[j] = '\0';
}

int main()
{
    char str1[] = "hello";
    char str2[] = "lo";

    squeeze(str1, str2);

    printf("Result: %s\n", str1);

    return 0;
}
```

## Exercise 2-5

### Question

Write the function `any(s1 , s2)`, which returns the first location in the string `s1` where any character from the string `s2` occurs, or `-1` if `s1` contains no characters from `s2`. (The standard library function `strpbrk` does the same job but returns a pointer to the location.)
