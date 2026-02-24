# #24 Replace hard-coded byte bit count with CHAR_BIT [Merged]

> Username: Kojoley  
> Created at: 2018-08-30 21:43:35 UTC  
> Updated at: 2018-09-09 17:55:09 UTC  
> Merged at: 2018-09-09 17:54:51 UTC  
> Closed at: 2018-09-09 17:54:51 UTC  
> Url: https://github.com/boostorg/pfr/pull/24  

I am not sure if it actually is a real problem, but still.

---

## Comment 1

> Username: coveralls  
> Created_at: 2018-08-30 21:54:26 UTC  
> Updated_at: 2018-08-30 21:55:29 UTC  
> Url: https://github.com/boostorg/pfr/pull/24#issuecomment-417479492  

[![Coverage Status](https://coveralls.io/builds/18762867/badge)](https://coveralls.io/builds/18762867)  
  
Coverage decreased (-0.2%) to 97.721% when pulling **793842e94e7432546498470fe827896960ff798d on Kojoley:patch-1** into **b0229248de7ee4bc775e6421eb4a9925a902cabc on apolukhin:develop**.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2018-09-03 20:08:59 UTC  
> Url: https://github.com/boostorg/pfr/pull/24#issuecomment-418186410  

Some strange error appeared on MSVC:  
```  
====== BEGIN OUTPUT ======  
common\read_write.cpp(54): test 'five_field_eq(value, result)' failed in function 'void __cdecl test_write_read<struct main::test4>(const struct main::test4 &)'  
1 error detected.  
   
EXIT STATUS: 1   
====== END OUTPUT ======  
      
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "..\..\..\bin.v2\libs\magic_get\test\precise_read_write.test\msvc-14.1\debug\threading-multi\precise_read_write.exe"   > "..\..\..\bin.v2\libs\magic_get\test\precise_read_write.test\msvc-14.1\debug\threading-multi\precise_read_write.output" 2>&1   
```  
Investigating...

---

## Comment 3

> Username: Kojoley  
> Created_at: 2018-09-03 23:23:50 UTC  
> Url: https://github.com/boostorg/pfr/pull/24#issuecomment-418204670  

I do not think it is related to the PR, because it fails globally.

---

## Comment 4

> Username: apolukhin  
> Created_at: 2018-09-09 17:55:09 UTC  
> Url: https://github.com/boostorg/pfr/pull/24#issuecomment-419732999  

Many thanks for the PR!

---
