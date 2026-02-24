# #206 crash when generating sphinx documentation after MSVC upgrade [Closed]

> Username: ulrichard  
> Created at: 2018-05-30 07:05:19 UTC  
> Updated at: 2022-07-14 11:35:05 UTC  
> Closed at: 2022-07-14 11:35:05 UTC  
> Url: https://github.com/boostorg/python/pull/206  

Generating sphinx documentation worked fine for our python embedding dll built with boost python.  
After the compiler upgrade to VisualStudio 15.7.2  it always leads to a crash.  
Boost or python were not changed, only MSVC. Recompiling boost with the new MSVC didn't help.  
I could prevent the access violation with this nullptr check.  
I didn't research the root cause further from there. There might be a better solution.

---

## Comment 1

> Username: SPKorhonen  
> Created_at: 2018-05-30 11:55:19 UTC  
> Url: https://github.com/boostorg/python/pull/206#issuecomment-393133631  

The underlying cause is reported in issue #207 and fixed by pull request #208.  
  
This pull request masks the problem when accessing doc-strings (via __doc__) attribute but still leaves the wrapped function objects in invalid state which leads to hard to diagnose problems when calling functions. Most often you get spurious errors stating the the call signature is not compatible.

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2019-03-24 00:41:22 UTC  
> Url: https://github.com/boostorg/python/pull/206#issuecomment-475917327  

Given that #208 is now merged, this PR should be obsolete. @SPKorhonen can you confirm that, please ?

---
