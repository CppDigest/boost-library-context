# #33 - Add `name` to base class descriptors [Open]

> Username: pdimov  
> Created at: 2022-10-01 18:10:15 UTC  
> Updated at: 2023-08-01 18:24:10 UTC  
> Url: https://github.com/boostorg/describe/issues/33  

The macros receive it, and the reflection TS also provides it.

---

## Comment 1

> Username: reddwarf69  
> Created at: 2023-08-01 17:54:29 UTC  
> Updated at: 2023-08-01 18:20:21 UTC  
> Url: https://github.com/boostorg/describe/issues/33#issuecomment-1660819424  

What about the name of the main class?  
  
I could make use of it in something like https://www.boost.org/doc/libs/1_82_0/libs/describe/doc/html/describe.html#example_fmtlib_class_formatter. typeid(t).name() is not the same.  
  
(or should this be handled via https://www.boost.org/doc/libs/release/doc/html/boost_typeindex.html ?)

---

## Comment 2

> Username: pdimov  
> Created at: 2023-08-01 18:24:10 UTC  
> Url: https://github.com/boostorg/describe/issues/33#issuecomment-1660860259  

Makes sense in principle but I'd need to add another primitive, `describe_type<T>`, from which to return it, as there's no good place for it in the existing ones.
