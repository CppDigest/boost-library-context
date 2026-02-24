# #19 - Missing C++11's std::ctype_base::blank in cpp_regex_traits.hpp [Open]

> Username: blazej-czapp  
> Created at: 2021-05-14 09:05:28 UTC  
> Updated at: 2021-05-14 14:50:43 UTC  
> Url: https://github.com/boostorg/xpressive/issues/19  

`cpp_regex_traits.hpp` introduces its own locale `ctype` masks on top of the standard ones (starting [here](https://github.com/boostorg/xpressive/blob/956d8df7905f042fda53777ae133eb743f40c489/include/boost/xpressive/traits/cpp_regex_traits.hpp#L139)). It relies on first combining all standard flags ([here](https://github.com/boostorg/xpressive/blob/956d8df7905f042fda53777ae133eb743f40c489/include/boost/xpressive/traits/cpp_regex_traits.hpp#L133)) and then finding some unused bits for its own masks.  
  
The block of masks being or'd is missing `std::ctype_base::blank`, which was introduced in C++11.   
  
As a side note, it all happens to work fine in `libc++` because:  
  
```  
 00000110 11111111  all_ctype_masks  
 00000001 00000000  ctype_base::blank  
  
 10000000 00000000  non_std_ctype_underscore  
 01000000 00000000  non_std_ctype_blank  
 00100000 00000000  non_std_ctype_newline  
```  
  
i.e. the mask for `blank` isn't conflicting with the `non_std` ones, but that's, of course, sheer luck.

---

## Comment 1

> Username: ericniebler  
> Created at: 2021-05-14 14:50:43 UTC  
> Url: https://github.com/boostorg/xpressive/issues/19#issuecomment-841292417  

Nice catch!
