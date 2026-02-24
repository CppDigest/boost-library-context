# #344 Fix compatibility with Python 3.10.0a4: fopen [Merged]

> Username: vstinner  
> Created at: 2021-01-06 09:12:15 UTC  
> Updated at: 2021-01-06 21:02:34 UTC  
> Merged at: 2021-01-06 12:55:49 UTC  
> Closed at: 2021-01-06 12:55:49 UTC  
> Url: https://github.com/boostorg/python/pull/344  

Replace private _Py_fopen() with public fopen(): private _Py_fopen()  
function was removed in 3.10.0a4:  
https://bugs.python.org/issue32381

---

## Comment 1

> Username: vstinner  
> Created_at: 2021-01-06 09:12:27 UTC  
> Url: https://github.com/boostorg/python/pull/344#issuecomment-755180039  

Fedora downstream issue: https://bugzilla.redhat.com/show_bug.cgi?id=1912903

---

## Comment 2

> Username: vstinner  
> Created_at: 2021-01-06 09:14:00 UTC  
> Url: https://github.com/boostorg/python/pull/344#issuecomment-755180764  

If Boost needs a public C API for https://www.python.org/dev/peps/pep-0446/ please ask for it :-) But it would be a new Python feature. In general, third party projects should not rely on private functions, or be prepared for breakage at new Python version.

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2021-01-06 12:55:55 UTC  
> Url: https://github.com/boostorg/python/pull/344#issuecomment-755283718  

Thanks !

---
