# #78 - Dependency on boost/smart_ptr [Closed]

> Username: anarthal  
> Created at: 2023-08-16 18:49:31 UTC  
> Updated at: 2023-08-17 21:34:22 UTC  
> Closed at: 2023-08-17 21:34:21 UTC  
> Url: https://github.com/boostorg/cobalt/issues/78  

Just used for allocate_unique, which seems a pretty trivial function - is it really worth it?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-08-17 01:19:38 UTC  
> Url: https://github.com/boostorg/cobalt/issues/78#issuecomment-1681467618  

I don't know - this is supposed to be a boost library, why is this dependency bad?

---

## Comment 2

> Username: anarthal  
> Created at: 2023-08-17 21:34:21 UTC  
> Url: https://github.com/boostorg/cobalt/issues/78#issuecomment-1683010131  

It makes the vcpkg package require an extra dependency, for instance. Although I guess depending on Asio might be already pulling it, so this may have no relevance.
