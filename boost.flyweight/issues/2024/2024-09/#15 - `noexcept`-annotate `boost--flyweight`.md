# #15 - `noexcept`-annotate `boost::flyweight` [Closed]

> Username: joaquintides  
> Created at: 2024-09-14 08:56:07 UTC  
> Updated at: 2024-09-15 10:24:55 UTC  
> Closed at: 2024-09-15 10:24:55 UTC  
> Url: https://github.com/boostorg/flyweight/issues/15  

Add `noexcept` specifiers to `boost::flyweight` operations where applicable (basically, where [the reference](https://www.boost.org/doc/libs/1_86_0/libs/flyweight/doc/reference/flyweight.html) mentions that the operation is `nothrow`).

---

## Comment 1

> Username: joaquintides  
> Created at: 2024-09-15 10:24:55 UTC  
> Url: https://github.com/boostorg/flyweight/issues/15#issuecomment-2351520650  

Fixed by #16.
