# #8 - CMake code doesn't search for Boost [Closed]

> Username: mgaunard  
> Created at: 2014-09-16 14:16:23 UTC  
> Updated at: 2014-09-17 00:12:08 UTC  
> Closed at: 2014-09-17 00:12:08 UTC  
> Url: https://github.com/boostorg/hana/issues/8  

Hana uses Boost without searching for it in its CMake code.

---

## Comment 1

> Username: mgaunard  
> Created at: 2014-09-16 14:30:08 UTC  
> Url: https://github.com/boostorg/hana/issues/8#issuecomment-55751209  

The code actually searches for Boost, but it's not using it everywhere that is needed.

---

## Comment 2

> Username: ldionne  
> Created at: 2014-09-16 15:25:37 UTC  
> Url: https://github.com/boostorg/hana/issues/8#issuecomment-55760402  

Can you be more specific? Where does it uses Boost without making sure that it is available?

---

## Comment 3

> Username: mgaunard  
> Created at: 2014-09-16 15:37:06 UTC  
> Updated at: 2014-09-16 15:38:03 UTC  
> Url: https://github.com/boostorg/hana/issues/8#issuecomment-55762314  

```  
FAILED: /home/mgaunard/build/llvm/Release/bin/clang++    -I../include    -W -Wall -Wextra -Wno-long-long -Wno-unused-local-typedefs -Wno-unused-parameter -Wwrite-strings -pedantic -std=c++1y -stdlib=libc++ -MMD -MT example/CMakeFiles/example.ext.boost.mpl.integral_c.constant.dir/ext/boost/mpl/integral_c/constant.cpp.o -MF example/CMakeFiles/example.ext.boost.mpl.integral_c.constant.dir/ext/boost/mpl/integral_c/constant.cpp.o.d -o example/CMakeFiles/example.ext.boost.mpl.integral_c.constant.dir/ext/boost/mpl/integral_c/constant.cpp.o -c ../example/ext/boost/mpl/integral_c/constant.cpp  
In file included from ../example/ext/boost/mpl/integral_c/constant.cpp:8:  
../include/boost/hana/ext/boost/mpl/integral_c.hpp:18:10: fatal error: 'boost/mpl/integral_c.hpp' file not found  
#include <boost/mpl/integral_c.hpp>  
         ^  
1 error generated.  
```  
  
example.ext.boost.mpl.integral_c.constant target

---

## Comment 4

> Username: ldionne  
> Created at: 2014-09-16 16:26:14 UTC  
> Url: https://github.com/boostorg/hana/issues/8#issuecomment-55770296  

Here's what happens: While I search for Boost and remove targets that depend on it when it's not available, I never actually add the `Boost_INCLUDE_DIRS` to the include paths of these targets. Hence, if Boost is available, the targets are available too but the compiler will fail to find the headers. It did not matter on my machine because the compiler already looked at /usr/local. I'm working on a fix.  
  
Thanks for reporting.
