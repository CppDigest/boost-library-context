# #550 build: Remove most of uses of cxxflags from test/Jamfile [Merged]

> Username: mloskot  
> Created at: 2021-01-26 23:44:03 UTC  
> Updated at: 2021-01-27 06:08:02 UTC  
> Merged at: 2021-01-27 06:07:52 UTC  
> Closed at: 2021-01-27 06:07:52 UTC  
> Url: https://github.com/boostorg/gil/pull/550  

Most uses of cxxflags are wrong.  
Use b2 warnings instead, e.g. warnings=pedantic

---
