# #577 - R-tree + interprocess allocator compilation error with clang -gnu++98 [Closed]

> Username: awulkiew  
> Created at: 2019-03-22 18:34:00 UTC  
> Updated at: 2019-03-28 22:10:34 UTC  
> Closed at: 2019-03-28 22:10:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/577  

See: https://www.boost.org/development/tests/develop/developer/geometry-index.html  
  
Manifested probably after some change in Container, Move or Variant.

---

## Comment 1

> Username: awulkiew  
> Created at: 2019-03-26 18:53:21 UTC  
> Updated at: 2019-03-27 17:36:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/577#issuecomment-476797640  

related: https://github.com/boostorg/container/issues/111  
fix: https://github.com/boostorg/container/commit/0691018321f03aad662d7d2ae5b3e060c7109e3c
