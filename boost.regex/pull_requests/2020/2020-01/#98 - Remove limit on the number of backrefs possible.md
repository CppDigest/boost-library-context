# #98 Remove limit on the number of backrefs possible. [Merged]

> Username: jzmaddock  
> Created at: 2020-01-19 11:28:59 UTC  
> Updated at: 2022-03-19 16:30:05 UTC  
> Merged at: 2020-01-21 10:13:41 UTC  
> Closed at: 2020-01-21 10:13:41 UTC  
> Url: https://github.com/boostorg/regex/pull/98  

Changes named sub-expressions to use different hashing scheme: high order bit is now always set to clashes between hashes and indexes don't happen until 2^30 or 2^62 sub-expressions in 32 and 64 bit code respectively.  
Changes bitmask of seen sub-expressions to use dynamic storage for sub-expression indexes above 64.  
Adds tests for the above.  
Fixes https://github.com/boostorg/regex/issues/75.

---
