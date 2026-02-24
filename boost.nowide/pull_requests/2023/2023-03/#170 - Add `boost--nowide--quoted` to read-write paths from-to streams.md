# #170 Add `boost::nowide::quoted` to read/write paths from/to streams. [Merged]

> Username: Flamefire  
> Created at: 2023-03-02 19:02:53 UTC  
> Updated at: 2023-03-04 15:48:29 UTC  
> Merged at: 2023-03-04 15:48:25 UTC  
> Closed at: 2023-03-04 15:48:25 UTC  
> Url: https://github.com/boostorg/nowide/pull/170  

Closes #136  
  
@SandroWissmann This will allow you to do e.g. `os << boost::nowide::quoted(path)`  
  
I hope that meets your requirements.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-03-03 18:23:18 UTC  
> Updated_at: 2023-03-04 15:38:20 UTC  
> Url: https://github.com/boostorg/nowide/pull/170#issuecomment-1453922316  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/170?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#170](https://codecov.io/gh/boostorg/nowide/pull/170?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (990710a) into [develop](https://codecov.io/gh/boostorg/nowide/commit/cdcd7043c6ac0b1b67dd91aa50888bd17e3d0159?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (cdcd704) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #170   +/-   ##  
========================================  
  Coverage    99.46%   99.47%             
========================================  
  Files           33       34    +1       
  Lines         3387     3447   +60       
========================================  
+ Hits          3369     3429   +60       
  Misses          18       18             
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/170?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/nowide/quoted.hpp](https://codecov.io/gh/boostorg/nowide/pull/170?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvcXVvdGVkLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/nowide/utf/convert.hpp](https://codecov.io/gh/boostorg/nowide/pull/170?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvdXRmL2NvbnZlcnQuaHBw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_fs.cpp](https://codecov.io/gh/boostorg/nowide/pull/170?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2ZzLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_traits.cpp](https://codecov.io/gh/boostorg/nowide/pull/170?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3RyYWl0cy5jcHA=) | `100.00% <0.00%> (ø)` | |  
| [test/test\_convert.cpp](https://codecov.io/gh/boostorg/nowide/pull/170?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvbnZlcnQuY3Bw) | `100.00% <0.00%> (ø)` | |  
  
</details>

---
