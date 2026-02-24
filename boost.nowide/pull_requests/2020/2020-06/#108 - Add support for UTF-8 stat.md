# #108 Add support for UTF-8 stat [Merged]

> Username: Flamefire  
> Created at: 2020-06-16 17:06:46 UTC  
> Updated at: 2020-06-18 15:58:21 UTC  
> Merged at: 2020-06-18 15:58:16 UTC  
> Closed at: 2020-06-18 15:58:16 UTC  
> Url: https://github.com/boostorg/nowide/pull/108  

As proposed in https://github.com/boostorg/nowide/issues/43#issuecomment-644583376 this implements `stat` by defining `stat_t` as the preferred struct type which resolves to the 64 bit type on Windows. On Linux the `struct stat` already uses `off_t` as the file size type and hence can usually deal with 64 bit file sizes, at least on 64 bit systems.  
  
On Windows an overload is provided so `stat_t` and `struct stat` can both be used. The latter is size-checked and EINVAL is returned if the size doesn't match (which should not occur in practice).     
Afterwards `_wstat64/_wstat` is used with the widened path passed.  
  
Note that on windows all names are prefixed by underscore in the mscrt, hence `stat` becomes `_stat` and `struct stat` becomes `struct _stat`. To allow seemless interop the typedefs `stat_t` and `posix_stat_t` exist as a convenience.  
  
Fixes #43

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-06-17 08:57:02 UTC  
> Updated_at: 2020-06-18 15:29:09 UTC  
> Url: https://github.com/boostorg/nowide/pull/108#issuecomment-645247527  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/108?src=pr&el=h1) Report  
> Merging [#108](https://codecov.io/gh/boostorg/nowide/pull/108?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/nowide/commit/49f016cfd9c485cba191d83e46f93f97cb630b43&el=desc) will **increase** coverage by `0.14%`.  
> The diff coverage is `100.00%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #108      +/-   ##  
===========================================  
+ Coverage    90.17%   90.31%   +0.14%       
===========================================  
  Files           24       27       +3       
  Lines         2524     2561      +37       
  Branches       185      185                
===========================================  
+ Hits          2276     2313      +37       
  Misses         242      242                
  Partials         6        6                
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/108?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/nowide/stat.hpp](https://codecov.io/gh/boostorg/nowide/pull/108/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvc3RhdC5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [src/stat.cpp](https://codecov.io/gh/boostorg/nowide/pull/108/diff?src=pr&el=tree#diff-c3JjL3N0YXQuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_stat.cpp](https://codecov.io/gh/boostorg/nowide/pull/108/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X3N0YXQuY3Bw) | `100.00% <100.00%> (ø)` | |

---
