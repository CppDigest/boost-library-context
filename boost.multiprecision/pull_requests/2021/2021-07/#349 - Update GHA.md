# #349 Update GHA [Merged]

> Username: mborland  
> Created at: 2021-07-25 17:48:16 UTC  
> Updated at: 2021-07-31 15:00:53 UTC  
> Merged at: 2021-07-31 12:24:38 UTC  
> Closed at: 2021-07-31 12:24:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/349  

Couple changes to the GHA runs:  
  
1. Removes testing on Ubuntu 16.04 (deprecated and will be removed in september)  
2. Removes g++5 and clang++5 testing  
3. Adds g++11 and clang++11 testing

---

## Comment 1

> Username: mborland  
> Created_at: 2021-07-27 11:09:40 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/349#issuecomment-887422591  

This is clean minus a OOM error in one of the ASAN tests. Not sure if that is spurious or not.

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2021-07-27 11:54:24 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/349#issuecomment-887448864  

> error in one of the ASAN tests. Not sure if that is spurious or not.  
  
Although I'm not sure and don't have time to look deeply, that test fail has the _look and feel_ of an actual failure, judging from the single failure in the log file of the test. Although on one single compiler, one single file seems strange.

---

## Comment 3

> Username: mborland  
> Created_at: 2021-07-28 18:14:14 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/349#issuecomment-888517815  

> > error in one of the ASAN tests. Not sure if that is spurious or not.  
>   
> Although I'm not sure and don't have time to look deeply, that test fail has the _look and feel_ of an actual failure, judging from the single failure in the log file of the test. Although on one single compiler, one single file seems strange.  
  
It looks like that test consistently fails for OOM. I added the config option for `is_ci_sanitizer_run` from math to disable certain tests.

---

## Comment 4

> Username: mborland  
> Created_at: 2021-07-31 09:20:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/349#issuecomment-890317930  

This is clean now

---
