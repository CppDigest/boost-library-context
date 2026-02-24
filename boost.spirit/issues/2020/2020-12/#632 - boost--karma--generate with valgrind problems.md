# #632 - boost::karma::generate with valgrind problems [Closed]

> Username: KordianD  
> Created at: 2020-12-21 15:34:01 UTC  
> Updated at: 2021-02-25 00:57:39 UTC  
> Closed at: 2021-02-25 00:57:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/632  

I have hard time understanding what going on here.  
This code works, but when I run it under valgrind it causes stack overflow problems.  
  
```  
#include <iostream>  
#include <limits>  
#include <boost/type_traits/is_float.hpp>  
#include <boost/type_traits/is_signed.hpp>  
#include <boost/type_traits/is_unsigned.hpp>  
#include <boost/utility/enable_if.hpp>  
#include <boost/spirit/include/karma.hpp>  
    
template<typename T>  
inline std::string ToString(const T& value, typename boost::enable_if<boost::is_arithmetic<T> >::type* = NULL,  
                                            typename boost::enable_if<boost::is_float<T> >::type* = NULL)  
{  
   std::string generated;  
   boost::spirit::karma::generate(std::back_insert_iterator<std::string>(generated),  
                                  boost::spirit::karma::real_generator<T, boost::spirit::karma::real_policies<T> >(),  
                                  value);  
   return generated;  
}  
   
int main() {  
   long double maxF128 = std::numeric_limits<long double>::max();  
  
   const std::string result = ToString(maxF128);  
   std::cout << result;  
}  
  
```  
  
``` ./main ```  
``` 1.19e4932 ```  
  
  
Under valgrind, long double seems to not be working.  
All other min, max value for ToString works under valgrind except long double.  
Long double is 128 on my platform. (compiler gcc 4.4.7)  
If I put small long double (123.456 for example, it works)  
  
``` valgrind ./main ```  
Valgrind output:  
  
````  
==9408== Memcheck, a memory error detector  
==9408== Copyright (C) 2002-2013, and GNU GPL'd, by Julian Seward et al.  
==9408== Using Valgrind-3.10.1 and LibVEX; rerun with -h for copyright info  
==9408== Command: ./main  
==9408==  
==9408== Stack overflow in thread 1: can't grow stack to 0xffe601fd8  
==9408==  
==9408== Process terminating with default action of signal 11 (SIGSEGV): dumping core  
==9408==  Access not within mapped region at address 0xFFE601FD8  
==9408==    at 0x513DD12: __kernel_standard (in /lib64/libm-2.12.so)  
==9408==  If you believe this happened as a result of a stack  
==9408==  overflow in your program's main thread (unlikely but  
==9408==  possible), you can try to increase the size of the  
==9408==  main thread stack using the --main-stacksize= flag.  
==9408==  The main thread stack size used in this run was 10485760.  
==9408== Stack overflow in thread 1: can't grow stack to 0xffe601fd0  
==9408==  
==9408== Process terminating with default action of signal 11 (SIGSEGV)  
==9408==  Access not within mapped region at address 0xFFE601FD0  
==9408==    at 0x4A23661: _vgnU_freeres (vg_preloaded.c:58)  
==9408==  If you believe this happened as a result of a stack  
==9408==  overflow in your program's main thread (unlikely but  
==9408==  possible), you can try to increase the size of the  
==9408==  main thread stack using the --main-stacksize= flag.  
==9408==  The main thread stack size used in this run was 10485760.  
==9408==  
==9408== HEAP SUMMARY:  
==9408==     in use at exit: 0 bytes in 0 blocks  
==9408==   total heap usage: 0 allocs, 0 frees, 0 bytes allocated  
==9408==  
==9408== All heap blocks were freed -- no leaks are possible  
==9408==  
==9408== For counts of detected and suppressed errors, rerun with: -v  
==9408== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 6 from 5)  
```

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-02-25 00:57:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/632#issuecomment-785496998  

I have reproduced the issue (using newer GCC/Valgrind), but there is nothing I could do about it, I could not even get an adequate backtrace. When I use Clang and libc++ everything is fine. Valgrind `long double` support is limited https://www.valgrind.org/docs/manual/manual-core.html so I suspect it somehow affects libstdc++ in a bad way. I suggest filing a bug to both, if you care about the issue, or just switch to AddressSanitizer/MemorySanitizer which is better than Valgrind in every aspect.
