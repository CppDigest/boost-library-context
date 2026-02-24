# #52 - Invalid casting in BOOST_INTRUSIVE_BSR_INTRINSIC [Closed]

> Username: igaztanaga  
> Created at: 2020-08-31 21:01:39 UTC  
> Updated at: 2020-08-31 21:15:27 UTC  
> Closed at: 2020-08-31 21:15:27 UTC  
> Url: https://github.com/boostorg/intrusive/issues/52  

Discovered with Boost.Container bug: 159 (https://github.com/boostorg/container/issues/159).  
  
In https://github.com/boostorg/intrusive/blob/d8761780b0386c53ab144173a5fdc536ee2e1da8/include/boost/intrusive/detail/math.hpp#L79, function floor_log2, there is a bad cast in the second argument that truncates 64 bit values:  
  
BOOST_INTRUSIVE_BSR_INTRINSIC( &log2, (unsigned long)x );  
  
it should be simply:  
  
BOOST_INTRUSIVE_BSR_INTRINSIC( &log2, x );  
  
as BOOST_INTRUSIVE_BSR_INTRINSIC will be _BitScanReverse64 in 64 bit builds, which takes unsigned __int64 as argument.
