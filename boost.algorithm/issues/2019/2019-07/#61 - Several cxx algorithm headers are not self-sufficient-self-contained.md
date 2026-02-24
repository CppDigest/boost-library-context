# #61 - Several cxx algorithm headers are not self-sufficient/self-contained [Closed]

> Username: Dani-Hub  
> Created at: 2019-07-06 11:25:17 UTC  
> Updated at: 2019-12-04 01:08:39 UTC  
> Closed at: 2019-12-04 01:08:39 UTC  
> Url: https://github.com/boostorg/algorithm/issues/61  

The following program (with boost as being part of the include path) doesn't compile:  
```  
#include <boost/algorithm/cxx14/equal.hpp>  
int main() {}  
```  
and produces (depending on the compiler) a diagnostics like:  
```  
In file included from prog.cc:2:  
/opt/wandbox/boost-1.70.0/clang-head/include/boost/algorithm/cxx14/equal.hpp:23:9: error: unknown type name 'BOOST_CONSTEXPR'  
        BOOST_CONSTEXPR bool operator () ( const T1& v1, const T2& v2 ) const { return v1 == v2 ;}  
        ^  
/opt/wandbox/boost-1.70.0/clang-head/include/boost/algorithm/cxx14/equal.hpp:27:5: error: unknown type name 'BOOST_CXX14_CONSTEXPR'  
    BOOST_CXX14_CONSTEXPR  
    ^  
/opt/wandbox/boost-1.70.0/clang-head/include/boost/algorithm/cxx14/equal.hpp:44:5: error: unknown type name 'BOOST_CXX14_CONSTEXPR'  
    BOOST_CXX14_CONSTEXPR  
    ^  
/opt/wandbox/boost-1.70.0/clang-head/include/boost/algorithm/cxx14/equal.hpp:68:1: error: unknown type name 'BOOST_CXX14_CONSTEXPR'  
BOOST_CXX14_CONSTEXPR  
^  
/opt/wandbox/boost-1.70.0/clang-head/include/boost/algorithm/cxx14/equal.hpp:87:1: error: unknown type name 'BOOST_CXX14_CONSTEXPR'  
BOOST_CXX14_CONSTEXPR  
^  
5 errors generated.  
```  
The header "boost/algorithm/cxx14/equal.hpp" misses to include "boost/config.hpp".  
  
The following lists the headers that I found are obviously affected by a missing  
```  
#include <boost/config.hpp>  
```  
  
1. "boost/algorithm/cxx11/copy_n.hpp"  
2. "boost/algorithm/cxx14/equal.hpp"

---

## Comment 1

> Username: mclow  
> Created at: 2019-12-03 16:33:29 UTC  
> Url: https://github.com/boostorg/algorithm/issues/61#issuecomment-561248569  

Fixed in develop 9ef3df0

---

## Comment 2

> Username: mclow  
> Created at: 2019-12-04 01:08:39 UTC  
> Url: https://github.com/boostorg/algorithm/issues/61#issuecomment-561428766  

Merged to master for the 1.72.0 release.
