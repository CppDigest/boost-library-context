# #205 Isomorphism docs: AdaptableUnaryFn, null vertices [Merged]

> Username: jan-grimo  
> Created at: 2020-02-16 10:15:23 UTC  
> Updated at: 2023-07-07 00:32:24 UTC  
> Merged at: 2023-07-07 00:32:24 UTC  
> Closed at: 2023-07-07 00:32:24 UTC  
> Url: https://github.com/boostorg/graph/pull/205  

Isomorphism docs  
- The expected concept for invariant functors is AdaptableUnaryFunction,  
  not UnaryFunction  
- Null vertices can appear in the isomorphism map output if both graphs  
  contain disconnected vertices, add a note to that parameter's  
  documentation  
  
Related to #203

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2021-12-10 03:39:43 UTC  
> Url: https://github.com/boostorg/graph/pull/205#issuecomment-990582138  

Remind me, what does "Adaptable" mean here?

---

## Comment 2

> Username: jan-grimo  
> Created_at: 2021-12-14 08:45:07 UTC  
> Url: https://github.com/boostorg/graph/pull/205#issuecomment-993308339  

I had to check (it's been a while), but as far as I can tell it means (in addition to the `UnaryFunction` concept in `concept_check.hpp`) that the argument type is convertible to the concept function argument type.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2023-07-07 00:31:45 UTC  
> Url: https://github.com/boostorg/graph/pull/205#issuecomment-1624472030  

Sorry about the long delay in acting on these, we just don't have many resources at the moment.

---
