# #184 - Consider adding allocator support [Closed]

> Username: eterchun  
> Created at: 2024-07-29 21:20:54 UTC  
> Updated at: 2024-09-30 04:53:11 UTC  
> Closed at: 2024-09-30 04:53:11 UTC  
> Url: https://github.com/boostorg/parser/issues/184  

One of the apparent benefits of a library like lexy over the current spirit libraries is that lexy supports allocators via the built-in types (e.g., [for containers](https://lexy.foonathan.net/reference/callback/container/#as_list) and [for strings](https://lexy.foonathan.net/reference/callback/string/#as_string)). Based on the current documentation, it appears the intention is [not to support allocators in Boost.Parser](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/memory_allocation.html). However, it seems that the current design could instead support custom allocators, for example via globals or perhaps parameters.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-09-30 04:53:11 UTC  
> Url: https://github.com/boostorg/parser/issues/184#issuecomment-2382055270  

Allocators suck.  I won't be doing this.  However, you can use your own containers with your own custom allocators, and parse into them.  For instance, parsing into a `pmr::vector<pmr::vector<...>>`.
