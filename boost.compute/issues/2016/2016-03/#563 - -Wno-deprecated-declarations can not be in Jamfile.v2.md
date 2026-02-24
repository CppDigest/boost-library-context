# #563 - -Wno-deprecated-declarations can not be in Jamfile.v2 [Closed]

> Username: jszuppe  
> Created at: 2016-03-06 22:05:17 UTC  
> Updated at: 2016-03-10 21:13:51 UTC  
> Closed at: 2016-03-10 21:13:45 UTC  
> Url: https://github.com/boostorg/compute/issues/563  

CXX option `-Wno-deprecated-declarations` can not be a requirement in Jamfile.v2 because this option is not recognizable by MSVC and some other compilers (`/wd4996` should be used for MSVC in this case).  
  
I would fix it myself but I'm not familiar with Boost.Build tool.  
  
http://www.boost.org/development/tests/develop/developer/output/oracle-intel-S2-12-4-stlport4-boost-bin-v2-libs-compute-test-test_accumulate-test-sun-12-4_stlport4-release-threading-multi.html  
  
http://www.boost.org/development/tests/develop/developer/output/teeks99-08c-win2012R2-64on64-boost-bin-v2-libs-compute-test-test_accumulate-test-msvc-10-0-dbg-adrs-mdl-64-thrd-mlt.html

---

## Comment 1

> Username: kylelutz  
> Created at: 2016-03-07 03:07:02 UTC  
> Url: https://github.com/boostorg/compute/issues/563#issuecomment-193071290  

Good catch, we should fix this. If I recall there is a way to include or exclude specific compilers which we could probably use for this, I'll see what I can find.

---

## Comment 2

> Username: jszuppe  
> Created at: 2016-03-07 08:48:54 UTC  
> Url: https://github.com/boostorg/compute/issues/563#issuecomment-193161635  

Everything is described here: https://svn.boost.org/trac/boost/wiki/Guidelines/WarningsGuidelines  
  
It's also possible to do this in code with:  
  
``` cpp  
#if defined(BOOST_MSVC)  
  #pragma warning(push)  
  #pragma warning(disable: 4996)  
#endif  
#include <cl.h> // and other standard OpenCL headers  
#if defined(BOOST_MSVC)  
  #pragma warning(pop)  
#endif  
```  
  
.. and accordingly for gcc and clang.
