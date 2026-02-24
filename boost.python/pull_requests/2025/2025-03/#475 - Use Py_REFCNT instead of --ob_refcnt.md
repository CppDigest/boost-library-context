# #475 Use Py_REFCNT instead of ->ob_refcnt [Merged]

> Username: pilleye  
> Created at: 2025-03-10 18:04:40 UTC  
> Updated at: 2025-03-10 20:35:31 UTC  
> Merged at: 2025-03-10 19:10:12 UTC  
> Closed at: 2025-03-10 19:10:12 UTC  
> Url: https://github.com/boostorg/python/pull/475  

Py_REFCNT was stabilized in 3.9, uses this official API instead of the `ob_refcnt` field that doesn't exist in the free-threaded build of 3.13.  
  
Closes #464.

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2025-03-10 18:15:25 UTC  
> Url: https://github.com/boostorg/python/pull/475#issuecomment-2711442369  

Please conditionalize the use of `Py_REFCNT` to allow for the code to work with older Python versions as well.

---

## Comment 2

> Username: pilleye  
> Created_at: 2025-03-10 18:20:26 UTC  
> Url: https://github.com/boostorg/python/pull/475#issuecomment-2711454080  

Fixed.

---
