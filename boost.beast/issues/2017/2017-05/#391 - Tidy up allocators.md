# #391 - Tidy up allocators [Closed]

> Username: vinniefalco  
> Created at: 2017-05-27 21:21:14 UTC  
> Updated at: 2018-04-28 20:41:57 UTC  
> Closed at: 2018-04-28 20:41:57 UTC  
> Url: https://github.com/boostorg/beast/issues/391  

* Calls to `allocate` and `deallocate` without a hint don't need to go through `allocator_traits`  
  
* `basic_fields` could provide a locality hint: a pointer to the previous element in the set

---

## Comment 1

> Username: stale[bot]  
> Created at: 2018-04-24 17:52:44 UTC  
> Url: https://github.com/boostorg/beast/issues/391#issuecomment-384022673  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-04-28 20:41:57 UTC  
> Url: https://github.com/boostorg/beast/issues/391#issuecomment-385204078  

Not worth changing
