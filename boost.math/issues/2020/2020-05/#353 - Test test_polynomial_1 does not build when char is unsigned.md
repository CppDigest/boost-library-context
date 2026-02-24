# #353 - Test test_polynomial_1 does not build when char is unsigned [Closed]

> Username: trex58  
> Created at: 2020-05-15 13:30:27 UTC  
> Updated at: 2020-05-19 07:27:45 UTC  
> Closed at: 2020-05-19 07:27:26 UTC  
> Url: https://github.com/boostorg/math/issues/353  

On **Fedora/x86_64** , "char" type is signed by default. Thus compiling/running this program:  
```  
#include <stdio.h>  
int main() {  
        char c = -1;  
        printf("%3d 0x%hhx\n", c, c);  
}  
```  
with:       gcc char.c -o char ; ./char  
returns:   **-1** 0xff  
  
However, on **Fedora/PPC64LE** , "char" type is unsigned by default, and we get: **255** 0xff .  
And, since the test: test_polynomial_1 makes a complex use of char values, it does not build on Fedora/PPC64LE.  
With no change, there are several errors like the following about narrowing negative char values:  
```  
../libs/math/test/test_polynomial.cpp:392:24: error: narrowing conversion of ‘-5’ from ‘int’ to ‘char’ [-Wnarrowing]  
  392 |     boost::array<T, 4> tmp = {{8, -5, -4, 3}};  
      |                        ^  
```  
  
One could (badly) use **-Wno-narrowing** , however, that leads to 3 errors:  
```  
../libs/math/test/test_polynomial.cpp(394): error: in "test_addition<char>": check result == expected has failed [{  
, , ,  } != {, ▒, ▒,  }]  
../libs/math/test/test_polynomial.cpp(426): error: in "test_multiplication<char>": check aa == a_sq has failed [{ d, , , <, , ,          } != { d, ▒, ▒, l, ▒, ▒,    }]  
../libs/math/test/test_polynomial.cpp(479): error: in "test_self_multiply_assign<char>": check a == asq has failed [{ d, , , <, , ,      } != { d, , , l, , ,        }]  
```  
  
A better (temporary) solution is to use **-fsigned-char** . Then the test passes. However, we see several warnings dealing with comparison between (signed) "char" and "unsigned int":  
```  
../boost/test/tools/old/impl.hpp:107:17: warning: comparison of integer expressions of different signedness: ‘const char’ and ‘const long unsigned int’ [-Wsign-compare]  
  107 |     return left == right;  
      |            ~~~~~^~~~~~~~  
../boost/test/tools/old/impl.hpp:107:17: warning: comparison of integer expressions of different signedness: ‘const long unsigned int’ and ‘const int’  
../boost/test/tools/old/impl.hpp:107:17: warning: comparison of integer expressions of different signedness: ‘const short int’ and ‘const long unsigned int’  
../boost/test/tools/old/impl.hpp:107:17: warning: comparison of integer expressions of different signedness: ‘const int’ and ‘const long unsigned int’  
../boost/test/tools/old/impl.hpp:107:17: warning: comparison of integer expressions of different signedness: ‘const long int’ and ‘const long unsigned int’   
```  
Thus, as says GCC documentation: "a comparison between signed and unsigned values could produce an incorrect result when the signed value is converted to unsigned."  
  
Moreover, using -fsigned-char for building Boost code implies that issues can appear on Fedora/PPC64LE when mixing Boost object files with other object files or libraries compiled without -fsigned-char (thus: unsigned).  
  
Another solution is to use **static_cast<T>(NegativeValue)** everywhere where it is needed in libs/math/test/test_polynomial.cpp :  
```  
392:  boost::array<T, 4> tmp = {{8, static_cast<T>(-5), static_cast<T>(-4), 3}};  
416:  boost::array<T, 7> const d3a_sq = {{100, static_cast<T>(-120), static_cast<T>(-44), 108, static_cast<T>(-20), static_cast<T>(-24), 9}};  
```  
This solution works on Fedora/x86_64 when compiling with -funsigned-char , and the test succeeds. However, we still have the "comparison of integer expressions of different signedness" which may hide issues with other tests.  
  
However, this solution does not work on Fedora/PPC64LE, because converting a negative floating point number to an unsigned char (or integer) replaces it by 0 on Power machines with "recent" versions of GCC (at least: 8, 9, 10).  
This is due to line 312 of this GCC C++ code when _II type is "double" and _OI type is "char" , where the  **fctiwuz 0,0**  instruction is generated (fctiwuz: "if the rounded value is less than 0.0, then the result is 0x0000_0000 and ...") :    
```  
  /usr/include/c++/9/bits/stl_algobase.h :  
   304   template<bool _IsMove, bool _IsSimple, typename _Category>  
   305     struct __copy_move  
   306     {  
   307       template<typename _II, typename _OI>  
   308         static _OI  
   309         __copy_m(_II __first, _II __last, _OI __result)  
   310         {  
   311           for (; __first != __last; ++__result, (void)++__first)  
   312             *__result = *__first;  
   313           return __result;  
   314         }  
   315     };  
  
_ZNSt11__copy_moveILb0ELb0ESt26random_access_iterator_tagE8__copy_mIPKdPcEET0_T_S7_S6_ :    
...  
        lfd 0,0(9)  
        fctiwuz 0,0  
        mfvsrwz 9,0  
        rlwinm 10,9,0,0xff  
...  
```  
  
So, it seems to me that Boost does not correctly handle unsigned char cases on Fedora/PPC64LE.  
What are your suggestions?

---

## Comment 1

> Username: trex58  
> Created at: 2020-05-18 17:02:46 UTC  
> Url: https://github.com/boostorg/math/issues/353#issuecomment-630314817  

Since this code does work only when char is "signed char", I suggest to change the test as follows:  
```  
# diff ./libs/math/test/test_polynomial.cpp.ORIGIN ./libs/math/test/test_polynomial.cpp  
268c268  
< typedef boost::mpl::list<char, short, int, long> integral_test_types;  
---  
> typedef boost::mpl::list<signed char, short, int, long> integral_test_types;  
```  
so that "char" is replaced by "signed char".  
  
This way, this code clearly says that it works only when "char" is "signed char", and this test succeeds everywhere, whatever "char" is: "unsigned char" or "signed char" by default.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-05-19 07:27:45 UTC  
> Url: https://github.com/boostorg/math/issues/353#issuecomment-630638430  

Done.  Many thanks for tracking this down.
