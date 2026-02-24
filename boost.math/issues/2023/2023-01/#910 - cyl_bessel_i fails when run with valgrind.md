# #910 - cyl_bessel_i fails when run with valgrind [Closed]

> Username: francescopt  
> Created at: 2023-01-02 16:23:43 UTC  
> Updated at: 2023-01-02 19:45:46 UTC  
> Closed at: 2023-01-02 19:45:46 UTC  
> Url: https://github.com/boostorg/math/issues/910  

The following program runs fails when run inside valgrind  
  
```  
#include <iostream>  
#include <boost/math/special_functions/bessel.hpp>  
  
int main()  
{  
  std::cout << boost::math::cyl_bessel_i(1.5, 0.4) << std::endl;  
  
  return 0;  
}  
```  
  
Output of valgrind:  
  
```  
==21292== Memcheck, a memory error detector  
==21292== Copyright (C) 2002-2022, and GNU GPL'd, by Julian Seward et al.  
==21292== Using Valgrind-3.19.0 and LibVEX; rerun with -h for copyright info  
==21292== Command: ./bessel  
==21292==   
-nan  
==21292==   
==21292== HEAP SUMMARY:  
==21292==     in use at exit: 0 bytes in 0 blocks  
==21292==   total heap usage: 2 allocs, 2 frees, 73,728 bytes allocated  
==21292==   
==21292== All heap blocks were freed -- no leaks are possible  
==21292==   
==21292== For lists of detected and suppressed errors, rerun with: -s  
==21292== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)  
```  
  
The test program is compiled with gcc 12.2.0 as  
  
```  
g++ -Wall -pedantic -std=c++17 -O3 -o bessel bessel.cpp  
```  
  
Valgrind version is 3.19.0.  
  
  
Without running the program inside valgrind, it gives the correct result:  
  
```  
0.0683662  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-01-02 19:45:46 UTC  
> Url: https://github.com/boostorg/math/issues/910#issuecomment-1369169189  

This is a known issue: internally we use long double precision arithmetic, and valgrind approximates this by delegating all long double std lib calls to their double precision equivalents.  In addition to a loss of precision, it breaks all our internal logic.  A trivial example would be `sqrt(std::numeric_limits<long double>::max())` which returns a NaN only when valgrind emulation is in effect.  
  
So I would argue that this is a known issue with valgrind, rather than our problem as such.
