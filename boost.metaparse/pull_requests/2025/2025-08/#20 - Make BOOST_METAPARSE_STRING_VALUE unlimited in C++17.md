# #20 Make BOOST_METAPARSE_STRING_VALUE unlimited in C++17 [Open]

> Username: denzor200  
> Created at: 2025-08-31 14:43:47 UTC  
> Updated at: 2025-10-19 08:51:19 UTC  
> Url: https://github.com/boostorg/metaparse/pull/20  



---

## Review 1 [Commented]

> Username: sabel83  
> Created_at: 2025-09-02 18:13:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/metaparse/pull/20#pullrequestreview-3177634924  

📁 include/boost/metaparse/v1/impl/fixed_string.hpp

```diff
  27 |+         };
  28 |+ 
  29 |+         template<typename CharT, std::size_t N>
```

> Username: sabel83  
> Created_at: 2025-09-02 18:13:42 UTC  
> Url: https://github.com/boostorg/metaparse/pull/20#discussion_r2316821136  

Please consider using a helper template function (e.g. `make_fixed_string` or something similar) instead of a deduction guide.  
  
I know that this is what deduction guides were added to the language for, but on the other hand replacing them with other techniques increases the portability of the code (e.g. it makes it possible to remove the requirement from the test and you should also be able to just add the new test case to the existing `string_value.cpp` test file). Note that a helper function doesn't add constraints to the users of the new `STRING_VALUE` implementation, so at the end of the day it is just an implementation detail of the library.  
  
The above considerations make the helper function a better candidate for a library intended to be used in several different environments.

> Username: denzor200  
> Created_at: 2025-10-18 00:29:30 UTC  
> Url: https://github.com/boostorg/metaparse/pull/20#discussion_r2441477968  

Ok, I will remove deduction guide from `fixed_string` structure and then I will move `fixed_string.hpp` outside from cpp17 directory.  
  
> e.g. it makes it possible to remove the requirement from the test and you should also be able to just add the new test case to the existing string_value.cpp test file  
  
Negative. Remember that `convert_string` structure depends on [__cpp_nontype_template_parameter_auto](http://en.cppreference.com/w/cpp/experimental/feature_test.html#cpp_nontype_template_parameter_auto) C++17 feature. We can't do something with it

> Username: denzor200  
> Created_at: 2025-10-18 00:58:20 UTC  
> Url: https://github.com/boostorg/metaparse/pull/20#discussion_r2441493676  

Done

> Username: sabel83  
> Created_at: 2025-10-19 08:51:19 UTC  
> Url: https://github.com/boostorg/metaparse/pull/20#discussion_r2443034412  

Thank you


---
