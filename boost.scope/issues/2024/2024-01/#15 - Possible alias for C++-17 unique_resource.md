# #15 - Possible alias for C++-17 unique_resource [Closed]

> Username: grisumbras  
> Created at: 2024-01-25 18:26:57 UTC  
> Updated at: 2024-01-25 21:23:13 UTC  
> Closed at: 2024-01-25 20:49:08 UTC  
> Url: https://github.com/boostorg/scope/issues/15  

One review suggested a different design for `unique_resource`, which puts default resource and deleter into template parameters. I've realised this can be easily replicated with an alias and a few helper templates: https://godbolt.org/z/aMToasrG8

---

## Comment 1

> Username: Lastique  
> Created at: 2024-01-25 20:49:08 UTC  
> Updated at: 2024-01-25 21:23:13 UTC  
> Url: https://github.com/boostorg/scope/issues/15#issuecomment-1910973922  

Yes, I had a similar idea. This is now implemented in https://github.com/Lastique/scope/commit/d45a6255aae23fa5c2b3241a82d82455f468507f and https://github.com/boostorg/core/pull/161. If the latter doesn't get merged, I can move it here.  
  
I still prefer to have separate templates for the deleter and resource traits because this allows for multiple unallocated resource values instead of just one. Windows `HANDLE`s are one example where this is useful. This also allows to mix and match `functor`, custom deleter class types, `unallocated_resource`, custom resource traits and no traits.
