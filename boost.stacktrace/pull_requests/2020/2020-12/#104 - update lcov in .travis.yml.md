# #104 update lcov in .travis.yml [Merged]

> Username: sdarwin  
> Created at: 2020-12-30 15:55:35 UTC  
> Updated at: 2021-01-03 09:54:37 UTC  
> Merged at: 2021-01-03 09:51:28 UTC  
> Closed at: 2021-01-03 09:51:28 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/104  

Fix travis errors:  
  
```  
geninfo: ERROR: need tool gcov-6!  
```  
   
```  
lcov: ERROR: cannot read file /home/travis/build/boostorg/any/coverals/coverage.info!  
```

---

## Comment 1

> Username: coveralls  
> Created_at: 2020-12-31 06:00:52 UTC  
> Updated_at: 2020-12-31 06:07:44 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/104#issuecomment-752855910  

[![Coverage Status](https://coveralls.io/builds/36016499/badge)](https://coveralls.io/builds/36016499)  
  
Coverage decreased (-60.2%) to 32.475% when pulling **7fdaebb8efe11bb9a08749a8d90931ab4688a5b6 on sdarwin:lcov** into **15f6b30f126f675a332e98622cba8eaa459daead on boostorg:develop**.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2021-01-03 09:54:37 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/104#issuecomment-753592690  

Many thanks!  
  
I've noted that coverage drops down because the information on coverage of nearby Boost libraries is not removed. For example Stacktrace coverage now includes coverage of Filesystem, Timer and Thread libraries.  
  
A fix would be appreciated! :)

---
