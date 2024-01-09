<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**

- [3.4 Switch](#34-switch)
- [3.5 Loops—`while` and `for`](#35-loopswhile-and-for)
- [3.8 Goto and labels](#38-goto-and-labels)
- [Exercises](#exercises)
    - [Exercise 3-3](#exercise-3-3)
        - [Question](#question)
        - [Answer](#answer)
    - [Exercise 3-4](#exercise-3-4)
        - [Question](#question-1)
    - [Exercise 3-5](#exercise-3-5)
        - [Question](#question-2)
    - [Exercise 3-6](#exercise-3-6)
        - [Question](#question-3)

<!-- markdown-toc end -->

# 3.4 Switch

The switch statement is a multi-way decision that tests whether an expression matches one of a number of *constant* integer values, and branches accordingly.

```c
switch (expression ) {
    case const-expr: statements
    case const-expr: statements
    default: statements
}
```

Each case is labeled by one or more *integer-valued constants* or *constant expressions*.

---

```c
switch (c)
{
    case '0':
    case '1':
    case '2':
    case '3':
    case '4':
    case '5':
    case '6':
    case '7':
    case '8':
    case '9':
        ndigit[c - '0']++;
        break;
    case ' ':
    case '\n':
    case '\t':
        nwhite++;
        break;
    default:
        nother++;
        break;
}
```

The break statement causes an immediate exit from the switch. Because cases serve just as labels, after the code for one case is done, execution *falls through* to the next unless you take explicit action to escape (use `break`).

这段话是在说，如果上面的代码中，`case '9'` 下面的代码块没有 `break` 语句的话，那么在执行完了 `ndigit[c - '0']++;` 之后，它还会接着执行  `case '\t'` 下面代码块中的代码 `nwhite++;`。这个行为叫作 **falls through**。

这跟 Rust 当中的 `match` 语句很不一样。

```rust
fn main() {
    let c = '9';

    match c {
        '0'..='9' => {
            ndigit[c as usize - '0' as usize] += 1;
            nwhite += 1;
        }
        ' ' | '\n' | '\t' => {
            nwhite += 1;
        }
        _ => {
            nother += 1;
        }
    }

    // Rest of the code...
}
```

在 Rust 的 `match` 表达式中，每个模式都可以包含一系列的代码块，类似于 `switch` 语句中的 `case`。然而，不同之处在于 Rust 的 `match` 表达式是严格的，不会发生 *fall through* 行为。一旦一个分支的模式匹配成功并执行了其代码块，`match` 表达式就会结束，不会继续匹配下一个分支。

---

Falling through cases is a mixed blessing. On the positive side, **it allows several cases to be attached to a single action**, as with the digits in this example.

But it also implies that normally each case must end with a break to prevent falling through to the next. Falling through from one case to another is not robust, being prone to disintegration when the program is modified. With the exception of multiple labels for a single computation, fall-throughs should be used sparingly, and commented.

这是在说，比如在上面代码中

```c
    case '0':
    case '1':
    case '2':
    case '3':
    case '4':
    case '5':
    case '6':
    case '7':
    case '8':
    case '9':
        ndigit[c - '0']++;
        break;
```

不管是 match 到了 `'0'`~`'9'` 中的哪个字符，都会执行

```c
        ndigit[c - '0']++;
```

而不需要把这行写到每一个 case 下面。因为 fall through 的存在，在 `case '0'` ~ `case '8'` 都没有写 break 的情况下，它们都会被过一遍，被 fall through 一遍。

# 3.5 Loops—`while` and `for`

The `for` statement

```c
for (expr1; expr2; expr3)
    statement
```

is equivalent to

```c
expr1;
while (expr2) {
    statement
    expr3;
}
```

except for the behavior of `continue`, which is described in Section 3.7.

---

Grammatically, the three components of a for loop are expressions. Most commonly,`expr1` and `expr3` are assignments or function calls and `expr2` is a relational expression (大于，小于或等于等等). **Any of the three parts can be omitted, although the semicolons must remain**. If `expr1` or `expr3` is omitted, it is simply dropped from the expansion. If the test, `expr2` , is not present, it is taken as permanently true, so

```c
for (;;) {
    ...
}
```

is an "infinite" loop, presumably to be broken by other means, such as a `break` or `return`.

在 for loop 中，分号前面的三个部分都是一个 expressions。而且最神奇的是：每一个部分，每一个 expression，你都可以省略，直接不写。但是，expression 可以不写，分号 `;` 不能不写。所以就有了上面的式子。

---

The index and limit of a C `for` loop can be altered from within the loop, and the index variable `i` retains its value when the loop terminates for any reason.

---

`atoi_v2.c`:

```c
#include <stdio.h>
#include <ctype.h>

// atoi: convert s to integer; version 2
int atoi(char s[])
{
    int i, n, sign;

    for (i = 0; isspace(s[i]); i++) // skip whitespace
        ;

    sign = (s[i] == '-') ? -1 : 1;

    if (s[i] == '+' || s[i] == '-') // skip sign
        i++;

    for (n = 0; isdigit(s[i]); i++)
        n = 10 * n + (s[i] - '0');

    return sign * n;
}

int main()
{
    int out = atoi("-3456");
    printf("%d", out);
}
```

`(s[i] - '0')` 用来做 string of integer => integer 的转换。`n` 是上一次计算的结果。`10 * n` 用来进位：比方说 input 是 `"34"`，程序首先处理 `'3'` 这个字符：`10 * 0 + ('3' - '0') = 3`，然后 3 赋值给 `n`，进入下一个循环。第二个循环变成：`10 * 3 + ('4' - '0')`。由于 `10 * 3` 这个式子的存在，原本的 `n` 的值 3，在乘以 10 的情况下，得到了进位，给第二个数字让出了位置。

---

Comma operator: `,`:

A pair of expressions separated by a comma is **evaluated left to right**, and the type and value of the result are the type and value of the right operand. Thus in a for statement, it is possible to place multiple expressions in the various parts, for example to process two indices in parallel.

在 for loop 的 expr1 中，你可以塞好几个 expression 进去。像这样：

```c
for (i = 0, j = strlen(s) - 1; ...) {} // 注意在分号前面的那个逗号，就是我们在这里说的 comma operator `,`
```

然后，被 comma operator 隔开的表达式是严格遵循从左到右取值的。

# 3.8 Goto and labels

C provides the infinitely-abusable `goto` statement, and labels to branch to.
Formally, the `goto` is never necessary, and in practice it is almost always easy
to write code without it.

The most common is to abandon processing in some deeply nested structure, such as breaking out of two or more loops at once. The `break` statement cannot be used directly since it only exits from the inner most loop.

最通常的会使用到 `goto` 的地方是：当你需要一次性完全 break out 一个嵌套了好几层的 for loop。因为普通的 `break` 只能 break out 最里层*那一层* for loop，不能一下子 break out 来到最外层的 for loop，从而继续接下来的代码。

```c
    for (...)
        for (...) {
            ...
            if (disaster)
                goto error;
         }
     ...
    error:
        clean up the mess
```

---

As another example, consider the problem of determining whether two arrays `a` and `b` have an element in common. One possibility is

```c
    for (i = 0; i < n; i++)
        for (j = 0; j < m; j++)
            if (a[i] == b[j])
                goto found;
    // didn't find any common element
    ...
found:
    // got one: a[i] == b[j]
    ...
```

Code involving a goto can always be written without one, though perhaps at the price of some repeated tests or an extra variable. For example, the array search becomes

```c
found = 0;
for(i =0; i < n && !found; i++)
    for (j = 0; j < m && !found; j++)
        if (a[i] == b[j])
            found = 1;
if (found)
    // got one: a[i - 1] == b[j - i]
    ...
else
    // didn't find any common element
    ...
```

With a few exceptions like those cited here, code that relies on `goto` statements is generally harder to understand and to maintain than code without `gotos`. Although we are not dogmatic about the matter, it does seem that `goto` statements should be used rarely, if at all.

# Exercises

## Exercise 3-3

### Question

Write a function `expand(s1, s2)` that expands shorthand notations like `a-z` in the string `s1` into the equivalent complete list `abc...xyz` in `s2`. Allow for letters of either case and digits, and be prepared to handle cases like `a-b-c` and `a-z0-9` and `-a-z`. Arrange that a leading or trailing `-` is taken literally (如果 `-` 出现在开头或者结尾，不要把它们当作是 range 的 indicator，仅把它们作为字面意思的 `-` 符号处理).

### Answer

```c
void expand(char s1[], char s2[])
{
    int i = 0, j = 0;
    if (s1[0] == '-')
    {
        s2[j++] = '-';
        i++;
    }

    while (s1[i] != '\0')
    {
        if (s1[i] == '-' && s1[i + 1] != '-' && s1[i + 1] != '\0')
        {
            char start_char = s1[i - 1] + 1; // The first character to be expanded
            char end_char = s1[i + 1];       // The last char in the endpoint of the range (s1)

            while (start_char <= end_char)
            {
                s2[j++] = start_char++;
            }

            i += 2;
        }
        else
        {
            s2[j++] = s1[i++];
        }
    }

    s2[j] = '\0';
}
```

## Exercise 3-4

### Question

In a two's complement number representation, our version of `itoa` doesn't handle the largest negative number, that is, the value of `n` equal to $-2^{\text{{wordsize}} - 1}$. Explain why not. Modify it to print that value correctly, regardless of the machine on which it runs.

"Out version of `itoa`":

```c
// itoa: convert n to characters in s
void itoa(int n, char s[])
{
    int i, sign;

    if ((sign = n) < 0)
        n = -n;

    i = 0;

    do
    {
        s[i++] = n % 10 + '0';
    } while ((n /= 10) > 0);

    if (sign < 0)
        s[i++] = '-';

    s[i] = '\0';
    reverse(s);
}
```

## Exercise 3-5

### Question

Write the function `itob(n, s, b)` that converts the integer `n` into a base `b` character representation in the string `s`. In particular, `itob(n, s, 16)` formats `n` as a hexadecimal integer in `s`.

## Exercise 3-6

### Question

Write a version of `itoa` that accepts three arguments instead of two. The third argument is a minimum field width; the converted number must be padded with blanks on the left if necessary to make it wide enough.
