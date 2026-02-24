# #21 - Boost.proto (1.71) fails to compile with msvc 14.24 with conformance mode enabled. [Closed]

> Username: sergjoker  
> Created at: 2019-12-08 20:29:20 UTC  
> Updated at: 2019-12-26 22:29:28 UTC  
> Closed at: 2019-12-26 22:29:28 UTC  
> Url: https://github.com/boostorg/proto/issues/21  

With the latest update of Visual Studio (16.4) MSVC toolkit has updated to 14.24 .  
With this release they've improved(or at least changed)  two-phase lookup in conformance mode ( /permissive- option to cl.exe). With this option enabled simplest code with boost.proto fails to compile.  
Steps to reproduce:  
* Make file test.cpp containing single line ```#include <boost/proto/traits.hpp>```  
* Open Visual Studio Command Prompt.  
* complile it with command line   
```cl.exe /EHsc /permissive-   test.cpp /I <path_to_boost_root>```  
Compilation will fail with an errors:  
```  
boost/proto/generate.hpp(239): error C2039: 'value': is not a member of 'boost::proto'  
boost/proto/generate.hpp(32): note: see declaration of 'boost::proto'  
boost/proto/generate.hpp(239): note: This diagnostic occurred in the compiler generated function 'Extends<boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<MemberClass::* >,boost::proto::argsns_::term<MemberClass::* >::arity>> boost::proto::pod_generator<Extends>::operator ()(const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<MemberClass::* >,boost::proto::argsns_::term<MemberClass::* >::arity> &) const'  
...  
```  
The problem is caused by the following:  
1. boost/proto/traits.hpp includes boost/proto/generate.hpp  
1. boost/proto/traits.hpp defines functions ```boost::proto::value``` in it (later than generate.hpp is included  
1. boost/proto/generate.hpp has code at lines  233-251 which is specific to MSVC and uses functions boost::proto::value ( which are not defined at that point ).   
1. proto::value seems not to be a subject of second phase lookup and must be found at the first phase, thus has to be defined before  code using it.  
1. msvc 14.23 comiped it well, but feels like they've changed two-phase lookup in compiler frontend and now the code fails.  
1. The code is MSVC only that's why it didn't trigger problems on other compilers with proper two-phase lookup.

---

## Comment 1

> Username: sergjoker  
> Created at: 2019-12-26 22:29:28 UTC  
> Url: https://github.com/boostorg/proto/issues/21#issuecomment-569143316  

Looks like it's fixed in boost 1.72. Closing it.
