# #168 Remove BOOST_CONTAINER_DECL from memory_resource [Merged]

> Username: pdimov  
> Created at: 2020-10-24 20:53:51 UTC  
> Updated at: 2020-10-25 16:22:18 UTC  
> Merged at: 2020-10-25 14:21:50 UTC  
> Closed at: 2020-10-25 14:21:50 UTC  
> Url: https://github.com/boostorg/container/pull/168  

Hopefully fixes #165.  
  
Since all member functions of `memory_resource` are either inline or pure, and since the class itself is abstract, there should be no need to dllexport it.  
  
Tested locally on `toolset=msvc-10.0,msvc-11.0,msvc-12.0,msvc-14.0,msvc-14.1,msvc-14.2,clang-win,gcc`.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2020-10-25 14:22:08 UTC  
> Url: https://github.com/boostorg/container/pull/168#issuecomment-716156229  

Thanks Peter!

---
