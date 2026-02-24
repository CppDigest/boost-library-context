# #216 - Locale testing [Open]

> Username: anarthal  
> Created at: 2024-02-13 11:10:21 UTC  
> Updated at: 2024-02-13 11:10:22 UTC  
> Url: https://github.com/boostorg/mysql/issues/216  

#69 (with the advent of Boost.Charconv) solves a problem of locale-dependency. We should have tests to verify that we don't fail parsing regardless of the locale used.  
  
Note that Docker images need to be updated to support the locale under test.
