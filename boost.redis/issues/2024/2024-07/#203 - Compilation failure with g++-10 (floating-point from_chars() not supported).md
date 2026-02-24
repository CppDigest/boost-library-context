# #203 - Compilation failure with g++-10 (floating-point from_chars() not supported) [Closed]

> Username: francoisk  
> Created at: 2024-07-15 12:05:30 UTC  
> Updated at: 2024-07-29 07:54:42 UTC  
> Closed at: 2024-07-29 07:54:42 UTC  
> Url: https://github.com/boostorg/redis/issues/203  

(See related issue #72.)  
  
Your documentation says Boost.Redis builds with g++ 10 but support for floating point `from_chars()` was only added in version 11 and indeed, I get the following error with g++ 10. Compilation succeeds with g++ 12.  
  
```  
In file included from include/boost/redis/adapter/detail/result_traits.hpp:13,  
                 from include/boost/redis/adapter/adapt.hpp:12,  
                 from include/boost/redis/detail/connection_base.hpp:10,  
                 from include/boost/redis/connection.hpp:10,  
                 from include/boost/redis.hpp:12,  
                 from tests/basics/driver.cpp:1:  
include/boost/redis/adapter/detail/adapters.hpp: In function ‘void boost::redis::adapter::detail::boost_redis_from_bulk(double&, std::string_view, boost::system::error_code&)’:  
include/boost/redis/adapter/detail/adapters.hpp:70:76: error: no matching function for call to ‘from_chars(std::basic_string_view<char>::const_pointer, std::basic_string_view<char>::const_pointer, double&)’  
   70 |    auto const res = std::from_chars(sv.data(), sv.data() + std::size(sv), d);  
      |                                                                            ^  
In file included from include/boost/redis/adapter/detail/adapters.hpp:29,  
                 from include/boost/redis/adapter/detail/result_traits.hpp:13,  
                 from include/boost/redis/adapter/adapt.hpp:12,  
                 from include/boost/redis/detail/connection_base.hpp:10,  
                 from include/boost/redis/connection.hpp:10,  
                 from include/boost/redis.hpp:12,  
                 from tests/basics/driver.cpp:1:  
/usr/include/c++/10/charconv:592:5: note: candidate: ‘template<class _Tp> std::__detail::__integer_from_chars_result_type<_Tp> std::from_chars(const char*, const char*, _Tp&, int)’  
  592 |     from_chars(const char* __first, const char* __last, _Tp& __value,  
      |     ^~~~~~~~~~  
```

---

## Comment 1

> Username: mzimbres  
> Created at: 2024-07-28 13:00:27 UTC  
> Updated at: 2024-07-28 13:00:51 UTC  
> Url: https://github.com/boostorg/redis/issues/203#issuecomment-2254511305  

In fact, CI is also not building for g++10, I will fix the documentation, thanks for reporting.
