# #1278 - cyl_bessel_j returns nan for large arguments on gcc [Open]

> Username: matwey  
> Created at: 2025-06-20 13:30:57 UTC  
> Updated at: 2025-06-20 19:28:28 UTC  
> Url: https://github.com/boostorg/math/issues/1278  

Hello, I am running gcc 14.2.1 (gcc 7 and 13 behaves consistently) and boost 1.87.  
  
I've found that the following code returns `nan` when compiled with `-O3 -funsafe-math-optimizations`:  
```c++  
using value_type = double;  
  
value_type x = 0x1.0p64;  
  
using namespace boost::math;  
  
std::cerr << cyl_bessel_j(0, x) << std::endl;  
std::cerr << cyl_bessel_j(1, x) << std::endl;  
std::cerr << cyl_bessel_j(2, x) << std::endl;  
```  
  
When I use clang with the same arguments, the result seems to be correct.

---

## Comment 1

> Username: mborland  
> Created at: 2025-06-20 14:22:45 UTC  
> Url: https://github.com/boostorg/math/issues/1278#issuecomment-2991819346  

Does this work on GCC without unsafe math? That flag combined with excess precision as default since GCC13 is probably doing something nefarious.

---

## Comment 2

> Username: matwey  
> Created at: 2025-06-20 14:43:18 UTC  
> Url: https://github.com/boostorg/math/issues/1278#issuecomment-2991893922  

Yes, gcc with `-O3 -fnounsafe-math-optimizations` is consistent with clang

---

## Comment 3

> Username: jzmaddock  
> Created at: 2025-06-20 16:25:37 UTC  
> Url: https://github.com/boostorg/math/issues/1278#issuecomment-2992194065  

I think you get what you ask for here - unsafe optimizations!  
  
I can reproduce with gcc15/mingw but my gdb assembly level debugging skills are pretty limited.  Should you wish to try and track this down the code for the asymptotic case is pretty simple, but I'm guessing one of the std lib functions is returning a nan here...

---

## Comment 4

> Username: matwey  
> Created at: 2025-06-20 16:38:51 UTC  
> Url: https://github.com/boostorg/math/issues/1278#issuecomment-2992221724  

> Should you wish to try and track this down the code for the asymptotic case is pretty simple, but I'm guessing one of the std lib functions is returning a nan here...  
  
Using `BOOST_MATH_INSTRUMENT_VARIABLE` I tracked that `phase` variable in `detail::asymptotic_bessel_j_large_x_2` is `nan`.  
However, `detail::asymptotic_bessel_phase_mx` called on its own works fine. So, the issue is appeared after some inlining I guess.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2025-06-20 17:23:34 UTC  
> Url: https://github.com/boostorg/math/issues/1278#issuecomment-2992331764  

Weird.  There's nothing but basic arithmetic in there, and the largest term goes to approx 1e139 which is well within `double` range.

---

## Comment 6

> Username: matwey  
> Created at: 2025-06-20 19:28:28 UTC  
> Url: https://github.com/boostorg/math/issues/1278#issuecomment-2992603977  

I guess that something like `inf/inf` appears here. I will try to provide more information.
