# #79 Move superproject build into own job and disable on master [Merged]

> Username: Flamefire  
> Created at: 2020-03-24 16:47:42 UTC  
> Updated at: 2020-03-25 10:12:22 UTC  
> Merged at: 2020-03-25 10:12:17 UTC  
> Closed at: 2020-03-25 10:12:17 UTC  
> Url: https://github.com/boostorg/nowide/pull/79  

Currently Boost.Filesystem doesn't build with CMake super project due to pending merges to master from Boost.Optional and Boost.Fusion  
  
--> Disable this test on master for now

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-03-24 19:56:35 UTC  
> Updated_at: 2020-03-25 09:49:54 UTC  
> Url: https://github.com/boostorg/nowide/pull/79#issuecomment-603473202  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/79?src=pr&el=h1) Report  
> Merging [#79](https://codecov.io/gh/boostorg/nowide/pull/79?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/nowide/commit/7cf83cf3fcaaf0ee496300d8814fa03f627365e0&el=desc) will **not change** coverage by `%`.  
> The diff coverage is `n/a`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #79   +/-   ##  
========================================  
  Coverage    89.63%   89.63%             
========================================  
  Files           24       24             
  Lines         2488     2488             
  Branches       186      186             
========================================  
  Hits          2230     2230             
  Misses         252      252             
  Partials         6        6             
```

---
