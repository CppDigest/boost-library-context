# #672 - Boost.Geometry can not be used with xmlrpc [Closed]

> Username: steveire  
> Created at: 2020-02-29 18:20:49 UTC  
> Updated at: 2020-05-06 14:07:24 UTC  
> Closed at: 2020-02-29 19:33:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/672  

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

---

## Comment 1

> Username: mloskot  
> Created at: 2020-02-29 19:28:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/672#issuecomment-592985645  

@steveire This is annoying indeed, but doesn't the report belong to https://github.com/boostorg/qvm?  
  
```  
geometry (develop u= origin/develop) $ find . -name '*pp' | xargs grep -E '\bCR\b'  
geometry (develop u= origin/develop) $  
```  
  
```  
geometry (master u= origin/master) $ find . -name '*pp' | xargs grep -E '\bCR\b'  
geometry (master u= origin/master) $  
```

---

## Comment 2

> Username: awulkiew  
> Created at: 2020-02-29 21:15:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/672#issuecomment-592995322  

@steveire A workaround for this would be to include only these parts of Boost.Geometry that you need, without transform strategies which are the only part that use QVM.

---

## Comment 3

> Username: awulkiew  
> Created at: 2020-03-01 13:39:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/672#issuecomment-593098471  

@steveire  BTW, you should rather suggest changing the macro name in xmlrpc to something like `XMLRPC_CR`.

---

## Comment 4

> Username: steveire  
> Created at: 2020-03-01 13:54:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/672#issuecomment-593100014  

xmlrpc is already fixed actually. It's just that the ubuntu package is out of date: https://bugs.launchpad.net/ubuntu/+source/xmlrpc-c/+bug/1865344
