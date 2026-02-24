# #168 - BOOST_ASSERT() in gzip.hpp triggers clang warning -Wstring-conversion [Open]

> Username: pkl97  
> Created at: 2023-10-29 10:51:53 UTC  
> Updated at: 2023-10-29 10:51:53 UTC  
> Url: https://github.com/boostorg/iostreams/issues/168  

boost/iostreams/filter/gzip.hpp, line 551:  
`BOOST_ASSERT(!"Bad state");`  
  
This line triggers a -Wstring-conversion warning:  
  
> boost/iostreams/filter/gzip.hpp:551:31: warning: implicit conversion turns string literal into bool: 'const char[10]' to 'bool' [-Wstring-conversion]  
  
Would it be possible to use `BOOST_ASSERT_MSG(false,"Bad state");` instead?  
  
This would make it easier to enable the warning in projects that use Boost.IOStreams.  
  
Btw: Most Boost code uses `BOOST_ASSERT_MSG()` nowadays (240 hits), the use of `BOOST_ASSERT(!...)` is not widespread (only 9 hits).
