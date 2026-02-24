# #1188 - LGPL license hits for GMP and MPFR [Closed]

> Username: jhbeskow  
> Created at: 2024-08-28 16:40:51 UTC  
> Updated at: 2024-08-30 13:47:26 UTC  
> Closed at: 2024-08-30 13:47:25 UTC  
> Url: https://github.com/boostorg/math/issues/1188  

We are receiving LGPL licensing compliance hits due to doc/constants/constants.qbk.  The specific hit comes from this paragraph.  
  
"[@http://gmplib.org GMP] and [@http://www.mpfr.org/ MPFR] have also been used to compute constants,  
but are licensed under the [@http://www.gnu.org/copyleft/lesser.html Lesser GPL license]  
and are [*not licensed for commercial use]."  
  
Can you speak to your usage of GMP and MPFR and how you are compliant with LGPL, which both are released under?  Are you dynamically linking to these libraries or statically linking to them?  My apologies, C and C++ are not my forte.  I do see that the header files are being pulled in.

---

## Comment 1

> Username: mborland  
> Created at: 2024-08-29 17:18:43 UTC  
> Url: https://github.com/boostorg/math/issues/1188#issuecomment-2318424648  

From the point of view of an end user, we do not use GMP or MPFR, and nothing we ship or that the user builds from our code will link to those libraries. However, we do have various maintenance programs that use high-precision arithmetic to generate test values and numeric constants. These are purely for the use of Boost developers and are not built when the user builds or installs the library. The paragraph you refer to in the documentation is probably poorly worded and, frankly, could just be deleted.  
  
CC: @jzmaddock and @ckormanyos

---

## Comment 2

> Username: jhbeskow  
> Created at: 2024-08-30 13:47:25 UTC  
> Url: https://github.com/boostorg/math/issues/1188#issuecomment-2321318362  

Excellent!  That is great information.  Thank you!
