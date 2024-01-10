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

A `struct` declaration defines a type. The right brace that terminates the list of memebers may be followed by a list of variables, just as for any basic type. That is

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

The parentheses are necessary in `(*pp).x` because the precedence of the structure member operator `.` is higher than `*`. The expression `*pp.x*` means `*(pp.x)`, which is illegal here because `x` is not a pointer.

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

In the same way, `*p->str` fetches whatever str points to; `*P->str++` increments `str` after accessing whatever it points to (just like `*s++`); `(*p->Str)++` increments whatever `str` points to; and `*p++->str` increments `p` after accessing whatever `str` points to.
