# #44 - Error on gcc 10  with -std=c++20 [Closed]

> Username: danielgavrila  
> Created at: 2020-05-16 19:53:31 UTC  
> Updated at: 2020-07-07 07:25:51 UTC  
> Closed at: 2020-07-07 07:25:25 UTC  
> Url: https://github.com/boostorg/pfr/issues/44  

/usr/local/ssd/projects/Labor/cpp20/magic/include/boost/pfr/detail/fields_count.hpp:233:53: error: static assertion failed: ====================>   
Boost.PFR: Types with user specified constructors (non-aggregate initializable types) are not supported.  
/usr/usr/local/ssd/projects/Labor/cpp20/magic/include/boost/pfr/detail/fields_count.hpp:233:53: error: static assertion failed: ====================>   
Hello,  
  
Just tried to compile the examples.cpp with gcc 10 with -std=c++20 flag enabled and got an static_assert error (see below). Removing the static_assert the samples is compiling.  
  
Best regards,  
Daniel  
  
Boost.PFR: Types with user specified constructors (non-aggregate initializable types) are not supported.  
/usr/local/ssd/projects/Labor/cpp20/magic/include/boost/pfr/detail/fields_count.hpp: In instantiation of ‘constexpr std::size_t boost::pfr::detail  
::fields_count() [with T = my_struct_nested; std::size_t = long unsigned int]’:  
/usr/local/ssd/projects/Labor/cpp20/magic/include/boost/pfr/detail/core14_loophole.hpp:158:67:   required from ‘auto boost::pfr::detail::tie_as_tu  
/local/ssd/projects/Labor/cpp20/magic/include/boost/pfr/detail/fields_count.hpp: In instantiation of ‘constexpr std::size_t boost::pfr::detail  
::fields_count() [with T = my_struct_nested; std::size_t = long unsigned int]’:  
/usr/local/ssd/projects/Labor/cpp20/magic/include/boost/pfr/detail/core14_loophole.hpp:158:67:   required from ‘auto boost::pfr::detail::tie_as_tu

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-07-07 07:25:24 UTC  
> Updated at: 2020-07-07 07:25:51 UTC  
> Url: https://github.com/boostorg/pfr/issues/44#issuecomment-654653737  

Fixed in develop and master (even made a new release https://github.com/apolukhin/magic_get/releases/tag/1.0.1 )  
  
Many thanks for the bug report!
