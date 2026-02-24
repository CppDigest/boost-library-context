# #159 - pmr::monotonic_buffer_resource crashes on large single allocations [Closed]

> Username: jmlundberg  
> Created at: 2020-08-27 21:47:06 UTC  
> Updated at: 2020-08-31 21:18:03 UTC  
> Closed at: 2020-08-31 21:18:03 UTC  
> Url: https://github.com/boostorg/container/issues/159  

```  
boost::container::pmr::monotonic_buffer_resource mr (boost::container::pmr::get_default_resource());  
boost::container::pmr::vector<char> vchar2(&r);  
const auto zz = 5368709127;  
std::cout << zz <<std::endl;  
vchar.resize(zz);  
```  
crashes at memset. windows, msvc, 64 bit.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-08-27 21:49:46 UTC  
> Updated at: 2020-08-27 21:50:05 UTC  
> Url: https://github.com/boostorg/container/issues/159#issuecomment-682208657  

The function `increase_next_buffer_at_least_to`  here:  
https://github.com/boostorg/container/blob/5a52472cd00994bf1752e92c1c178a5a822a36cb/src/monotonic_buffer_resource.cpp#L148  
  
Does not make the next buffer large enough to hold `bytes`, because the math function `floor_log2` it relies on is 32-bit and not 64-bit:  
https://github.com/boostorg/intrusive/blob/d8761780b0386c53ab144173a5fdc536ee2e1da8/include/boost/intrusive/detail/math.hpp#L79  
  
```  
   inline std::size_t floor_log2 (std::size_t x)  
   {  
      unsigned long log2;  
      BOOST_INTRUSIVE_BSR_INTRINSIC( &log2, (unsigned long)x );  
      return log2;  
   }  
```

---

## Comment 2

> Username: igaztanaga  
> Created at: 2020-08-31 21:18:03 UTC  
> Url: https://github.com/boostorg/container/issues/159#issuecomment-684045643  

Many thanks for the report. As Vinnie has correctly identified, it's a Boost.Intrusive problem, just fixed in boostorg/intrusive#52.
