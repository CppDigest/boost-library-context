# #225 Fix for #13154: added -features=zla to the Oracle compiler's command line [Merged]

> Username: mkartashev  
> Created at: 2017-08-09 13:06:09 UTC  
> Updated at: 2021-10-02 22:08:40 UTC  
> Merged at: 2017-08-09 13:38:56 UTC  
> Closed at: 2017-08-09 13:38:56 UTC  
> Url: https://github.com/boostorg/build/pull/225  

Fix for #13154: added -features=zla to the compiler's command line for both libraries that require -compat=5 (stlport and apache). For the GNU library, -compat=g is used through the -std=c++03 option and it includes -features=zla

---

## Comment 1

> Username: mkartashev  
> Created_at: 2017-08-10 10:14:21 UTC  
> Url: https://github.com/boostorg/build/pull/225#issuecomment-321511006  

Thanks!

---
