# #154 Fix missing standalone define [Merged]

> Username: Flamefire  
> Created at: 2022-03-26 14:21:44 UTC  
> Updated at: 2022-03-26 16:44:42 UTC  
> Merged at: 2022-03-26 16:44:38 UTC  
> Closed at: 2022-03-26 16:44:38 UTC  
> Url: https://github.com/boostorg/nowide/pull/154  

This basically generates the standalone config.hpp from the regular config.hpp by pasting the required defines otherwise coming from `<boost/config.hpp>` into the appropriate place.  
  
This avoids the standalone config becoming out of sync with the main one which e.g. resulted in a missing `NOWIDE_USE_WCHAR_OVERLOADS`.  
This is now checked on CI by compiling (some builds) with `-Wundef` as was actually intended.  
  
As a side effect Cygwin builds using the standalone version will use the custom filebuf implementation just as they do using the Boost version. This is due to Cygwin also using `wchar_t*` for paths which requires enabling those overloads which requires the custom filebuf.  
To be exact: This is until C++17 which adds `wchar_t*` overloads to `filebuf::open` in that case, but when using C++17 and `std::filesystem::path` you'd probably want to use the `std` variants of `<fstream>` anyway.     
To remediate this allow users to overwrite `NOWIDE_USE_WCHAR_OVERLOADS` and default `NOWIDE_USE_FILEBUF_REPLACEMENT` to that allowing to overwrite this too except for (native, i.e. not Cygwin) Windows platforms.  
  
Note that while those knobs are provided not all possible configurations are actually tested and it is the users task to ensure that those defines are set in all used compilation units or there may be ODR violations. It is hence strongly advised to not change the defaults.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-03-26 16:34:36 UTC  
> Updated_at: 2022-03-26 16:39:16 UTC  
> Url: https://github.com/boostorg/nowide/pull/154#issuecomment-1079728422  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/154?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#154](https://codecov.io/gh/boostorg/nowide/pull/154?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (04a9541) into [develop](https://codecov.io/gh/boostorg/nowide/commit/1f6e9600a9c78ec27f180f13d7ed3c2b001d37b0?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1f6e960) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #154   +/-   ##  
========================================  
  Coverage    99.34%   99.34%             
========================================  
  Files           33       33             
  Lines         3051     3051             
========================================  
  Hits          3031     3031             
  Misses          20       20             
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/154?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/nowide/filebuf.hpp](https://codecov.io/gh/boostorg/nowide/pull/154/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvZmlsZWJ1Zi5ocHA=) | `94.16% <ø> (ø)` | |  
| [include/boost/nowide/fstream.hpp](https://codecov.io/gh/boostorg/nowide/pull/154/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvZnN0cmVhbS5ocHA=) | `100.00% <ø> (ø)` | |  
| [test/test\_stdio.cpp](https://codecov.io/gh/boostorg/nowide/pull/154/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3N0ZGlvLmNwcA==) | `100.00% <ø> (ø)` | |

---
