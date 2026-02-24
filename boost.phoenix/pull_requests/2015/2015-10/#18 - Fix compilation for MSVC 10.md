# #18 Fix compilation for MSVC 10 [Merged]

> Username: MarcelRaad  
> Created at: 2015-10-14 17:53:29 UTC  
> Updated at: 2016-07-25 05:00:02 UTC  
> Merged at: 2016-07-25 01:23:32 UTC  
> Closed at: 2016-07-25 01:23:32 UTC  
> Url: https://github.com/boostorg/phoenix/pull/18  

With the Dinkumware standard library shipped with Visual C++ 10, the unordered containers are defined in namespace std::tr1 and brought into namespace std via a using-declaration. This leads to compile errors when re-declaring the classes in namespace std.  
  
The errors are visible here:  
http://www.boost.org/development/tests/develop/developer/output/teeks99-08c-win2012R2-64on64-boost-bin-v2-libs-phoenix-test-querying_find-test-msvc-10-0-dbg-adrs-mdl-64-thrd-mlt.html

---

## Comment 1

> Username: Flast  
> Created_at: 2016-07-23 06:14:18 UTC  
> Url: https://github.com/boostorg/phoenix/pull/18#issuecomment-234702065  

The test is now passed (I don't know, why?). Is it still failed?

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2016-07-24 15:28:43 UTC  
> Url: https://github.com/boostorg/phoenix/pull/18#issuecomment-234783411  

The issue still exists, it just seems that the test now has a workaround for it (again).  
  
```  
#include <boost/phoenix/stl/algorithm/querying.hpp>  
#include <unordered_set>  
#include <unordered_map>  
```  
  
leads to:  
  
```  
1>c:\program files (x86)\microsoft visual studio 10.0\vc\include\unordered_set(513): error C2874: using-declaration causes a multiple declaration of 'std::tr1::unordered_multiset'  
1>          c:\program files (x86)\microsoft visual studio 10.0\vc\include\unordered_set(274) : see declaration of 'std::tr1::unordered_multiset'  
1>c:\program files (x86)\microsoft visual studio 10.0\vc\include\unordered_set(514): error C2874: using-declaration causes a multiple declaration of 'std::tr1::unordered_set'  
1>          c:\program files (x86)\microsoft visual studio 10.0\vc\include\unordered_set(60) : see declaration of 'std::tr1::unordered_set'  
1>c:\program files (x86)\microsoft visual studio 10.0\vc\include\unordered_map(589): error C2874: using-declaration causes a multiple declaration of 'std::tr1::unordered_map'  
1>          c:\program files (x86)\microsoft visual studio 10.0\vc\include\unordered_map(83) : see declaration of 'std::tr1::unordered_map'  
1>c:\program files (x86)\microsoft visual studio 10.0\vc\include\unordered_map(590): error C2874: using-declaration causes a multiple declaration of 'std::tr1::unordered_multimap'  
1>          c:\program files (x86)\microsoft visual studio 10.0\vc\include\unordered_map(342) : see declaration of 'std::tr1::unordered_multimap'  
```

---
