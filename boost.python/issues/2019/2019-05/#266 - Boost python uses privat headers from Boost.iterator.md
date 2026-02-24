# #266 - Boost python uses privat headers from Boost.iterator [Open]

> Username: Mike-Devel  
> Created at: 2019-05-05 11:41:23 UTC  
> Updated at: 2019-05-05 11:41:42 UTC  
> Url: https://github.com/boostorg/python/issues/266  

Is there a particular reason, why `object_operators.hpp` includes enable_if and config_def/undef from iterator?:  
  
https://github.com/boostorg/python/blob/0a9b687e9ec653708bdfa5816730c3c3ec5872e3/include/boost/python/object_operators.hpp#L12  
  
I haven't tried to understand the code, but as far as I can tell, removing the includes and using `boost::enable_if` from boost core instead of `boost::iterators::enable_if` just works as expected (only tested with VS2017 in c++17 mode).
