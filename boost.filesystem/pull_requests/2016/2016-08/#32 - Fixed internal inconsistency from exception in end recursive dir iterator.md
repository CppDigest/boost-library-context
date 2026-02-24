# #32 Fixed internal inconsistency from exception in end recursive dir iterator [Closed]

> Username: ansingh  
> Created at: 2016-08-29 07:44:57 UTC  
> Updated at: 2018-11-24 17:16:22 UTC  
> Closed at: 2018-11-24 17:16:22 UTC  
> Url: https://github.com/boostorg/filesystem/pull/32  

If there’s an exception during increment, the check to make end  
iterator is skipped. If this happens on the last element of recursive  
directory iterator, the iterator is left in an inconsistent state.

---

## Comment 1

> Username: Lastique  
> Created_at: 2018-11-24 17:16:21 UTC  
> Url: https://github.com/boostorg/filesystem/pull/32#issuecomment-441382161  

This should be already fixed in the current develop.

---
