# #23 - Boost.Geometry can not be used with xmlrpc [Closed]

> Username: steveire  
> Created at: 2020-02-29 19:33:24 UTC  
> Updated at: 2020-03-01 00:02:18 UTC  
> Closed at: 2020-03-01 00:02:17 UTC  
> Url: https://github.com/boostorg/qvm/issues/23  

REF: http://xmlrpc-c.sourceforge.net/  
  
```  
#include <xmlrpc-c/abyss.h>  
#include <boost/geometry.hpp>  
```  
  
Output:  
  
```  
In file included from test.cpp:3:  
In file included from /usr/include/boost/geometry.hpp:17:  
In file included from /usr/include/boost/geometry/geometry.hpp:50:  
In file included from /usr/include/boost/geometry/strategies/strategies.hpp:106:  
In file included from /usr/include/boost/geometry/strategies/transform/matrix_transformers.hpp:27:  
In file included from /usr/include/boost/qvm/mat_operations.hpp:10:  
In file included from /usr/include/boost/qvm/mat_operations2.hpp:6:  
/usr/include/boost/qvm/gen/mat_operations2.hpp:1571:33: error: expected ',' or '>' in template-parameter-list  
            template <int R,int CR,int C>  
                                ^  
/usr/include/xmlrpc-c/abyss.h:601:17: note: expanded from macro 'CR'  
#define CR      '\r'  
                ^  
```  
  
  
"moved" from https://github.com/boostorg/geometry/issues/672

---

## Comment 1

> Username: zajo  
> Created at: 2020-03-01 00:02:17 UTC  
> Url: https://github.com/boostorg/qvm/issues/23#issuecomment-593007797  

Fixed, https://github.com/boostorg/qvm/commit/ffb434f1fad62d2bd2e50d23afbb960ffdab573f, thanks.
