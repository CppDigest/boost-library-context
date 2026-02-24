# #85 - BSD include guards [Closed]

> Username: alanbirtles  
> Created at: 2018-09-08 17:15:52 UTC  
> Updated at: 2019-01-14 06:21:53 UTC  
> Closed at: 2019-01-14 06:21:53 UTC  
> Url: https://github.com/boostorg/predef/issues/85  

Are the include guards in [os/bsd.h](../blob/develop/include/boost/predef/os/bsd.h) as intended? The sub headers are only included the second time you include the file?:  
  
```  
#ifndef BOOST_PREDEF_OS_BSD_H  
#define BOOST_PREDEF_OS_BSD_H  
  
   defined(BSD) || \  
    defined(_SYSTYPE_BSD) \  
    )  
#   undef BOOST_OS_BSD  
#   include <sys/param.h>  
#   if !defined(BOOST_OS_BSD) && defined(BSD4_4)  
#       define BOOST_OS_BSD BOOST_VERSION_NUMBER(4,4,0)  
#   endif  
#   if !defined(BOOST_OS_BSD) && defined(BSD4_3)  
#       define BOOST_OS_BSD BOOST_VERSION_NUMBER(4,3,0)  
#   endif  
#   if !defined(BOOST_OS_BSD) && defined(BSD4_2)  
#       define BOOST_OS_BSD BOOST_VERSION_NUMBER(4,2,0)  
#   endif  
#   if !defined(BOOST_OS_BSD) && defined(BSD)  
#       define BOOST_OS_BSD BOOST_PREDEF_MAKE_10_VVRR(BSD)  
#   endif  
#   if !defined(BOOST_OS_BSD)  
#       define BOOST_OS_BSD BOOST_VERSION_NUMBER_AVAILABLE  
#   endif  
#endif  
....  
  
#if BOOST_OS_BSD  
#   define BOOST_OS_BSD_AVAILABLE  
#   include <boost/predef/detail/os_detected.h>  
#endif  
  
#define BOOST_OS_BSD_NAME "BSD"  
  
#else  
  
#include <boost/predef/os/bsd/bsdi.h>  
#include <boost/predef/os/bsd/dragonfly.h>  
#include <boost/predef/os/bsd/free.h>  
#include <boost/predef/os/bsd/open.h>  
#include <boost/predef/os/bsd/net.h>  
  
#endif  
  
#include <boost/predef/detail/test.h>  
BOOST_PREDEF_DECLARE_TEST(BOOST_OS_BSD,BOOST_OS_BSD_NAME)  
  
```  
  
Apparently this confuses netbeans: https://stackoverflow.com/questions/37236721/unable-to-resolve-template-based-identifier-get-netbeans-8-1
