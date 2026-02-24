# #1198 - Review 2: codecov [Closed]

> Username: jzmaddock  
> Created at: 2025-11-04 18:46:41 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-17 12:53:15 UTC  
> Url: https://github.com/boostorg/decimal/issues/1198  

There are still places marked by LCOV_EXCL_START/LCOV_EXCL_STOP that correspond to valid code paths. Please try to avoid this. It's okay not having 100% coverage, but marking corner cases as "won't happen" makes the metric less reliable.
