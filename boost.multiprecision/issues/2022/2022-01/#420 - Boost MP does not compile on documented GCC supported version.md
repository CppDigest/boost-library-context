# #420 - Boost MP does not compile on documented GCC supported version [Closed]

> Username: gcflymoto  
> Created at: 2022-01-24 16:36:35 UTC  
> Updated at: 2022-01-25 09:23:55 UTC  
> Closed at: 2022-01-25 09:23:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/420  

Cross-filed with https://github.com/boostorg/boost/issues/610  
  
As documented on the [release notes](https://www.boost.org/users/history/version_1_78_0.html)  
  
"""  
Boost's primary test compilers are:  
  
Linux:  
...  
GCC, C++11: 4.7.3, 4.8.5, 11  
"""  
  
But this does play out in practice.. compiling with GCC4.7.3, GCC4.8.5 with -std=c++ fails. Should the release notes change?  
  
boost/1.78.0/boost/multiprecision/detail/number_base.hpp:36:2: error: #error "This library now requires a C++11 or later compiler - this message was generated as a result of BOOST_NO_CXX11_HDR_TYPE_TRAITS being set"  
  
/usr/bin/gcc/4.7.3/bin/g++ -c -std=c++11 -fPIC -DBOOST_PP_VARIADICS  test.cpp  
  
from test.cpp:13:  
/usr/boost/1.78.0/boost/multiprecision/detail/number_base.hpp:36:2: error: #error "This library now requires a C++11 or later compiler - this message was generated as a result of BOOST_NO_CXX11_HDR_TYPE_TRAITS being set"  
/usr/boost/1.78.0/boost/multiprecision/detail/number_base.hpp:48:2: error: #error "This library now requires a C++11 or later compiler - this message was generated as a result of BOOST_NO_CXX11_REF_QUALIFIERS being set"  
/usr/boost/1.78.0/boost/multiprecision/detail/number_base.hpp:72:2: error: #error "This library now requires a C++11 or later compiler - this message was generated as a result of BOOST_NO_CXX11_REF_QUALIFIERS being set"  
/usr/boost/1.78.0/boost/multiprecision/detail/number_base.hpp:92:0: warning: ignoring #pragma GCC warning [-Wunknown-pragmas]  
In file included from /usr/boost/1.78.0/boost/multiprecision/cpp_int.hpp:14:0,  
                 from /usr/boost/1.78.0/boost/multiprecision/integer.hpp:10,  
                 from test.cpp:13:  
  
The same happens with GCC 4.8.5  ... the lowest GCC that works for me is 5.3.0

---

## Comment 1

> Username: mborland  
> Created at: 2022-01-24 19:17:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/420#issuecomment-1020454051  

The [release notes for boost 1.76](https://www.boost.org/users/history/version_1_76_0.html) documented this. The announcement on the top of the GitHub readme also says `ANNOUNCEMENT: Support for C++03 is now removed from this library. Any attempt to build with a non C++11 conforming compiler is doomed to failure.`

---

## Comment 2

> Username: gcflymoto  
> Created at: 2022-01-24 19:19:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/420#issuecomment-1020456402  

Thanks for the info but the top-level boost release notes have not been updated to reflect this change. Hence I opened  
  
https://github.com/boostorg/boost/issues/610  
  
Should we just continue on that ticket instead?

---

## Comment 3

> Username: jzmaddock  
> Created at: 2022-01-25 09:23:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/420#issuecomment-1020976648  

> Should we just continue on that ticket instead?  
  
I believe so yes, we decided some time ago that C++03 was no longer a support requirement, and compilers pre-gcc-5 have only pre-C++11 support (they're good compilers, but things have moved on).  I suspect the top level docs are just stale.
