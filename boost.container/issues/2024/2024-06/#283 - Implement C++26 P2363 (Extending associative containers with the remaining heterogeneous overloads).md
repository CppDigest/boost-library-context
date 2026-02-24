# #283 - Implement C++26 P2363 (Extending associative containers with the remaining heterogeneous overloads) [Closed]

> Username: igaztanaga  
> Created at: 2024-06-27 11:47:17 UTC  
> Updated at: 2025-11-12 14:55:14 UTC  
> Closed at: 2025-11-12 14:55:14 UTC  
> Url: https://github.com/boostorg/container/issues/283  

See paper https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2023/p2363r5.html, the library should add:  
  
- try_emplace  
- insert_or_assign  
- operator[]  
- at  
- insert  
  
overloads.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-11-12 14:55:14 UTC  
> Url: https://github.com/boostorg/container/issues/283#issuecomment-3522359997  

With commit https://github.com/boostorg/container/commit/5353cfc736bd4f41fb7effe1e75a4d3b5311cafe, all method are implemented in associative containers.
