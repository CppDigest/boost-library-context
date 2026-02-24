# #262 - [Question] Is predecessor map in `astar_search<>()` redundant? [Open]

> Username: qbit86  
> Created at: 2021-06-01 22:39:00 UTC  
> Updated at: 2023-05-07 23:56:16 UTC  
> Url: https://github.com/boostorg/graph/issues/262  

Why does API [takes](https://github.com/boostorg/graph/blob/d865589d62afba7ca590bfcaf76f0723f8494dd4/include/boost/graph/astar_search.hpp#L357) predecessor map as an argument? Anyway, it can be restored using the `edge_relaxed` visitor callback, right?

---

## Comment 1

> Username: hdu-sdlzx  
> Created at: 2022-12-10 03:28:40 UTC  
> Url: https://github.com/boostorg/graph/issues/262#issuecomment-1345015495  

Maybe they are for different use cases?  
An argument provides a way for algorithm users to get results, and a callback is used by algorithm designers to use the function as building block.  
To me, callbacks are more powerful but arguments are easier to use.
