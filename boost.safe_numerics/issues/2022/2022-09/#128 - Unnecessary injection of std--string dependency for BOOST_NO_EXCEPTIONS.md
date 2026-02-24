# #128 - Unnecessary injection of std::string dependency for BOOST_NO_EXCEPTIONS [Open]

> Username: jplcz  
> Created at: 2022-09-30 14:05:39 UTC  
> Updated at: 2022-09-30 14:08:35 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/128  

When using safe_numerics in embedded system (kernel mode code), where exceptions are disabled, uncessary dependency on std::string is created (std::exception and friends, as well). Currently I have patched this out locally via ``#ifdef BOOST_NO_EXCEPTIONS``, however maybe there might be better solution ?   
  
https://github.com/boostorg/safe_numerics/blob/13ca3d6dd36db1aac2d6b5caca2c281d15c881ad/include/boost/safe_numerics/exception.hpp#L118  
https://github.com/boostorg/safe_numerics/blob/13ca3d6dd36db1aac2d6b5caca2c281d15c881ad/include/boost/safe_numerics/exception.hpp#L190
