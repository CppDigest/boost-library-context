# #236 Update PGI C++ compiler support [Merged]

> Username: dkolsen-pgi  
> Created at: 2017-07-26 19:56:33 UTC  
> Updated at: 2017-07-28 22:03:16 UTC  
> Merged at: 2017-07-26 23:02:26 UTC  
> Closed at: 2017-07-26 23:02:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/236  

Remove a PGI-specific workaround in the functor constructor.  The workaround was necessary several years ago to get class functor to compiler with PGI, but the workaround now causes compilation errors.

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-07-26 21:48:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/236#issuecomment-318192838  

If the workaround is needed for older versions of the compiler, why not add a version check?

---

## Comment 2

> Username: djowel  
> Created_at: 2017-07-28 21:45:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/236#issuecomment-318769102  

Very good point, actually.

---

## Comment 3

> Username: dkolsen-pgi  
> Created_at: 2017-07-28 22:03:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/236#issuecomment-318772309  

The older versions of the PGI compiler that would benefit from the workaround are not actively being used by any customers that we (PGI) know of.  Adding a version check would add clutter to the code with no real benefit.

---
