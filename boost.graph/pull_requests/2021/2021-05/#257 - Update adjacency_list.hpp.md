# #257 Update adjacency_list.hpp [Open]

> Username: shrek1402  
> Created at: 2021-05-05 08:48:59 UTC  
> Updated at: 2022-03-04 02:13:34 UTC  
> Url: https://github.com/boostorg/graph/pull/257  

If the project has a function with the same name, a name conflict occurs  
error C2668: add_edge

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2022-03-04 02:13:34 UTC  
> Url: https://github.com/boostorg/graph/pull/257#issuecomment-1058757411  

Hmmm, I think it's intentionally unqualified so that the function is found via ADL, although I could be wrong.  
What was the signature of your `add_edge` function and what namespace was it in?

---
