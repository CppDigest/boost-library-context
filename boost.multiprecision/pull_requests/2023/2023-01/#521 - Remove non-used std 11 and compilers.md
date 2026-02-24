# #521 Remove non-used std 11 and compilers [Merged]

> Username: ckormanyos  
> Created at: 2023-01-17 06:30:30 UTC  
> Updated at: 2023-01-17 18:11:45 UTC  
> Merged at: 2023-01-17 18:11:41 UTC  
> Closed at: 2023-01-17 18:11:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/521  

It seems like some tiny artifacts can be removed from GHA script YAML.  
- The removal of the _11_ in `11, 14, 17` seems OK since no tests were running there.  
- It seems like g++-9 and clang++ aren't used and aren't needed in the `ubuntu-focal-slow-tests` job in which they were installed.

---
