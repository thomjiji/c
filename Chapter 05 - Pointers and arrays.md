<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**

- [5.3 Pointer and arrays](#53-pointer-and-arrays)
- [5.4 Address arithmetic](#54-address-arithmetic)
- [5.5 Character pointers and functions](#55-character-pointers-and-functions)
- [5.6 Pointer array; Pointers to pointers](#56-pointer-array-pointers-to-pointers)
- [5.7 Multi-dimensional arrays](#57-multi-dimensional-arrays)
- [Exercises](#exercises)
    - [Exercise 5-3](#exercise-5-3)
        - [Question](#question)
        - [Answer](#answer)
    - [Exercise 5-4](#exercise-5-4)
        - [Question](#question-1)
    - [Exercise 5-5](#exercise-5-5)
        - [Question](#question-2)
    - [Exercise 5-6](#exercise-5-6)
        - [Question](#question-3)
    - [Exercise 5-7](#exercise-5-7)
        - [Question](#question-4)
    - [Exercise 5-8](#exercise-5-8)
        - [Question](#question-5)
        - [Answer](#answer-1)
    - [Exercise 5-9](#exercise-5-9)
        - [Question](#question-6)
        - [Answer](#answer-2)
- [Footnotes](#footnotes)

<!-- markdown-toc end -->

# 5.3 Pointer and arrays

> Be definition, the value of a variable or expression of type *array* is the address of element zero of the array.

从定义上来说，一个 array 的值就是 array 的第一个元素的地址。举例来说，如果 `a` 是一个 array，`pa` 是一个 pointer。

```c
pa = &a[0];
```

现在 `pa` 和 `a` 拥有相同的值！`pa` 是一个指向 `a` array 的第一个元素的 pointer，`a` 也是！`a` 是一个 array 没错，但 `a` 这个名字，这个 identifier 的原意就是一个指向第一个元素的 pointer。它们完全是一个东西。

因此上面的 assignment 可以进一步写成：

```c
pa = a;
```

更神奇的来了：`a[i]` 可以写成 `*(a + i)`。看起来你在做 array 的 indexing，实际上你做的是 pointer 的运算！

If `pa` is a pointer, expressions may use it with a subscript; `pa[i]` is identical to `*(pa + i)`. That is, **an array-and-index expression is equivalent to one written as a pointer and offset**.

**When an array name is passed to a function, what is passed is the location of the initial element**. Within the called function, this argument is a local variable, and so an array name parameter is a pointer, that is, a variable containing an address.

当我们 pass 一个 array 给一个函数，实际 pass 的是一个 variable，这个 variable 包含 array 第一个元素的地址。然后实际上这个 variable 就是一个 pointer，这个 pointer 就是一个 variable。

---

As formal parameters in a function definition,

```c
chat s[];
```

and 

```c
char *s
```

are equivalent; we prefer the latter because it says more explicitly that the parameter is a pointer. **When an array name is passed to a function, the function can at its convenience believe that it has been handed either an array or a pointer, and manipulate it accordingly.**

# 5.4 Address arithmetic

Pointer can be compared under certain circumstances. If `ptr1` and `ptr2` point to members of the same array, then relations like `==`, `I=`, `<`, `>=`, etc., work properly. For example:

```c
ptr1 < ptr2 
```

Any pointer can be meaningfully compared for equality or inequality with zero. But the behavior is **undefined** for arithmetic or comparison with pointers that *do not* point to members of the same array.

The construction `ptr + n` means the address of the n-th object beyond the one `p` currently points to. This is true regardless of the kind of object `p` points to.

Pointer substraction is also valid: if `p` and `q` point to elements of the same array, and `p < q`, then `q - p + 1` is the number of elements from `p` to `q` inclusive.

Pointer arithmetic is consistent: if we had been dealing with `float`s, which occupy more storage than `char`s, and if `p` were a pointer to `float`, `p++` would advance to the next `float`. **All the pointer manipulations automatically take into account the size of the object pointed to.**

# 5.5 Character pointers and functions

Perhaps the most common occurrence of string constants is as arguments to functions, as in:

```c
printf("hello, world");
```

When a character string like this appears in a program, access it is through a *character pointer*; `printf` receives a pointer to the beginning of the character array. That is, a string constant is accessed by a pointer to its first element.

# 5.6 Pointer array; Pointers to pointers

In Chapter 3 we presented a Shell sort function that would sort an array of integers, and in Chapter 4 we improved on it with a quicksort. The same algorithms will work, except that now we have to deal with lines of text, which are of different lengths, and which, unlike integers, can't be compared or moved in a single operation. We need a data representation that will cope efficiently and conveniently with variable-length text lines.

This is where the array of pointers enters. If the lines to be sorted are stored end-to-end in one long character array, then each line can be accessed by a pointer to its first character. The pointer themselves can be stored in an array. Two lines can be compared by passing their pointers to `strcmp`. When two out-of-order lines have to be exchanged, the pointers in the pointer array are exchanged, not the text lines themselves.

The sorting process has threes steps:

> 1. read all the lines of input
> 2. sort them
> 3. print them in order

# 5.7 Multi-dimensional arrays

C provides rectangular multi-dimensional arrays, although in practice they are much less used than arrays of pointers.

---

`date_converter.c` > `day_of_year()`:

```c
/* day_of_year: set day of year from month & day */
int day_of_year(int year, int month, int day)
{
    int i, leap;

    leap = year % 4 == 0 && year % 100 != 0 || year % 400 == 0;
    for (i = 1; i < month; i++)
        day += daytab[leap][i];

    return day;
}
```

变量 `leap` 用来记录该年份是否为闰年。是否为闰年的判断方法为：

1. If a year is divisible by 4 but not by 100, it is a leap year.
2. If a year is divisible by 400, it is a leap year.

`leap` 判断完毕，我们就知道应该 focus 在 `daytab` 的哪一行。然后在 for loop 的帮助下，把该行的直到输入参数 `month` 之前的所有月份天数相加，加到输入参数 `day` 当中，得到总天数 `day`，然后返回。这是这个函数的逻辑。

---

`date_converter.c` > `month_day()`:

```c
/* month_day: set month, day from day of year */
void month_day(int year, int yearday, int *pmonth, int *pday)
{
    int i, leap;

    leap = year % 4 == 0 && year % 100 != 0 || year % 400 == 0;
    for (i = 1; yearday > daytab[leap][i]; i++)
        yearday -= daytab[leap][i];

    *pmonth = i;
    *pday = yearday;
}
```

同样先判断是否为闰年，然后进入 for loop。因为第二个元素才是真正的月份天数，所以我们初始化 `i` 到 1，从 `daytab` 的第二个元素开始。设定终止条件 `yearday > daytab[leap][i]`。在 for loop 的 body 中，每循环一次，就从 `yearday` 中减去该月的天数。减到何时为止呢？看终止条件：从 `yearday` 中减去该月天数，直到该月天数大于所剩的 `yearday` 的时候。每进行一次循环，`daytab[leap][i]` 就进到下一个月份的天数，然后从 `yearday` 中减去该月天数。

当 for loop 的终止条件满足，程序进行到下一行。我们把 `i` 的赋值给指针 `pmonth`，作为实际的月份；把所剩的 `yearday` 赋值给指针 `pday`，作为实际月份的天数。从而实现了从 `yearday` 到 `monthday` 的转换。

---

In C, a two-dimensional array is really a one-dimensional array, each of whose elements is an array.

If a two-dimensional array is to be passed to a function, the parameter declaration in the function must include the number of columns; the number of rows is irrelevant, since what is passed is, as before, a pointer to an array of rows, where each row is an array of 13 `int`s.

If the array `daytab` is to be passed to a function `f`, the declaration of `f` wound be

```c
f(int daytab[2][13]) { ... }
```

It could also be

```c
f(int daytab[][13]) { ... }
```

Since the number of rows is irrelevant, or it could be

```c
f(int (*daytab)[13]) { ... }
```

which says that the parameter is **a pointer to an array of 13 integers**.

The parentheses are necessary since brackets `[]` have higher precedence than `*`. Without parentheses, the delcaration

```c
int *daytab[13]
```

is **an array of 13 pointers to integers**. More generally, only the first dimension (subscript) of an array is free; all the others have to be specified.

# 5.10 Command-line arguments

`echo.c`:

```c
#include <stdio.h>

// echo command-line arguments; 1st version
int main(int argc, char *argv[])
{
    int i;

    for (i = 1; i < argc; i++)
    {
        printf("%s%s", argv[i], (i < argc - 1) ? " " : "");
    }

    printf("\n");
    return 0;
}
```

When `main` is called, it is called with two arguments. The first (conventianally called `argc`, for argument count) is the number of command-line arguments the program was invoked with; the second (`argv`, for argument vector) is a pointer to an array of character strings that contain the arguments, one per string.

By convention, `argv[0]` is the name by which the program was invoked, so `argc` is at least 1.

`argv` is a pointer to an array of pointers. Each element in the array (`argv[0]`, `argv[1]`, etc.) is a pointer to a string (character array).

`argc` 是输入的 arguments 的数量。`argv` 是一个装着输入的 arguments 内容的数组，也可以理解为装着很多个指针的数组。像在上面的 `echo.c` 程序中，如果像这样运行程序

```sh
$ echo hello world
```

那么 `argv` 会变成像是这样：

```c
char *argv[] = {
    "echo",
    "hello",
    "world"
}
```

`argv` 是一个数组也就意味着它本身其实也是一个指针，指向其第一个元素。而第一个元素也是一个指针，指向第一个 character string（`echo`）的第一个 char（`e`）。

---

The standard requires that `argv[argc]` be a null pointer.

---

`echo_v2.c` (pointer version):

```c
#include <stdio.h>

// echo command-line arguments; 2nd version
int main(int argc, char *argv[])
{
    while (--argc > 0)
    {
        printf("%s%s", *++argv, (argc > 1) ? " " : "");
    }

    printf("\n");
    return 0;
}
```

Since `argv` is a pointer to the beginning of the array of argument strings, incrementing it by 1 (`++argv`) makes it point at the original `argv[1]` instead of `argv[0]`. Each successive increment moves it along to the next argument; `*argv` is then the pointer to that argument. At the same time, `argc` is decremented; when it becomes zero, there are no arguments left to print.

在 while loop 的条件语句 `--argc > 0` 中，先进行的是 `argc` 的 decrement，然后是 decrement 之后的 `argc` 的值与 0 进行比较。上来就做一个 `argc` 的 decrement 的原因是：第一个是程序名，比如在这里的 `echo`，它是不需要被 print 出来的。

while loop 中的 `printf` 这一行比较 tricky。`*++argv` 这里的运行顺序是：先 increment `argv`，效果上是让 `argv` 这个指针指到下一个元素。然后再使用 `*` operator 来 dereference `argv`，得到该处的 string 的值，比如 `Hello`。如果 `argc` 大于 1，意味着还有 argument 没有处理到（没有 print 出来），于是输出空格，为下一个 argument 留出位置。如果 `argc` 等于 1，意味着没有 argument 存在了。那么输出空字符。

除此之外这里还有一个吊诡的点。上一段中有这样一句话：

> `*++argv` 这里使用 `*` operator 是用来 dereference `argv`，得到该处的 string 的值，比如 `Hello`。

但这里得到的值实际上不应该是“Hello”而是“H”对吗？因为如果我们仔细分析一下：`argv` 这个名字本身是一个 pointer，指向数组的第一个元素。而因为该数组当中都是 character string，character string 本身也只是一个数组；而数组本身也只是一个 pointer，所以 `argv` 就变成了一个指向 pointer 的 pointer。那么当我使用 `*argv` 来解引用，我得到的究竟是什么？还是一个 pointer 对吗？这个解引用所得到的值，是另一个 pointer。而这“另一个 pointer”指向 character string 的第一个字符，“H”。

我们可以使用如下的代码来验证这一点：

```c
int main(int argc, char *argv[])
{
    // while (--argc > 0)
    // {
    //     printf("%s%s", *++argv, (argc > 1) ? " " : "");
    // }

    char *firstString = *++argv;
    printf("%p\n", firstString);
    return 0;
}
```

首先第一行，我们把解引用后的值赋给 `firstString` 这个 pointer。然后在下一行把它打印出来，可以得到该 pointer 存储的地址：

```sh
0x16baf723a
```

如果我们对 `firstString` 再做一次解引用的话：

```c
int main(int argc, char *argv[])
{
    // while (--argc > 0)
    // {
    //     printf("%s%s", *++argv, (argc > 1) ? " " : "");
    // }

    char *firstString = *++argv;
    printf("%c\n", *firstString);
    return 0;
}
```

会得到这样的结果：

```sh
H
```

这样就得到 `argv` 这个 pointer 指向的那个 pointer 指向的 character array 的第一个元素的值。

那为什么 `*++argv` 的操作可以得到我们想要的“Hello”的结果而不是“H”呢？原因在于 `printf` 的 format specifier 用的是 `%s` 而不是 `%c`。从这里可以观察到 `%s` 有一种神奇的效果，可以直接拿到指针的值，而不需要指针本身解引用。这是我目前为止没有了解到的一个面向，兴许这个行为很正常。

我们可以再实验一下：把原代码中的这一行

```c
        printf("%s%s", *++argv, (argc > 1) ? " " : "");
```

改成

```c
        printf("%c%s", **++argv, (argc > 1) ? " " : "");
```

这里是对第一次解引用得到的那个指针再做一次解引用。这样按道理我们会得到一个 `char`，presumably 会是“H”。结果正如我们所料：

```c
H w
```

也就是“Hello world”这两个单词的首个字母，首个字符。

---

```c
        printf("%s%s", *++argv, (argc > 1) ? " " : "");
```

可以写成

```c
        printf((argc > 1) ? "%s " : "%s", *++argv);
```

这说明：format argument of `printf` can be an expression too.

# 5.11 Pointers to functions

In C, a function itself is not a variable, but it is possible to define pointers to functions, which can be assigned, placed in arrays, passed to functions, returned by functions, and so on.

# Exercises

## Exercise 5-3

### Question

Write a pointer version of the function `strcat` that we showed in Chapter 2: `strcat(s, t)` copies the string `t` to the end of `s`.

### Answer

```c
char *strcat(char *s, const char *t)
{
    char *dest = s;

    // Move the pointer 'dest' to the end of the string 's'
    while (*dest != '\0')
    {
        dest++;
    }

    // Copy the string 't' to the end of 's'
    while ((*dest++ = *t++) != '\0')
        ;

    return s;
}
```

我们首先创建一个新的 pointer 叫作 `dest`，把 pointer `s` 的值（就是一个地址）assign 给它。这样，pointer `dest` 和 pointer `s` 都指向同一块内存区域。

然后我们通过一个 while loop，把 pointer `dest` advance 到字符串 `s`[^1] 的末尾。经过第一个 while loop，`dest` 指向的是字符串 `s` 的 null terminator。

这时我们来到第二个 while loop。这个 while loop 的首次迭代，将指针 `t` 指向的字符值赋给指针 `dest` 所指向的位置，也即字符串 `s` 的空字符 `'\0'`。这时，`s` 的空字符 `'\0'` 会被替换为 `t` 的第一个元素的值。 这时在我们的目标 str `s`当中也就没有空字符了。不过不用担心，这第二个 while loop 的结尾会把它重新加回来。

[^1]: 我们可以直接把 `s` 称作是那个字符串本身，即使它看上去好像只是一个指向字符串的指针。因为字符串作为一个数组，数组的名字（identifier）就是一个指向该数组第一个元素的指针。

## Exercise 5-4

### Question

Write the function `strend(s, t)`, which returns 1 if the string `t` occurs at the end of the string `s`, and zero otherwise.

```c
int strend(char *s, char *t)
{
    int i, j;

    // Find the end of string s
    for (i = 0; s[i] != '\0'; i++)
        ;

    // Find the end of string t
    for (j = 0; t[j] != '\0'; j++)
        ;

    // Compare characters from the end of s and t
    while (j >= 0 && s[i] == t[j])
    {
        i--;
        j--;
    }

    // If j is -1, all characters in t matched with the end of s
    return (j == -1) ? 1 : 0;
}
```

指针 `s` 和 `t`，先 advance 指针 `s` i 个单位，`s + i`，直到 `s[i]` 等于 `t[0]`。

## Exercise 5-5

### Question

Write versions of library functions `strncpy`, `strncat`, and `strncmp`, which operate on at most the first `n` characters of their argument strings. For example, `strncpy(s, t, n)` copies at most `n` characters of `t` to `s`.

## Exercise 5-6

### Question

Rewrite appropriate programs from earlier chapters and exercises with pointers instead of array indexing. Good possibilities include `getline` (Chapter 1 and 4), `atoi`, `itoa`, and their variants (Chapter 2, 3, and 4), `reverse` (Chapter 3), and `strindex` and `getop` (Chapter 4).

## Exercise 5-7

### Question

Rewrite `readlines` to store lines in an array supplied by `main`, rather than calling `alloc` to maintain storage. How much faster is the program?

## Exercise 5-8

### Question

There is no error checking in `day_of_year` or `month_day`. Remedy this defect.

### Answer

`date_converter_improved.c`:

```c
#include <stdio.h>

static char daytab[2][13] = {
    {0, 31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31},
    {0, 31, 29, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31}};

int is_leap(int year);

int is_valid_date(int year, int month, int day)
{
    // Add more conditions if needed
    int leap = is_leap(year);
    return (year > 0 && month >= 1 && month <= 12 && day >= 1 && day <= daytab[leap][month]);
}

int day_of_year(int year, int month, int day)
{
    if (!is_valid_date(year, month, day))
    {
        // Handle invalid date
        return -1; // or some other suitable value
    }

    int i, leap;

    leap = is_leap(year);

    for (i = 1; i < month; i++)
        day += daytab[leap][i];

    return day;
}

void month_day(int year, int yearday, int *pmonth, int *pday)
{
    if (!is_valid_date(year, 1, 1) || yearday < 1 || yearday > 365 + is_leap(year))
    {
        // Handle invalid yearday
        *pmonth = -1; // or some other suitable value
        *pday = -1;   // or some other suitable value
        return;
    }

    int i, leap;

    leap = is_leap(year);

    for (i = 1; yearday > daytab[leap][i]; i++)
        yearday -= daytab[leap][i];

    *pmonth = i;
    *pday = yearday;
}

int is_leap(int year)
{
    return year % 4 == 0 && (year % 100 != 0 || year % 400 == 0);
}

int main()
{
    int year = 2024; // Replace with the desired year
    int month = 6;   // Replace with the desired month
    int day = 7;     // Replace with the desired day

    int yearday = day_of_year(year, month, day);
    printf("Day of the year: %d\n", yearday);

    int result_month, result_day;
    month_day(year, yearday, &result_month, &result_day);
    printf("Month: %d, Day: %d\n", result_month, result_day);

    return 0;
}
```

## Exercise 5-9

### Question

Rewrite the routines `day_of_year` and `month_day` with pointers instead of indexing.

### Answer

`date_converter_pointer`:

```c
#include <stdio.h>

static char daytab[2][13] = {
    {0, 31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31},
    {0, 31, 29, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31}};

int day_of_year(int year, int month, int day);
void month_day(int year, int yearday, int *pmonth, int *pday);

int main()
{
    int year = 2023; // Replace with the desired year
    int month = 5;   // Replace with the desired month
    int day = 7;     // Replace with the desired day

    int yearday = day_of_year(year, month, day);
    printf("Day of the year: %d\n", yearday);

    int result_month, result_day;
    month_day(year, yearday, &result_month, &result_day);
    printf("Month: %d, Day: %d\n", result_month, result_day);

    return 0;
}

/* day_of_year: set day of year from month & day */
int day_of_year(int year, int month, int day)
{
    int i, leap;

    leap = year % 4 == 0 && year % 100 != 0 || year % 400 == 0;

    char *pdaytab = leap ? daytab[1] : daytab[0];

    for (i = 1; i < month; i++)
        day += *(pdaytab + i);

    return day;
}

/* month_day: set month, day from day of year */
void month_day(int year, int yearday, int *pmonth, int *pday)
{
    int i, leap;
    char *pdaytab = daytab[0];

    leap = year % 4 == 0 && year % 100 != 0 || year % 400 == 0;
    for (i = 1; yearday > *(pdaytab + i); i++)
        yearday -= *(pdaytab + i);

    *pmonth = i;
    *pday = yearday;
}
```

## Exercise 5-10

Write the program `expr`, which evaluates a reverse Polish expression from the command line, where each opeartor or operand is a separate argument. For example,

```sh
expr 2 3 4 + *
```

evaluates `2 x (3 + 4)`.
