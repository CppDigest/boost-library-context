# #724 Fix: annotate_on_success doesn't annotate variant [Merged]

> Username: goblin-rulez  
> Created at: 2022-04-24 13:48:49 UTC  
> Updated at: 2022-04-25 08:20:17 UTC  
> Merged at: 2022-04-25 00:49:22 UTC  
> Closed at: 2022-04-25 00:49:22 UTC  
> Url: https://github.com/boostorg/spirit/pull/724  

- disable the generic version of on_success if traits::is_variant<T> is true  
- specialize on_success for forward_ast<T>, and call on_success() with the result of ast.get()  
  
  
Fixes #723

---
