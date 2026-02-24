# #368 - Compiling Boost with unsigned char makes several tests to fail [Closed]

> Username: trex58  
> Created at: 2020-05-11 21:26:13 UTC  
> Updated at: 2025-01-03 01:25:13 UTC  
> Closed at: 2020-07-28 16:07:10 UTC  
> Url: https://github.com/boostorg/math/issues/368  

### Actual behavior  
  
I've been unable to find any information about Boost being able to manage both signed char and unsigned char.  
My expectation is that Boost is able to manage both cases.  
  
On Fedora/x86_64, C/C++ char type is signed ( -127 to +127 ) by default.  
On other operating systems, like AIX, char is unsigned (0 .. 256) by default.  
  
On Fedora/x86_64, I've first compiled/tested Boost with no option, thus using (default) signed char.  
Then, I've rebuilt/retested Boost with **-funsigned-char**, and I've found that several tests are now failing (see below for the complete list).  
  
Here below, C means : failure at compilation time, and T means : failure at testing time .  
```  
   - math :   
       C test_polynomial.o             ../libs/math/test/test_polynomial.cpp:394:24: error: narrowing conversion of ‘-5’ from ‘int’ to ‘char’ [-Wnarrowing]  394 |     boost::array<T, 4> tmp = {{8, -5, -4, 3}};  
   - mpl :  
       C char.o...                      error: narrowing conversion of ‘-1’ from ‘int’ to ‘char’ [-Wnarrowing]  
       C integral_c.o                   error: narrowing conversion of ‘-1’ from ‘int’ to ‘char’ [-Wnarrowing]  
   - config :  
       T config_test_no_except.run...   Failed to value-initialize m_2d_char_array.  
       T config_test_no_rtti.run...     Failed to value-initialize m_2d_char_array.  
       T config_test.run...             Failed to value-initialize m_2d_char_array.  
       T config_test_threaded.run...    Failed to value-initialize m_2d_char_array.  
   - lambda :  
       T result_of_tests.run...         ../libs/lambda/test/result_of_tests.cpp(303): test apply1<int>(_1, one) == 1 failed  
   - lockfree :  
       T freelist_test.run...           freelist_test: ../libs/lockfree/test/freelist_test.cpp:37: dummy::dummy(): Assertion `allocated == 0' failed.  
   - process :  
       T async_pipe.run...              terminate called after throwing an instance of 'boost::process::process_errorterminate called recursively  
   - stacktrace :  
       T noop_terminate_handler_no_dbg.run...  terminate called after throwing an instance of 'boost::interprocess::interprocess_exception'  
       T noop_terminate_handler.run...         terminate called after throwing an instance of 'boost::interprocess::interprocess_exception'  
   - wave :  
       T testwave_dll.run...            (1 test(s) failed)  
       T testwave.run...                (1 test(s) failed)  
```  
  
About the **math:test_polynomial test**, it is easy to use casting like: static_cast<T>(-5) so that errors disappear. Then, the test works perfectly well on Fedora/x86_64. However, on AIX (unsigned char by default), a conversion from double to unsigned char breaks negative value (replacing them by: 0) due to the use of the Power "fctiwuz f0,f0" assembly instruction, and thus the test fails.  
  
### Expected  behavior  
  
All the above tests do succeed when not using unsigned char (-funsigned-char).  
  
### C++ Minimal Working Example  
  
See the above Boost tests.  
  
### Environment  
  
- OperatingSystem/Architecture: Fedora31 / x86_64  
- Compiler version: GCC 9.3.1 - 20200408  
- Build settings:  
- Version (Git ref or `<boost/version.hpp>`): 1.73.0

---

## Comment 1

> Username: ckormanyos  
> Created at: 2020-06-10 14:26:14 UTC  
> Updated at: 2020-06-10 14:27:20 UTC  
> Url: https://github.com/boostorg/math/issues/368#issuecomment-642045576  

Ugh...  
I suppose you probably would like to use default unsigned for char.  
But does your compiler also support `-fsigned-char`?  
And does the minimal example work with it?  
  
John will know better, but I suppose, that one would be taking a whole lotta enable_if to work around at compile time.

---

## Comment 2

> Username: trex58  
> Created at: 2020-06-10 15:29:51 UTC  
> Updated at: 2020-06-10 17:15:35 UTC  
> Url: https://github.com/boostorg/math/issues/368#issuecomment-642085749  

Hi  
  
The C/C++ standard says that the "char" type is "undefined". That means that a program cannot have any expectation about what will be done when using the "char" type. That is not the case for the "signed char" type.  
So, I think that either Boost should guarantee that its use of "char" type is OK on **all** platforms, or it should not use "char" but rather "signed char" everywhere it thinks that it needs signed character type, or the Boost community should try to build/test all Boost with -funsigned-char" on Linux/x86_64, as I did, and fix the issues that I listed above.  
  
Using "-fsigned-char" when compiling Boost does fix issues. However, Boost mainly delivers many .hpp files . Thus, a user of Boost on AIX will very probably not think to use "-fsigned-char" when compiling some code on AIX, since he probably is using his own tool and scripts for compiling his code (like cmake or b2) and he will not think about changing this.  
  
On PowerPC, converting negative floating point values to an unsigned char (and also to any unsigned integer) results in: 0 , since at least GCC v8. As an example, that breaks test math: test_polynomial_1.  
That's weird... apparently, however it follows some comment in the standard document. So, in some cases, that breaks the algorithm of Boost, not due to Boost code but due to Boost using some GCC C++ templates with "unsigned char" type. (Such issue is VERY difficult to find and understand since GCC compiler on Power adds an assembler instruction which does the trick).  
  
The code here below shows what happens in different environments. Please note that there are subtle differences between all cases.  
```  
# cat cast.c  
  
#include <stdio.h>  
  
int main()  
{  
       float f = -2;  
       double d = -2;  
       long double ld = -2;  
       char c;  
       unsigned char uc;  
       unsigned short us;  
       unsigned int ui;  
       unsigned long ul;  
  
       printf("             %5s %5s %5s %5s %5s\n", "c", "uc", "us", "ui", "ul");  
  
       c = (char)f;  
       uc = (unsigned char)f;  
       us = (unsigned short)f;  
       ui = (unsigned int)f;  
       ul = (unsigned long)f;  
       printf("float:       %5d %5d %5d %5d %5d\n", (int)c, (int)uc, (int)us, (int)ui, (int)ul);  
  
       c = (char)d;  
       uc = (unsigned char)d;  
       us = (unsigned short)d;  
       ui = (unsigned int)d;  
       ul = (unsigned long)d;  
       printf("double:      %5d %5d %5d %5d %5d\n", (int)c, (int)uc, (int)us, (int)ui, (int)ul);  
  
       c = (char)ld;  
       uc = (unsigned char)ld;  
       us = (unsigned short)ld;  
       ui = (unsigned int)ld;  
       ul = (unsigned long)ld;  
       printf("long double: %5d %5d %5d %5d %5d\n", (int)c, (int)uc, (int)us, (int)ui, (int)ul);  
  
       return 0;  
}  
  
Fedora/x86_64 : OK  
  
                 c    uc    us    ui    ul  
float:          -2   254 65534    -2    -2  
double:         -2   254 65534    -2    -2  
long double:    -2   254 65534    -2    -2  
  
Fedora/Power : KO  
  
                 c    uc    us    ui    ul  
float:           0     0     0     0     0  
double:          0     0     0     0     0  
long double:   254   254 65534    -2     0  
  
AIX GCC 6.3.0 : OK  
  
                 c    uc    us    ui    ul  
float:         254   254 65534    -2    -2  
double:        254   254 65534    -2    -2  
long double:   254   254 65534    -2    -2  
  
AIX GCC 8.4.0 & 9.3.0 32bit or 64bit : KO  
  
                 c    uc    us    ui    ul  
float:           0     0     0     0     0  
double:          0     0     0     0     0  
long double:     0     0     0     0     0  
```  
  
Look also at this example, which is close to what Boost does in some test:  
```  
# cat vector.cc  
#include <vector>  
#include <iostream>  
  
double const dbl[4] = { 10, -1, -2, 4 };  
  
int main()  
{  
       double d = -2;  
       char c = (char)d;  
  
       std::cout << (int)c << std::endl;  
  
       std::vector<char> v(&dbl[0], &dbl[4]);  
  
       for (auto it = begin (v); it != end (v); ++it) {  
               std::cout << (int)*it << std::endl;  
       }  
  
       return 0;  
}  
  
 g++ vector.cc -o vector ; ./vector  
  
AIX and Fedora/PPCL4LE:  
0  
10  
0  
0  
4  
  
Fedora/x86_64:  
-2  
10  
-1  
-2  
4  
```

---

## Comment 3

> Username: jzmaddock  
> Created at: 2020-06-10 17:23:30 UTC  
> Url: https://github.com/boostorg/math/issues/368#issuecomment-642149535  

Did this change to develop not fix this (for Boost.Math) ?  https://github.com/boostorg/math/commit/bfbd6a93ee47222bb59982e084c27e0090bf1a6e

---

## Comment 4

> Username: trex58  
> Created at: 2020-06-11 14:23:43 UTC  
> Url: https://github.com/boostorg/math/issues/368#issuecomment-642692006  

> Did this change to develop not fix this (for Boost.Math) ? [bfbd6a9](https://github.com/boostorg/math/commit/bfbd6a93ee47222bb59982e084c27e0090bf1a6e)  
  
It is exactly what I've done on my side already.  
With this change, I no more have a failure with math:test_polynomial test. :-) Good!

---

## Comment 5

> Username: trex58  
> Created at: 2020-06-11 14:24:59 UTC  
> Url: https://github.com/boostorg/math/issues/368#issuecomment-642692710  

So, based on the list I provided in first comment, there are only 13 remaining Boost tests which fail because probably using "char".

---

## Comment 6

> Username: jzmaddock  
> Created at: 2020-07-28 16:07:10 UTC  
> Url: https://github.com/boostorg/math/issues/368#issuecomment-665130336  

Closing down as this is now fixed as far as Boost.Math is concerned - if you haven't filed bug reports for the other libraries please do so.

---

## Comment 7

> Username: jeking3  
> Created at: 2025-01-03 01:24:45 UTC  
> Updated at: 2025-01-03 01:25:13 UTC  
> Url: https://github.com/boostorg/math/issues/368#issuecomment-2568585649  

This was in boostorg/mpl#50.  
It would be good to add a standard `-funsigned-char` to all CI jobs.
