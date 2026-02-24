# #29 - Use BOOST_CXX14_CONSTEXPR for currently non-constexpr functions [Closed]

> Username: tonyelewis  
> Created at: 2018-10-27 20:08:18 UTC  
> Updated at: 2018-11-01 19:49:54 UTC  
> Closed at: 2018-10-29 20:24:18 UTC  
> Url: https://github.com/boostorg/rational/issues/29  

Following on from [Trac#13099](https://svn.boost.org/trac10/ticket/13099), I would like to (re) propose adding more `constexpr` to rational. I think the easiest way is to liberally scatter `BOOST_CXX14_CONSTEXPR`, with the thought that any specific instances can be modified to `BOOST_CONSTEXPR` later where appropriate.  
  
I've submitted PR #28 to illustrate the sort of thing I mean. After that change, we can use code like:  
  
~~~cpp  
#include <boost/rational.hpp>  
  
constexpr ::boost::rational<int> a{ 1, 2 };  
constexpr auto b  = 3 * a;  
constexpr auto c1 = ::boost::rational<int>{ 1, 2 } += a;  
constexpr auto c2 = ::boost::rational<int>{ 1, 2 } -= a;  
constexpr auto c3 = ::boost::rational<int>{ 1, 2 } *= a;  
constexpr auto c4 = ::boost::rational<int>{ 1, 2 } /= a;  
constexpr auto d1 = b + a;  
constexpr auto d2 = b - a;  
constexpr auto d3 = b * a;  
constexpr auto d4 = b / a;  
constexpr auto e  = abs( b );  
constexpr bool f  = a < b;  
constexpr bool g  = a == a;  
constexpr bool h  = ++( ::boost::rational<int>{ 1, 2 } );  
constexpr bool i  =   ( ::boost::rational<int>{ 1, 2 } )++;  
constexpr bool j  = --( ::boost::rational<int>{ 1, 2 } );  
constexpr bool k  =   ( ::boost::rational<int>{ 1, 2 } )--;  
~~~  
  
Thanks very much.

---

## Comment 1

> Username: jeking3  
> Created at: 2018-10-29 20:24:18 UTC  
> Url: https://github.com/boostorg/rational/issues/29#issuecomment-434065329  

Tip: Next time, add "This fixes #29" somewhere in your description of the PR, and it will close Issue #29 when the pull request is merged.

---

## Comment 2

> Username: tonyelewis  
> Created at: 2018-11-01 19:49:54 UTC  
> Url: https://github.com/boostorg/rational/issues/29#issuecomment-435164228  

Thanks. Yes - I knew that really but forgot because I've been away from GitHub for a while.
