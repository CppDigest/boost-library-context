# #275 - Compilation fails if custom key comparison is used [Closed]

> Username: asclearuc  
> Created at: 2024-05-02 15:10:15 UTC  
> Updated at: 2024-06-05 09:24:09 UTC  
> Closed at: 2024-06-05 09:24:08 UTC  
> Url: https://github.com/boostorg/container/issues/275  

The company I work for - wants to migrate to newer version of boost (1.78 -> 1.80+), and next change (https://github.com/boostorg/container/commit/ccf13f6770409f22812fd9839314c97653e11d84#diff-0034e7b487413a758657a08287f73eaab77da00e768e8db209ed389cc59ad7a0R1226-R1270) - breaks the compilation:  
  
  * in previous version (1.78): all calls to `find` have `KeyNodeCompare(key_comp())` as a parameter  
  * in newer version (1.80+): all calls to `find` have `KeyNodeCompare()` as a parameter  
  
The code has the custom collator, which must not have default constructor.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-06-05 09:24:08 UTC  
> Url: https://github.com/boostorg/container/issues/275#issuecomment-2149314094  

I've changed all default-constructed KeyNodeCompare instances in tree in the following commit:  
  
https://github.com/boostorg/container/commit/13be16a0c8e60468f0c2a99a4c474e175aa4ee29  
  
This should solve the problem. A reduced example of your use case would help, I could add a similar tests to the regression.
