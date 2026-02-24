# #311 - CMakeLists.txt appears to have far too many dependencies [Open]

> Username: barcharcraz  
> Created at: 2021-06-03 10:18:36 UTC  
> Updated at: 2021-09-13 15:43:07 UTC  
> Url: https://github.com/boostorg/test/issues/311  

It seems like the CMakeLists.txt that was added a week ago lists a bunch of dependencies that boost test doesn't actually depend on, except for it's own tests/examples. MPL seems to be one example but there may be others.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-09-13 15:43:07 UTC  
> Url: https://github.com/boostorg/test/issues/311#issuecomment-918323410  

https://pdimov.github.io/boostdep-report/develop/test.html#mpl
