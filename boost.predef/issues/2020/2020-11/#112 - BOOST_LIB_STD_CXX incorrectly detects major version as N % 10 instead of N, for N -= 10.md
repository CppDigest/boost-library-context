# #112 - BOOST_LIB_STD_CXX incorrectly detects major version as N % 10 instead of N, for N >= 10 [Closed]

> Username: mkurdej  
> Created at: 2020-11-16 12:59:49 UTC  
> Updated at: 2021-01-04 04:52:33 UTC  
> Closed at: 2021-01-04 04:52:33 UTC  
> Url: https://github.com/boostorg/predef/issues/112  

Probably `BOOST_PREDEF_MAKE_10_VPPP` should not divide modulo 10.  
  
`BOOST_LIB_STD_CXX` uses `BOOST_PREDEF_MAKE_10_VPPP`.  
However, newer versions of libc++ have `_LIBCPP_VERSION` equal to e.g. `10000`.  
So the major version gets the value of 0 (10 % 10).  
  
The fix seems to be straightforward:  
`#define BOOST_PREDEF_MAKE_10_VPPP(V) BOOST_VERSION_NUMBER((V)/1000,0,(V)%1000)`  
instead of  
`#define BOOST_PREDEF_MAKE_10_VPPP(V) BOOST_VERSION_NUMBER(((V)/1000)%10,0,(V)%1000)`.  
  
Or use a different macro instead of BOOST_PREDEF_MAKE_10_VPPP.
