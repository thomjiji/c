<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**

- [6.1 Basic of structures](#61-basic-of-structures)
- [6.2 Structures and functions](#62-structures-and-functions)
- [6.3 Arrays of structures](#63-arrays-of-structures)

<!-- markdown-toc end -->


# 6.1 Basic of structures

```c
struct point { // <== "point" is called "structure tag"
    int x;     // <== variables in it are called "members"
    int y;
}
```

The keyword `struct` introduces a structure declaration, which is a list of declarations enclosed in braces. An optional name called a *structure tag* may follow the word `struct`. The tag names this kind of structure, and can be used subsequently as a shorthand for the part of the declaration in braces.

The variables named in a structure are called *members*.

一个 `struct` 的名字叫作 tag，`struct` 里面的变量叫作 members。

A `struct` declaration defines a type. The right brace that terminates the list of members may be followed by a list of variables, just as for any basic type. That is

```c
struct { ... } x, y, z;
```

is syntactically analogous to

```c
int x, y, z;
```

in the sense that each statement declares `x`, `y`, `z` to be variables of the named type and causes space to be set aside for them.

---

A structure delcaration that is not followed by a list of variables reserves no storage; it merely describes a template or the shape of a structure. If the declaration is tagged, however, the tag can be used later in the definitions of instances of the structure. For example, given the declaration of `point`,

```c
struct point pt;
```

**defines** a variable `pt` which is a structure of type `struct point`.

---

Structures can be nested.

```c
struct rect {
    struct point pt1;
    struct point pt2;
}
```

# 6.2 Structures and functions

The only legal operations on a structure are copying it or assigning to it as a unit, taking its address with `&`, and accessing its members. Copy and assignment include passing arguments to functions and returning values from functions as well.

---

If a large structure is to be passed to a function, it's generally more efficient to pass a pointer than to copy the whole structure. Structure pointers are just like pointers to ordinary variables.

```c
struct point *pp;
```

says that `pp` is a pointer to a structure of type `struct point`.

If `pp` points to a `point` struct, `*pp` is the structure, and `(*pp).x` and `(*pp).y` are the memebers. To use `pp`, we might write, for example

```c
struct point origin, *pp;

pp = &origin;
printf("origin is (%d, %d)\n", (*pp).x, (*pp).y);
```

The parentheses are necessary in `(*pp).x` because the precedence of the structure member operator `.` is higher than `*`. The expression `*pp.x` means `*(pp.x)`, which is illegal here because `x` is not a pointer.

---

Pointers to structures are so frequently used that an alternative notation is provided as a shorthand. If `p` is a pointer to a structure, then

```c
p->member-of-structure
```

refers to the particular member. So we could write instead

```c
printf("origin is (%d, %d)\n", pp->x, pp->y);
```

Both `.` and `->` associate from left to right, so if we have

```c
struct rect r, *rp = &r;
```

then these four expressions are equivalent:

```c
r.pt1.x
rp->pt1.x
(r.pt1).x
(rp->pt1).x
```

---

The structure operators `.` and `->`, together with `()` for function calls and `[]` for subscripts, are at the top of the precedence hierarchy and thus bind very tightly.

```c
struct {
    int len;
    char *str;
} *p;
```

then

```c
++p->len
```

increments `len`, not `p`, because the implied parenthesization is `++(p->len)`. Parentheses can be used to alter the binding: `(++p)->len` increments `p` before accessing `len`, and `(p++)->len` increments `p` afterward. (This last set of parentheses is unnecessary.)

In the same way, `*p->str` fetches whatever `str` points to; `*p->str++` increments `str` after accessing whatever it points to (just like `*s++`); `(*p->str)++` increments whatever `str` points to; and `*p++->str` increments `p` after accessing whatever `str` points to.

# 6.3 Arrays of structures

```c
struct key {
    char *word;
    int count;
} keytab[NKEYS];
```

declares a structure type `key`, defines an array `keytab` of structures of this type, and sets aside storage for them. Each element of the array is a structure. This could also be written

```c
struct key {
    char *word;
    int count;
};

struct key keytab[NKEYS];
```

---

C provides a compile-time unary operator called `sizeof` that can be used to compute the size of *any* object. The expressions

```c
sizeof object
```

and

```c
sizeof(type name)
```

yield an integer equal to the size of the specified object or type in bytes. An object can be a variable or array or structure. A type name can be the name of a basic type like `int` or `double`, or a derived type like a structure or a pointer.

---

```c
// getword: get next word or character from input
int getword(char *word, int lim)
{
    int c, getch(void);
    void ungetch(int);
    char *w = word;

    while (isspace(c = getch()))
        ;
    if (c != EOF)
        *w++ = c;
    if (!isalpha(c))
    {
        *w = '\0';
        return c;
    }
    for (; --lim > 0; w++)
        if (!isalnum(*w = getch()))
        {
            ungetch(*w);
            break;
        }
    *w = '\0';
    return word[0];
}
```

`getword` fetches the next "word" from the input, where a word is either a string of letters and digits beginning with a letter, or a single non-white space character.

`getword` 会 pickup 的：

1. a string of letters;
2. digits beginning with a letter;
3. single non-white space character.

---

The function value is the first character of the word, or `EOF` for end of file, or the character itself if it is not alphabetic.

`getword` 可能返回的：

1. first character of the word;
2. `EOF`;
3. character if it's not alphabetic (for example digits, and symbols: `+`, `&`...).

---

上面可能 `getword` 可能返回第 2 第 3 点，具体会从以下代码返回：

```c
    if (!isalpha(c))
    {
        *w = '\0';
        return c;
    }
```

第 1 点会从最后一行代码返回：`return word[0];`。

---

这个函数一个一个的从 input 当中提取关键词。每运行一次，提取一个关键词。具体工作原理如下：

第一个 while loop 排除掉行首的空格。如果 `c` 不等于 `EOF`，那么把 `c` 赋值给此时 `w` 所在的位置，替换掉原本可能存在的值。然后把 `w` 这个指针进一位，指向下一个元素。再检查 `c` 是否是字母，如果不是，那么将此时 `w` 指向的值设为 null character，然后直接返回 `c` 值。

---

`getword` also uses `isspace` to skip white space, `isalpha` to identify letters, and `isalnum` to identify letters and digits.

# Exercises

## Exercise 6-1

Our version of `getword` does not properly handle underscores, string constants, comments, or preprocessor control lines. Write a better version.
