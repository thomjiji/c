```c
struct point {
    int x;
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
