# #908 guard tag against empty type [Merged]

> Username: grisumbras  
> Created at: 2024-05-20 17:28:07 UTC  
> Updated at: 2024-05-26 19:42:59 UTC  
> Merged at: 2024-05-26 19:17:46 UTC  
> Closed at: 2024-05-26 19:17:46 UTC  
> Url: https://github.com/boostorg/boost/pull/908  

In b2 `$(a) in A B C` is true when `$(a)` is empty (empty set is a subset of any set). So, the tag will produce a result for untyped targets, which is clearly not intended.  
  
This currently does not happen anyway, because b2 doesn't run `<tag>`s for untyped targets, but I intend to change that, so that users could do `make a : : @make-rule : <tag>@tag-rule ;`

---

## Comment 1

> Username: grisumbras  
> Created_at: 2024-05-26 19:10:40 UTC  
> Url: https://github.com/boostorg/boost/pull/908#issuecomment-2132367869  

@pdimov anything else I need to do for this to get merged?

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-05-26 19:17:35 UTC  
> Url: https://github.com/boostorg/boost/pull/908#issuecomment-2132369437  

> In b2 `$(a) in A B C` is true when `$(a)` is empty  
  
Amazing.

---
