# #17 - don't use void* when it should be void (*)() [Closed]

> Username: jll63  
> Created at: 2025-05-10 22:06:02 UTC  
> Updated at: 2025-06-01 17:50:24 UTC  
> Closed at: 2025-06-01 17:50:24 UTC  
> Url: https://github.com/boostorg/openmethod/issues/17  



---

## Comment 1

> Username: jll63  
> Created at: 2025-05-26 21:25:56 UTC  
> Url: https://github.com/boostorg/openmethod/issues/17#issuecomment-2910650589  

There is still the issue of `deferred_static_rtti`, which converts function pointers to `type_id`s.
