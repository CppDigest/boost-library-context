# #113 - wrong number of decimal places formatting mpf_float as fixed-point [Closed]

> Username: emfrias  
> Created at: 2019-02-12 22:28:07 UTC  
> Updated at: 2019-03-04 18:38:42 UTC  
> Closed at: 2019-03-04 18:38:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/113  

Hello,  
  
I've encountered a bug where I get the wrong number of digits when formatting an mpf_float_50 as fixed precision:  
```  
std::cout << std::setprecision(3) << std::fixed << boost::multiprecision::mpf_float_50("99.9809") << "\n";  
```  
This prints "99.9809" instead of the expected "99.981".  
  
I traced into it a little and it looks like the code starts by [calling `mpf_get_str()`](https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/gmp.hpp#L292)  asking for three digits,  
which returns `"1"` with `e == 3` (meaning 100).  So it turns around and [calls mpf_get_str()](https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/gmp.hpp#L345) again, this time asking  
for 6 digits, and this time it returns `"999809"` with `e == 2` (meaning 99.9809) -- the extra digits caused `mpf_get_str()` to round differently.  
  
I've hacked my copy to check whether `e` changes, and if so, adjusts `digits` accordingly and calling `mpf_get_str()` a third time, but I'm not sure that's the right solution.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-03-04 18:38:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/113#issuecomment-469367890  

Will be in the next release.
