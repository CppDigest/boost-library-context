# #21 Fix compilation in case if key is void* [Merged]

> Username: Lastique  
> Created at: 2017-01-10 19:58:17 UTC  
> Updated at: 2017-02-17 10:59:46 UTC  
> Merged at: 2017-01-16 00:25:14 UTC  
> Closed at: 2017-01-16 00:25:14 UTC  
> Url: https://github.com/boostorg/intrusive/pull/21  

If key type is `void*`, wrong `operator()` is chosen because of the dummy argument that is used for SFINAE. As a result the key comparison adapter invokes the user's predicate with only one argument, which fails to compile.  
  
Use SFINAE on the operator return types instead of adding dummy arguments. Fixes ticket #12745.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2017-01-12 21:29:25 UTC  
> Url: https://github.com/boostorg/intrusive/pull/21#issuecomment-272289380  

Many thanks. It seems that putting SFINAE in the return type kills some old compilers, so maybe we need to change the void* argument to something else.

---

## Comment 2

> Username: Lastique  
> Created_at: 2017-01-14 12:32:40 UTC  
> Url: https://github.com/boostorg/intrusive/pull/21#issuecomment-272621370  

I've updated the patch to use dummy arguments again.

---

## Comment 3

> Username: igaztanaga  
> Created_at: 2017-01-16 00:25:58 UTC  
> Url: https://github.com/boostorg/intrusive/pull/21#issuecomment-272750050  

Great, all tests passed ok with the second patch. Many thanks!

---

## Comment 4

> Username: Lastique  
> Created_at: 2017-02-17 10:54:17 UTC  
> Url: https://github.com/boostorg/intrusive/pull/21#issuecomment-280619393  

A kind reminder to merge this change to master before 1.64.

---

## Comment 5

> Username: igaztanaga  
> Created_at: 2017-02-17 10:59:46 UTC  
> Url: https://github.com/boostorg/intrusive/pull/21#issuecomment-280620508  

Thanks for the reminder. Merged into master.

---
