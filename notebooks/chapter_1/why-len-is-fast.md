
# Why `len()` Is So Fast (and How You Can Use It Too)

One of Python’s most deceptively simple functions is also one of its fastest: `len()`. At a glance, you might expect that calling `len(x)` on a string, list, or memoryview would involve some method call under the hood—after all, this is an object-oriented language, right?

But Python, particularly **CPython**, doesn’t play by those rules when it comes to performance-critical operations. For built-in types like `str`, `list`, and `tuple`, calling `len(x)` doesn’t actually invoke a method. Instead, CPython reaches directly into the C-level struct that represents the object and reads a field that stores the length. No method dispatch, no overhead—just raw speed.

This design is intentional. Getting the number of items in a collection is so common and fundamental that Python gives `len()` a shortcut for built-in types. It’s the same story with `abs()`, which also receives special treatment in the Python data model.

---

## Custom Objects and `__len__`

So how does this square with Python’s otherwise elegant and consistent object model? That’s where special methods come in.

If you define a custom class and want `len()` to work on it, all you need to do is implement the `__len__()` method:

```python
class MyCollection:
    def __init__(self, items):
        self._items = items

    def __len__(self):
        return len(self._items)
```

Now, `len(MyCollection([1, 2, 3]))` will return `3`. Behind the scenes, Python checks if the object defines `__len__`, and if so, it calls it. This is a beautiful compromise between raw performance for built-in types and the flexibility and consistency that Python is known for.

> _“Special cases aren't special enough to break the rules.”_  
> — *The Zen of Python*

---

## A Legacy of Language Design

This idea isn’t new. Python’s predecessor, **ABC**, had a special operator `#` for getting the length of a collection. Writing `#s` would return the length of sequence `s`, and `x#s` would count the number of times `x` appeared in `s`—similar to `s.count(x)` in modern Python.

Even though Python chose a functional style (`len(x)`) over a method call (`x.len()`), it keeps the door open for extensibility through special methods. Think of `len()` and `abs()` as *unary operators*—they're functions on the surface, but they integrate deeply into Python’s object model.

---

## The Power of Special Methods

Here’s a quick cheat sheet of special method categories and some commonly used names:

### Emulating Collections

| Behavior       | Method                     |
|----------------|----------------------------|
| Length         | `__len__`                  |
| Indexing       | `__getitem__`, `__setitem__`, `__delitem__` |
| Membership     | `__contains__`             |
| Iteration      | `__iter__`, `__next__`, `__reversed__` |

### Numeric Operators

| Operator Type     | Symbols / Methods |
|-------------------|-------------------|
| Unary             | `__neg__`, `__pos__`, `__abs__` |
| Arithmetic        | `__add__`, `__sub__`, `__mul__`, `__truediv__`, `__floordiv__`, `__mod__`, `__pow__` |
| Reversed          | `__radd__`, `__rsub__`, `__rmul__`, etc. |
| Augmented         | `__iadd__`, `__isub__`, `__imul__`, etc. |

### Comparisons

| Operator | Method        |
|----------|---------------|
| `<`      | `__lt__`      |
| `<=`     | `__le__`      |
| `==`     | `__eq__`      |
| `!=`     | `__ne__`      |
| `>`      | `__gt__`      |
| `>=`     | `__ge__`      |

---

## Best Practices

- ✅ Use `__len__` when your custom class behaves like a collection.
- ❌ Don’t override `len()` itself—Python calls the special method for you.
- 💡 Think of `len()` and `abs()` as part of Python’s protocol-based design, not just standalone functions.
- 🔄 Keep behavior consistent with Python’s built-in types when implementing special methods.

---

## Further Reading

Want to dive deeper into Python's internals and design philosophy? These resources are worth checking out:

- [Fluent Python – Data Model](https://fpy.li/dtmodel)
- [Python Nutshell](https://fpy.li/pynut3)
- [David Beazley’s Generator Tricks](https://dabeaz.com/per.html)
- [Python Cookbook](https://fpy.li/pycook3)
- [The Art of the Metaobject Protocol](https://mitpress.mit.edu/books/art-metaobject-protocol)
