# #208  Fix static object initialization under Visual Studio 2017 [Merged]

> Username: SPKorhonen  
> Created at: 2018-05-30 08:04:30 UTC  
> Updated at: 2019-03-04 02:59:49 UTC  
> Merged at: 2019-03-04 02:59:49 UTC  
> Closed at: 2019-03-04 02:59:49 UTC  
> Url: https://github.com/boostorg/python/pull/208  

By separating the initialization of "static const signature_element ret" into separate function defined in detail namespace the compiler correctly initializes the static variable.  
  
Fixes issue #207

---

## Review 1 [Commented]

> Username: stefanseefeld  
> Created_at: 2018-05-30 12:11:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/208#pullrequestreview-124371331  

Would you please add an inline comment to explain the change ? Later maintainers of the code will be gratefull... :-)

---

## Comment 2

> Username: SPKorhonen  
> Created_at: 2018-05-30 13:33:58 UTC  
> Url: https://github.com/boostorg/python/pull/208#issuecomment-393163395  

Added comments and fixed indent from tab to space

---
