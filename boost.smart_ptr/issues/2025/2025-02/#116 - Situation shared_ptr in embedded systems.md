# #116 - Situation shared_ptr in embedded systems [Open]

> Username: jplcz  
> Created at: 2025-02-19 20:28:25 UTC  
> Updated at: 2025-02-20 19:22:52 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/116  

I have created Proof of Concept inside https://github.com/jplcz/smart_ptr/pull/1/files  
  
General idea is to allow "nothrow" allocation of shared_ptr class of objects from smart_ptr library, however without need to go through expensive exception handling code, which can be ofter either disabled or it's usage available only in certain scopes (e.g. kernel mode code).   
In such cases, developers may want to simply handle errors instead of crashing entire thread.  
  
I have also added ``BOOST_SP_EMBEDDED`` ifdefs, which allow developer to disable any APIs which may throw. Currently it requires using ``sp_nothrow_tag`` to constructors. Plain functions get ``try`` prefix, such as ``try_make_unique`` or ``try_make_shared``. If anything fails, they simply return empty smart pointer. Another method is ``reset``, which now becomes ``try_reset`` - instead of leaving object in "partial" state, it doesn't modify the object if memory allocated would fail.  
  
Are there any plans to introduce such feature in the library ?

---

## Comment 1

> Username: pdimov  
> Created at: 2025-02-20 00:18:45 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/116#issuecomment-2670072847  

But `shared_ptr` already supports -fno-exceptions.

---

## Comment 2

> Username: jplcz  
> Created at: 2025-02-20 06:47:44 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/116#issuecomment-2670606189  

It "works" if you expect program to crash on OOM (not good idea in e.g. kernel mode), however the idea is to recover the program in exception-less environment or when exceptions are completely unnecessary.  
Shared_count will crash on OOM because it can recover only through throw.  Make_shared and friends depend on shared_count and they're going to have the same issue. For make_unique workaround is just to initialize `unique_ptr(new(std::nothrow) Foo)`

---

## Comment 3

> Username: pdimov  
> Created at: 2025-02-20 19:22:50 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/116#issuecomment-2672465243  

Right. So the suggestion is, for every function or constructor that allocates, introduce a non-throwing overload that returns an empty pointer instead?  
  
Can you summarize these new additions (as in, list their declarations here) so that I don't have to infer them from the diff?
