# #18 Extract exception type name from std::exception_ptr on libstdc++ [Merged]

> Username: apolukhin  
> Created at: 2019-01-15 20:57:36 UTC  
> Updated at: 2019-03-03 06:10:11 UTC  
> Merged at: 2019-03-03 06:10:00 UTC  
> Closed at: 2019-03-03 06:10:00 UTC  
> Url: https://github.com/boostorg/exception/pull/18  

There's a public function https://github.com/gcc-mirror/gcc/blob/41d6b10e96a1de98e90a7c0378437c3255814b16/libstdc%2B%2B-v3/libsupc%2B%2B/exception_ptr.h#L153 that returns `std::type_info` of the exception.

---

## Comment 1

> Username: zajo  
> Created_at: 2019-03-03 06:10:11 UTC  
> Url: https://github.com/boostorg/exception/pull/18#issuecomment-468992669  

Thank you.

---
