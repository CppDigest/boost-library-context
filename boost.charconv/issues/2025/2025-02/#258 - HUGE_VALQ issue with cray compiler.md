# #258 - HUGE_VALQ issue with cray compiler [Closed]

> Username: aminiussi  
> Created at: 2025-02-26 09:49:08 UTC  
> Updated at: 2025-02-26 18:13:46 UTC  
> Closed at: 2025-02-26 18:13:46 UTC  
> Url: https://github.com/boostorg/charconv/issues/258  

Hi  
  
The cmake quadmath detection code dectect quandmath availability with the de cray compiler:  
   
```  
10:40:20 [alainm@login3 rel]#crayCC --version  
Cray clang version 18.0.0  (0e4696aa65fa9549bd5e19c216678cc98185b0f7)  
Target: x86_64-unknown-linux-gnu  
Thread model: posix  
InstalledDir: /opt/cray/pe/cce/18.0.0/cce-clang/x86_64/share/../bin  
10:43:47 [alainm@login3 rel]#  
```  
```  
10:39:59 [alainm@login3 rel]#more  znort.cpp   
#include <quadmath.h>                                                                                                                                                
int main()                                                                                                                                                           
{                                                                                                                                                                    
    __float128 f = -2.0Q;                                                                                                                                            
    f = fabsq(f);                                                                                                                                                    
                                                                                                                                                                     
    return 0;                                                                                                                                                        
}     
10:40:08 [alainm@login3 rel]#CC ./znort.cpp   
10:40:12 [alainm@login3 rel]#./a.out   
10:40:20 [alainm@login3 rel]#  
```  
Unfortuinatly, charconv uses HUGE_VALQ, which is not as available as expected:  
```  
10:45:42 [alainm@login3 rel]#ninja -j 1 -k 1  
[0/2] Re-checking globbed directories...  
[1/4] Building CXX object libs/charconv/CMakeFiles/boost_charconv.dir/src/from_chars.cpp.o  
FAILED: libs/charconv/CMakeFiles/boost_charconv.dir/src/from_chars.cpp.o   
/opt/cray/pe/craype/2.7.32/bin/CC -DBOOST_CHARCONV_HAS_QUADMATH -DBOOST_CHARCONV_NO_LIB -DBOOST_CHARCONV_SOURCE -DBOOST_CHARCONV_STATIC_LINK -I/lus/work/CT4/cin7233/alainm/boost/libs/charconv/include -I/lus/work/CT4/cin7233/alainm/boost/libs/config/include -I/lus/work/CT4/cin7233/alainm/boost/libs/assert/include -I/lus/work/CT4/cin7233/alainm/boost/libs/core/include -I/lus/work/CT4/cin7233/alainm/boost/libs/static_assert/include -I/lus/work/CT4/cin7233/alainm/boost/libs/throw_exception/include -O3 -DNDEBUG -fvisibility=hidden -fvisibility-inlines-hidden -MD -MT libs/charconv/CMakeFiles/boost_charconv.dir/src/from_chars.cpp.o -MF libs/charconv/CMakeFiles/boost_charconv.dir/src/from_chars.cpp.o.d -o libs/charconv/CMakeFiles/boost_charconv.dir/src/from_chars.cpp.o -c /lus/work/CT4/cin7233/alainm/boost/libs/charconv/src/from_chars.cpp  
In file included from /lus/work/CT4/cin7233/alainm/boost/libs/charconv/src/from_chars.cpp:14:  
/lus/work/CT4/cin7233/alainm/boost/libs/charconv/src/float128_impl.hpp:146:28: error: use of undeclared identifier '__builtin_huge_valq'  
  146 |         return negative ? -HUGE_VALQ : HUGE_VALQ;  
      |                            ^  
/opt/cray/pe/cce/18.0.0/cce/x86_64/include/craylibs/quadmath.h:163:19: note: expanded from macro 'HUGE_VALQ'  
  163 | #define HUGE_VALQ __builtin_huge_valq()  
      |                    
```  
  
So that looks like a cray specific issue, but would it be worth checking for **HUGE_VALQ** availability in the quadmath detection test?  
As in:  
```  
    __float128 big = -HUGE_VALQ;                                                                                                                                            
```  
?  
  
thanks

---

## Comment 1

> Username: mborland  
> Created at: 2025-02-26 16:02:48 UTC  
> Url: https://github.com/boostorg/charconv/issues/258#issuecomment-2685506737  

Can you please see if PR #259 solves your issue? I added a check for `HUGE_VALQ` for both b2 and CMake.

---

## Comment 2

> Username: aminiussi  
> Created at: 2025-02-26 18:07:50 UTC  
> Url: https://github.com/boostorg/charconv/issues/258#issuecomment-2685820876  

> Can you please see if PR [#259](https://github.com/boostorg/charconv/pull/259) solves your issue? I added a check for `HUGE_VALQ` for both b2 and CMake.  
  
Yes, it works as expected.  
  
Thanks!

---

## Comment 3

> Username: mborland  
> Created at: 2025-02-26 18:13:33 UTC  
> Url: https://github.com/boostorg/charconv/issues/258#issuecomment-2685832630  

> > Can you please see if PR [#259](https://github.com/boostorg/charconv/pull/259) solves your issue? I added a check for `HUGE_VALQ` for both b2 and CMake.  
>   
> Yes, it works as expected.  
>   
> Thanks!  
  
Perfect. I'll merge to develop and master. It'll be in the 1.88 release in a few weeks.
