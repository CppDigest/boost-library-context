# #176 - Protect <> in examples [Open]

> Username: mglisse  
> Created at: 2019-07-17 12:25:44 UTC  
> Updated at: 2019-08-07 03:55:59 UTC  
> Url: https://github.com/boostorg/graph/issues/176  

https://www.boost.org/doc/libs/1_70_0/libs/graph/doc/incremental_components.html  
The first example, from examples/incremental_components.cpp, was copy-pasted raw, without protecting special characters from HTML, so in particular all templates are invisible.

---

## Comment 1

> Username: anadon  
> Created at: 2019-08-07 03:55:59 UTC  
> Url: https://github.com/boostorg/graph/issues/176#issuecomment-518929471  

I'll queue this up for documentation fixes after I'm done with CI.
