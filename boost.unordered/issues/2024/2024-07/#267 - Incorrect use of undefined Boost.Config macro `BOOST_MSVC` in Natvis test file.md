# #267 - Incorrect use of undefined Boost.Config macro `BOOST_MSVC` in Natvis test file [Closed]

> Username: joaquintides  
> Created at: 2024-07-23 15:42:14 UTC  
> Updated at: 2024-07-24 07:05:23 UTC  
> Closed at: 2024-07-24 07:05:23 UTC  
> Url: https://github.com/boostorg/unordered/issues/267  

The following line:  
  
https://github.com/boostorg/unordered/blob/develop/test/natvis_tests.cpp#L9  
  
checks the existence of `BOOST_MSVC` when there's no possibility of it being defined --`<boost/config.hpp>` should be included before.
