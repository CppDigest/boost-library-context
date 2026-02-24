# #978 C++23 Floats [Merged]

> Username: mborland  
> Created at: 2023-04-24 13:11:39 UTC  
> Updated at: 2023-06-28 06:02:29 UTC  
> Merged at: 2023-06-28 06:02:25 UTC  
> Closed at: 2023-06-28 06:02:25 UTC  
> Url: https://github.com/boostorg/math/pull/978  

A lot more explicit casting is required now because implicit conversions from promoted types or a higher rank suffix (e.g. 0.3L on an F64) are errors.  
  
Questions:  
- In promotion I prioritize the fixed width type over the standard type (e.g. F64 before double). Is this how we want to proceed?  
- How much do we want to support [bfloat16_t](https://en.wikipedia.org/wiki/Bfloat16_floating-point_format)?  
  
[Here](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2022/p1467r9.html) is the paper for easy reference.  
  
@jzmaddock, @ckormanyos, @NAThompson

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2023-04-24 16:27:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/978#pullrequestreview-1398406152  

📁 include/boost/math/tools/promotion.hpp

```diff
  99 |+ #endif 
 100 |+ #ifdef __STDCPP_FLOAT128_T__
 101 |+            typename std::conditional<std::is_same<std::float128_t, T1P>::value || std::is_same<std::float128_t, T2P>::value, // either long double?
```

> Username: jzmaddock  
> Created_at: 2023-04-24 16:27:31 UTC  
> Url: https://github.com/boostorg/math/pull/978#discussion_r1175530001  

I wonder if now that we have `std::common_type`, whether we should basically just rely on that here?  
  
It simplifies the code, but also, it handles the case where double or long double do not have the sizes we expect.

> Username: mborland  
> Created_at: 2023-04-24 16:48:57 UTC  
> Updated_at: 2023-04-24 16:49:05 UTC  
> Url: https://github.com/boostorg/math/pull/978#discussion_r1175552937  

I could try it. Since `std::common_type` is for implicit conversion I wonder if it will work with these types since they require explicit conversions?

> Username: mborland  
> Created_at: 2023-04-25 10:07:31 UTC  
> Url: https://github.com/boostorg/math/pull/978#discussion_r1176299390  

Unfortunately `std::common_type_t` did not work.


---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2023-04-24 16:28:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/978#pullrequestreview-1398408284  

📁 include/boost/math/tools/promotion.hpp

```diff
 186 |+       template <> struct promote_args_2<std::float64_t, double> { using type = std::float64_t; };
 187 |+       template <> struct promote_args_2<double, std::float64_t> { using type = std::float64_t; };
 188 |+       template <> struct promote_args_2<std::float64_t, long double> { using type = long double; };
```

> Username: jzmaddock  
> Created_at: 2023-04-24 16:28:52 UTC  
> Updated_at: 2023-04-24 16:28:53 UTC  
> Url: https://github.com/boostorg/math/pull/978#discussion_r1175531447  

I think this may be wrong, as `long double` may be the same size and representation as `float64_t` in which case we should prefer that?


---

## Comment 3

> Username: jzmaddock  
> Created_at: 2023-04-24 16:36:13 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1520494196  

I think you're correct to prefer the new named types given a tie: this is what the std specifies.  
  
Question: what do we want to do about `float16_t`?  It's an interchange format, and not normally a computation format.  Do we really want to perform complex calculations on such a type given that the result may be meaningless?  Any error bigger than 10^-4 will wipe out all the digits, and any result larger than 65519 will be +INF.  On the other hand, the user has explicitly asked for that type, and we may be on hardware with specialised support for it!

---

## Comment 4

> Username: mborland  
> Created_at: 2023-04-24 16:53:09 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1520515585  

> I think you're correct to prefer the new named types given a tie: this is what the std specifies.  
>   
> Question: what do we want to do about `float16_t`? It's an interchange format, and not normally a computation format. Do we really want to perform complex calculations on such a type given that the result may be meaningless? Any error bigger than 10^-4 will wipe out all the digits, and any result larger than 65519 will be +INF. On the other hand, the user has explicitly asked for that type, and we may be on hardware with specialised support for it!  
  
That was my concern with `bfloat16_t`. It has a larger range but a mere 8 bits of precision. `blfloat` was designed for ML where getting the order of magnitude correct is what matters. I think we can make most everything work for the 16 bit types but offer no real guarantees of accuracy.

---

## Comment 5

> Username: mborland  
> Created_at: 2023-04-26 11:14:33 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1523243973  

Now that promotion is agreeable with the current CI system how much testing do we want to add @jzmaddock? Adding `float32_t` and `float64_t` to everything would add significant strain to the runners that support it. Maybe test either `float` and `double` or `float32_t` and `float64_t`, but not both? [GCC 13.1 was released this morning](https://gcc.gnu.org/pipermail/gcc/2023-April/241196.html) so hopefully we see it in Ubuntu 22.04 repos soon.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2023-04-27 10:39:44 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1525438174  

That's a good question, this explosion of types presents us with a problem!  
  
I don't see any point in running say special function accuracy tests with *both* say a 64-bit `double` and `float64_t`.  
  
I'm also not convinced that it's worth running any tests with `float16_t` or `bfloat16_t`, these are probably "use at your own risk" on account of their limited range and precision?  
  
Now that the tests *should hopefully* be 128-bit float clean, we could start running accuracy tests for `float128_t` when `long double` is not 128-bit.  
  
What we should also do at a minimum, is instantiate the concept-checks for these new types, so something like:  
  
```  
#define TEST_COMPLEX  
  
#include "compile_test/instantiate.hpp"  
  
int main(int argc, char* [])  
{  
   if(argc > 10000)  
   {  
      instantiate(float16_t(0));  
      instantiate_mixed(float16_t(0));  
   }  
}  
```  
  
For each of these new types and see what fails.  That would take care of the distributions and special functions, but not things like the integrators etc.  
  
Once there are Mingw gcc-13 builds available I'll be able to help out a bit more, nothing yet though.  No sign of msvc supporting these types just yet either :(

---

## Comment 7

> Username: mborland  
> Created_at: 2023-04-27 14:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1525780720  

That sounds reasonable to me. I think adding a disclaimer to the docs about the 16-bit types is about as far as we should go with it. I doubt MSVC rolls out support anytime soon since support is optional.

---

## Comment 8

> Username: mborland  
> Created_at: 2023-05-02 11:51:16 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1531334738  

GCC 13.1 is now in the homebrew repos on Mac.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2023-05-13 18:14:52 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1546723698  

@mborland : hope you don't mind, but I pushed some fairly trivial fixes for MInw64.  
  
Question: do we care about the blizzard of warnings generated by float32.cpp?  And should we add float128.cpp?

---

## Comment 10

> Username: mborland  
> Created_at: 2023-05-15 10:26:48 UTC  
> Updated_at: 2023-05-15 10:29:27 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1547597610  

> @mborland : hope you don't mind, but I pushed some fairly trivial fixes for MInw64.  
>   
> Question: do we care about the blizzard of warnings generated by float32.cpp? And should we add float128.cpp?  
  
Feel free to make changes; I leave the branch open to maintainers so the you can. I hit a lot of errors when attempting float128.cpp. I think it is an alias to `__float128` because there were a lot of complaints about the stream operators missing.  
  
I think for the float32 warnings I would ignore them for now to get things working and then we'll have to go back more methodically. Any time a constant is a double converted to float32 you are going to get a warning. There are definitely going to be lots of instances of that throughout the codebase.

---

## Comment 11

> Username: mborland  
> Created_at: 2023-05-15 18:46:11 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1548375095  

@jzmaddock Drone now has an image for Ubuntu 23.04. I have it cycling, but it looks like we have a good runner for g++13.

---

## Comment 12

> Username: mborland  
> Created_at: 2023-05-16 06:10:00 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1549048641  

Only one failure from the new floats occurs in quadrature:  
  
```  
====== BEGIN OUTPUT ======  
Running 1 test case...  
Default hardware concurrency = 12  
Testing that finite singular boundaries work on naive Monte-Carlo integration on type _Float64  
unknown location(0): fatal error: in "naive_monte_carlo_test": memory access violation at address: 0x7ffef427eff8: no mapping at fault address  
naive_monte_carlo_test.cpp(455): last checkpoint: "naive_monte_carlo_test" test entry  
  
*** 1 failure is detected in the test module "naive_monte_carlo_test"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
```  
  
@NAThompson

---

## Comment 13

> Username: mborland  
> Created_at: 2023-05-16 13:05:24 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1549630840  

@jzmaddock `std::float128_t` testing has been added. GCC's `<charconv>` has an overload for `std::float128_t` so I use `to_chars` rather than `boost::lexical_cast` since there is no streaming operator.

---

## Comment 14

> Username: NAThompson  
> Created_at: 2023-05-16 13:54:52 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1549721768  

@mborland : Mind creating a separate issue and I'll take a look?

---

## Comment 15

> Username: mborland  
> Created_at: 2023-05-16 13:57:46 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1549729732  

> @mborland : Mind creating a separate issue and I'll take a look?  
  
My fix is here: https://github.com/boostorg/math/pull/978/commits/d217813b03a41818460ff0be8f66b108aa880e27. It looks like the conversions in `<cmath>` functions failed. I also replaced comparisons to infinity since Intel likes to complain about and/or replace them with `false`.

---

## Comment 16

> Username: mborland  
> Created_at: 2023-05-17 06:42:40 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1550831516  

A bug has been filed with GCC about the stack overflows: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=109883

---

## Comment 17

> Username: mborland  
> Created_at: 2023-05-17 16:41:46 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1551734687  

`daubechies_scaling_test` does not seem to like `_Float32`  
  
```  
====== BEGIN OUTPUT ======  
Testing 2 vanishing moment scaling function quadratures on type _Float32  
Scaling functor size is 50331704 bytes  
Testing 2 vanishing moment scaling function quadratures on type _Float64  
Scaling functor size is 100663368 bytes  
Testing 3 vanishing moment scaling function quadratures on type _Float32  
Scaling functor size is 83886136 bytes  
Testing 3 vanishing moment scaling function quadratures on type _Float64  
Scaling functor size is 167772232 bytes  
Testing 4 vanishing moment scaling function quadratures on type _Float32  
Scaling functor size is 117440568 bytes  
Testing 4 vanishing moment scaling function quadratures on type _Float64  
Scaling functor size is 234881096 bytes  
Testing 5 vanishing moment scaling function quadratures on type _Float32  
Scaling functor size is 150995000 bytes  
Error at daubechies_scaling_test.cpp:test_quadratures:424:  
  Mollified relative error in _Float32 precision is 0.412, which exceeds 0.0345, error/tol  = 11.9.  
  Expected: +0.000000000 = +0x0p+0  
  Computed: +0.412235230 = +0x1.a620fep-2  
  ULP distance: +1.05401971e+09  
  Derivative of normalizing sum for 5 vanishing moment scaling function doesn't vanish.  
Testing 5 vanishing moment scaling function quadratures on type _Float64  
Scaling functor size is 301989960 bytes  
Testing 6 vanishing moment scaling function quadratures on type _Float32  
Scaling functor size is 276824100 bytes  
Error at daubechies_scaling_test.cpp:test_quadratures:424:  
  Mollified relative error in _Float32 precision is 0.455, which exceeds 0.0345, error/tol  = 13.2.  
  Expected: +0.000000000 = +0x0p+0  
  Computed: +0.454844683 = +0x1.d1c2cep-2  
  ULP distance: +1.05544947e+09  
  Derivative of normalizing sum for 6 vanishing moment scaling function doesn't vanish.  
Testing 6 vanishing moment scaling function quadratures on type _Float64  
Scaling functor size is 553648184 bytes  
Testing 7 vanishing moment scaling function quadratures on type _Float32  
Scaling functor size is 327155748 bytes  
Error at daubechies_scaling_test.cpp:test_quadratures:424:  
  Mollified relative error in _Float32 precision is 1.65, which exceeds 0.0345, error/tol  = 47.8.  
  Expected: +0.000000000 = +0x0p+0  
  Computed: -1.649215817 = -0x1.a63302p+0  
  ULP distance: +1.07079923e+09  
  Derivative of normalizing sum for 7 vanishing moment scaling function doesn't vanish.  
Testing 7 vanishing moment scaling function quadratures on type _Float64  
Scaling functor size is 654311480 bytes  
Testing 8 vanishing moment scaling function quadratures on type _Float32  
Scaling functor size is 377487396 bytes  
Testing 8 vanishing moment scaling function quadratures on type _Float64  
Scaling functor size is 754974776 bytes  
Testing 9 vanishing moment scaling function quadratures on type _Float32  
Scaling functor size is 427819044 bytes  
Error at daubechies_scaling_test.cpp:test_quadratures:424:  
  Mollified relative error in _Float32 precision is 0.146, which exceeds 0.0345, error/tol  = 4.24.  
  Expected: +0.000000000 = +0x0p+0  
  Computed: +0.146283329 = +0x1.2b9698p-3  
  ULP distance: +1.04161568e+09  
  Derivative of normalizing sum for 9 vanishing moment scaling function doesn't vanish.  
Testing 9 vanishing moment scaling function quadratures on type _Float64  
Scaling functor size is 855638072 bytes  
Testing 10 vanishing moment scaling function quadratures on type _Float32  
Scaling functor size is 637534272 bytes  
Error at daubechies_scaling_test.cpp:test_quadratures:424:  
  Mollified relative error in _Float32 precision is 0.0988, which exceeds 0.0345, error/tol  = 2.86.  
  Expected: +0.000000000 = +0x0p+0  
  Computed: +0.098770872 = +0x1.9490c4p-4  
  ULP distance: +1.03666701e+09  
  Derivative of normalizing sum for 10 vanishing moment scaling function doesn't vanish.  
Testing 10 vanishing moment scaling function quadratures on type _Float64  
Scaling functor size is 1275068504 bytes  
Testing 11 vanishing moment scaling function quadratures on type _Float32  
Scaling functor size is 704643136 bytes  
Error at daubechies_scaling_test.cpp:test_quadratures:424:  
  Mollified relative error in _Float32 precision is 0.338, which exceeds 0.0345, error/tol  = 9.78.  
  Expected: +0.000000000 = +0x0p+0  
  Computed: -0.337837547 = -0x1.59f216p-2  
  ULP distance: +1.05152333e+09  
  Derivative of normalizing sum for 11 vanishing moment scaling function doesn't vanish.  
Testing 11 vanishing moment scaling function quadratures on type _Float64  
Scaling functor size is 1409286232 bytes  
Testing 12 vanishing moment scaling function quadratures on type _Float32  
Scaling functor size is 771752000 bytes  
Error at daubechies_scaling_test.cpp:test_quadratures:424:  
  Mollified relative error in _Float32 precision is 0.763, which exceeds 0.0345, error/tol  = 22.1.  
  Expected: +0.000000000 = +0x0p+0  
  Computed: -0.762872636 = -0x1.86973ep-1  
  ULP distance: +1.06137485e+09  
  Derivative of normalizing sum for 12 vanishing moment scaling function doesn't vanish.  
Testing 12 vanishing moment scaling function quadratures on type _Float64  
Scaling functor size is 1543503960 bytes  
Testing 13 vanishing moment scaling function quadratures on type _Float32  
Scaling functor size is 838860864 bytes  
Error at daubechies_scaling_test.cpp:test_quadratures:424:  
  Mollified relative error in _Float32 precision is 0.523, which exceeds 0.0345, error/tol  = 15.1.  
  Expected: +0.000000000 = +0x0p+0  
  Computed: +0.522994280 = +0x1.0bc5e8p-1  
  ULP distance: +1.0573504e+09  
  Derivative of normalizing sum for 13 vanishing moment scaling function doesn't vanish.  
Testing 13 vanishing moment scaling function quadratures on type _Float64  
Scaling functor size is 1677721688 bytes  
Testing 14 vanishing moment scaling function quadratures on type _Float32  
Scaling functor size is 905969728 bytes  
Error at daubechies_scaling_test.cpp:test_quadratures:424:  
  Mollified relative error in _Float32 precision is 0.089, which exceeds 0.0345, error/tol  = 2.58.  
  Expected: +0.000000000 = +0x0p+0  
  Computed: -0.088996731 = -0x1.6c87d6p-4  
  ULP distance: +1.03535514e+09  
  Derivative of normalizing sum for 14 vanishing moment scaling function doesn't vanish.  
Testing 14 vanishing moment scaling function quadratures on type _Float64  
Scaling functor size is 1811939416 bytes  
Testing 15 vanishing moment scaling function quadratures on type _Float32  
Scaling functor size is 973078592 bytes  
Error at daubechies_scaling_test.cpp:test_quadratures:424:  
  Mollified relative error in _Float32 precision is 0.567, which exceeds 0.0345, error/tol  = 16.4.  
  Expected: +0.000000000 = +0x0p+0  
  Computed: +0.567362785 = +0x1.227d6p-1  
  ULP distance: +1.05809478e+09  
  Derivative of normalizing sum for 15 vanishing moment scaling function doesn't vanish.  
Testing 15 vanishing moment scaling function quadratures on type _Float64  
Scaling functor size is 1946157144 bytes  
Testing 16 vanishing moment scaling function quadratures on type _Float32  
Scaling functor size is 1040187456 bytes  
Error at daubechies_scaling_test.cpp:test_quadratures:424:  
  Mollified relative error in _Float32 precision is 0.278, which exceeds 0.0345, error/tol  = 8.04.  
  Expected: +0.000000000 = +0x0p+0  
  Computed: -0.277678818 = -0x1.1c57d6p-2  
  ULP distance: +1.04950477e+09  
  Derivative of normalizing sum for 16 vanishing moment scaling function doesn't vanish.  
Testing 16 vanishing moment scaling function quadratures on type _Float64  
Scaling functor size is 2080374872 bytes  
Testing 17 vanishing moment scaling function quadratures on type _Float32  
Scaling functor size is 1107296320 bytes  
Error at daubechies_scaling_test.cpp:test_quadratures:424:  
  Mollified relative error in _Float32 precision is 0.615, which exceeds 0.0345, error/tol  = 17.8.  
  Expected: +0.000000000 = +0x0p+0  
  Computed: -0.614834547 = -0x1.3acb98p-1  
  ULP distance: +1.0588912e+09  
  Derivative of normalizing sum for 17 vanishing moment scaling function doesn't vanish.  
Testing 17 vanishing moment scaling function quadratures on type _Float64  
Scaling functor size is 2214592600 bytes  
Testing 18 vanishing moment scaling function quadratures on type _Float32  
Scaling functor size is 1174405184 bytes  
Error at daubechies_scaling_test.cpp:test_quadratures:424:  
  Mollified relative error in _Float32 precision is 0.543, which exceeds 0.0345, error/tol  = 15.7.  
  Expected: +0.000000000 = +0x0p+0  
  Computed: -0.542812467 = -0x1.15eb84p-1  
  ULP distance: +1.05768288e+09  
  Derivative of normalizing sum for 18 vanishing moment scaling function doesn't vanish.  
Testing 18 vanishing moment scaling function quadratures on type _Float64  
Scaling functor size is 2348810328 bytes  
Testing 19 vanishing moment scaling function quadratures on type _Float32  
Scaling functor size is 1241514048 bytes  
Error at daubechies_scaling_test.cpp:test_quadratures:424:  
  Mollified relative error in _Float32 precision is 0.297, which exceeds 0.0345, error/tol  = 8.59.  
  Expected: +0.000000000 = +0x0p+0  
  Computed: +0.296685010 = +0x1.2fce32p-2  
  ULP distance: +1.05014246e+09  
  Derivative of normalizing sum for 19 vanishing moment scaling function doesn't vanish.  
Testing 19 vanishing moment scaling function quadratures on type _Float64  
Scaling functor size is 2483028056 bytes  
Testing agreement with Ten Lectures  
Testing Daubechies filters with 1 vanishing moments on type _Float32  
Testing Daubechies filters with 1 vanishing moments on type _Float64  
Testing Daubechies filters with 1 vanishing moments on type long double  
Testing Daubechies filters with 2 vanishing moments on type _Float32  
Testing Daubechies filters with 2 vanishing moments on type _Float64  
Testing Daubechies filters with 2 vanishing moments on type long double  
Testing Daubechies filters with 3 vanishing moments on type _Float32  
Testing Daubechies filters with 3 vanishing moments on type _Float64  
Testing Daubechies filters with 3 vanishing moments on type long double  
Testing Daubechies filters with 4 vanishing moments on type _Float32  
Testing Daubechies filters with 4 vanishing moments on type _Float64  
Testing Daubechies filters with 4 vanishing moments on type long double  
Testing Daubechies filters with 5 vanishing moments on type _Float32  
Testing Daubechies filters with 5 vanishing moments on type _Float64  
Testing Daubechies filters with 5 vanishing moments on type long double  
Testing Daubechies filters with 6 vanishing moments on type _Float32  
Testing Daubechies filters with 6 vanishing moments on type _Float64  
Testing Daubechies filters with 6 vanishing moments on type long double  
Testing Daubechies filters with 7 vanishing moments on type _Float32  
Testing Daubechies filters with 7 vanishing moments on type _Float64  
Testing Daubechies filters with 7 vanishing moments on type long double  
Testing Daubechies filters with 8 vanishing moments on type _Float32  
Testing Daubechies filters with 8 vanishing moments on type _Float64  
Testing Daubechies filters with 8 vanishing moments on type long double  
Testing Daubechies filters with 9 vanishing moments on type _Float32  
Testing Daubechies filters with 9 vanishing moments on type _Float64  
Testing Daubechies filters with 9 vanishing moments on type long double  
Testing Daubechies filters with 10 vanishing moments on type _Float32  
Testing Daubechies filters with 10 vanishing moments on type _Float64  
Testing Daubechies filters with 10 vanishing moments on type long double  
Testing Daubechies filters with 11 vanishing moments on type _Float32  
Testing Daubechies filters with 11 vanishing moments on type _Float64  
Testing Daubechies filters with 11 vanishing moments on type long double  
Testing Daubechies filters with 12 vanishing moments on type _Float32  
Testing Daubechies filters with 12 vanishing moments on type _Float64  
Testing Daubechies filters with 12 vanishing moments on type long double  
Testing Daubechies filters with 13 vanishing moments on type _Float32  
Testing Daubechies filters with 13 vanishing moments on type _Float64  
Testing Daubechies filters with 13 vanishing moments on type long double  
Testing Daubechies filters with 14 vanishing moments on type _Float32  
Testing Daubechies filters with 14 vanishing moments on type _Float64  
Testing Daubechies filters with 14 vanishing moments on type long double  
Testing Daubechies filters with 15 vanishing moments on type _Float32  
Testing Daubechies filters with 15 vanishing moments on type _Float64  
Testing Daubechies filters with 15 vanishing moments on type long double  
Testing Daubechies filters with 16 vanishing moments on type _Float32  
Testing Daubechies filters with 16 vanishing moments on type _Float64  
Testing Daubechies filters with 16 vanishing moments on type long double  
Testing Daubechies filters with 17 vanishing moments on type _Float32  
Testing Daubechies filters with 17 vanishing moments on type _Float64  
Testing Daubechies filters with 17 vanishing moments on type long double  
Testing Daubechies filters with 18 vanishing moments on type _Float32  
Testing Daubechies filters with 18 vanishing moments on type _Float64  
Testing Daubechies filters with 18 vanishing moments on type long double  
Testing Daubechies filters with 19 vanishing moments on type _Float32  
Testing Daubechies filters with 19 vanishing moments on type _Float64  
Testing Daubechies filters with 19 vanishing moments on type long double  
Testing integer grid with 2 vanishing moments and 0 derivative on type _Float32  
Testing integer grid with 2 vanishing moments and 1 derivative on type _Float32  
Testing integer grid with 2 vanishing moments and 0 derivative on type _Float64  
Testing integer grid with 2 vanishing moments and 1 derivative on type _Float64  
Testing integer grid with 2 vanishing moments and 0 derivative on type long double  
Testing integer grid with 2 vanishing moments and 1 derivative on type long double  
Testing integer grid with 3 vanishing moments and 0 derivative on type _Float32  
Testing integer grid with 3 vanishing moments and 1 derivative on type _Float32  
Testing integer grid with 3 vanishing moments and 0 derivative on type _Float64  
Testing integer grid with 3 vanishing moments and 1 derivative on type _Float64  
Testing integer grid with 3 vanishing moments and 0 derivative on type long double  
Testing integer grid with 3 vanishing moments and 1 derivative on type long double  
Testing integer grid with 4 vanishing moments and 0 derivative on type _Float32  
Testing integer grid with 4 vanishing moments and 1 derivative on type _Float32  
Testing integer grid with 4 vanishing moments and 0 derivative on type _Float64  
Testing integer grid with 4 vanishing moments and 1 derivative on type _Float64  
Testing integer grid with 4 vanishing moments and 0 derivative on type long double  
Testing integer grid with 4 vanishing moments and 1 derivative on type long double  
Testing integer grid with 5 vanishing moments and 0 derivative on type _Float32  
Testing integer grid with 5 vanishing moments and 1 derivative on type _Float32  
Testing integer grid with 5 vanishing moments and 0 derivative on type _Float64  
Testing integer grid with 5 vanishing moments and 1 derivative on type _Float64  
Testing integer grid with 5 vanishing moments and 0 derivative on type long double  
Testing integer grid with 5 vanishing moments and 1 derivative on type long double  
Testing integer grid with 6 vanishing moments and 0 derivative on type _Float32  
Testing integer grid with 6 vanishing moments and 1 derivative on type _Float32  
Testing integer grid with 6 vanishing moments and 0 derivative on type _Float64  
Testing integer grid with 6 vanishing moments and 1 derivative on type _Float64  
Testing integer grid with 6 vanishing moments and 0 derivative on type long double  
Testing integer grid with 6 vanishing moments and 1 derivative on type long double  
Testing integer grid with 7 vanishing moments and 0 derivative on type _Float32  
Testing integer grid with 7 vanishing moments and 1 derivative on type _Float32  
Testing integer grid with 7 vanishing moments and 0 derivative on type _Float64  
Testing integer grid with 7 vanishing moments and 1 derivative on type _Float64  
Testing integer grid with 7 vanishing moments and 0 derivative on type long double  
Testing integer grid with 7 vanishing moments and 1 derivative on type long double  
Testing integer grid with 8 vanishing moments and 0 derivative on type _Float32  
Testing integer grid with 8 vanishing moments and 1 derivative on type _Float32  
Testing integer grid with 8 vanishing moments and 0 derivative on type _Float64  
Testing integer grid with 8 vanishing moments and 1 derivative on type _Float64  
Testing integer grid with 8 vanishing moments and 0 derivative on type long double  
Testing integer grid with 8 vanishing moments and 1 derivative on type long double  
Testing integer grid with 9 vanishing moments and 0 derivative on type _Float32  
Testing integer grid with 9 vanishing moments and 1 derivative on type _Float32  
Testing integer grid with 9 vanishing moments and 0 derivative on type _Float64  
Testing integer grid with 9 vanishing moments and 1 derivative on type _Float64  
Testing integer grid with 9 vanishing moments and 0 derivative on type long double  
Testing integer grid with 9 vanishing moments and 1 derivative on type long double  
Testing integer grid with 10 vanishing moments and 0 derivative on type _Float32  
Testing integer grid with 10 vanishing moments and 1 derivative on type _Float32  
Testing integer grid with 10 vanishing moments and 0 derivative on type _Float64  
Testing integer grid with 10 vanishing moments and 1 derivative on type _Float64  
Testing integer grid with 10 vanishing moments and 0 derivative on type long double  
Testing integer grid with 10 vanishing moments and 1 derivative on type long double  
Testing integer grid with 11 vanishing moments and 0 derivative on type _Float32  
Testing integer grid with 11 vanishing moments and 1 derivative on type _Float32  
Testing integer grid with 11 vanishing moments and 0 derivative on type _Float64  
Testing integer grid with 11 vanishing moments and 1 derivative on type _Float64  
Testing integer grid with 11 vanishing moments and 0 derivative on type long double  
Testing integer grid with 11 vanishing moments and 1 derivative on type long double  
Testing integer grid with 12 vanishing moments and 0 derivative on type _Float32  
Testing integer grid with 12 vanishing moments and 1 derivative on type _Float32  
Testing integer grid with 12 vanishing moments and 0 derivative on type _Float64  
Testing integer grid with 12 vanishing moments and 1 derivative on type _Float64  
Testing integer grid with 12 vanishing moments and 0 derivative on type long double  
Testing integer grid with 12 vanishing moments and 1 derivative on type long double  
Testing integer grid with 13 vanishing moments and 0 derivative on type _Float32  
Testing integer grid with 13 vanishing moments and 1 derivative on type _Float32  
Testing integer grid with 13 vanishing moments and 0 derivative on type _Float64  
Testing integer grid with 13 vanishing moments and 1 derivative on type _Float64  
Testing integer grid with 13 vanishing moments and 0 derivative on type long double  
Testing integer grid with 13 vanishing moments and 1 derivative on type long double  
Testing integer grid with 14 vanishing moments and 0 derivative on type _Float32  
Testing integer grid with 14 vanishing moments and 1 derivative on type _Float32  
Testing integer grid with 14 vanishing moments and 0 derivative on type _Float64  
Testing integer grid with 14 vanishing moments and 1 derivative on type _Float64  
Testing integer grid with 14 vanishing moments and 0 derivative on type long double  
Testing integer grid with 14 vanishing moments and 1 derivative on type long double  
Testing integer grid with 15 vanishing moments and 0 derivative on type _Float32  
Testing integer grid with 15 vanishing moments and 1 derivative on type _Float32  
Testing integer grid with 15 vanishing moments and 0 derivative on type _Float64  
Testing integer grid with 15 vanishing moments and 1 derivative on type _Float64  
Testing integer grid with 15 vanishing moments and 0 derivative on type long double  
Testing integer grid with 15 vanishing moments and 1 derivative on type long double  
Testing integer grid with 16 vanishing moments and 0 derivative on type _Float32  
Testing integer grid with 16 vanishing moments and 1 derivative on type _Float32  
Testing integer grid with 16 vanishing moments and 0 derivative on type _Float64  
Testing integer grid with 16 vanishing moments and 1 derivative on type _Float64  
Testing integer grid with 16 vanishing moments and 0 derivative on type long double  
Testing integer grid with 16 vanishing moments and 1 derivative on type long double  
Testing integer grid with 17 vanishing moments and 0 derivative on type _Float32  
Testing integer grid with 17 vanishing moments and 1 derivative on type _Float32  
Testing integer grid with 17 vanishing moments and 0 derivative on type _Float64  
Testing integer grid with 17 vanishing moments and 1 derivative on type _Float64  
Testing integer grid with 17 vanishing moments and 0 derivative on type long double  
Testing integer grid with 17 vanishing moments and 1 derivative on type long double  
Testing integer grid with 18 vanishing moments and 0 derivative on type _Float32  
Testing integer grid with 18 vanishing moments and 1 derivative on type _Float32  
Testing integer grid with 18 vanishing moments and 0 derivative on type _Float64  
Testing integer grid with 18 vanishing moments and 1 derivative on type _Float64  
Testing integer grid with 18 vanishing moments and 0 derivative on type long double  
Testing integer grid with 18 vanishing moments and 1 derivative on type long double  
Testing integer grid with 19 vanishing moments and 0 derivative on type _Float32  
Testing integer grid with 19 vanishing moments and 1 derivative on type _Float32  
Testing integer grid with 19 vanishing moments and 0 derivative on type _Float64  
Testing integer grid with 19 vanishing moments and 1 derivative on type _Float64  
Testing integer grid with 19 vanishing moments and 0 derivative on type long double  
Testing integer grid with 19 vanishing moments and 1 derivative on type long double  
Testing integer grid with 3 vanishing moments and 2 derivative on type _Float32  
Testing integer grid with 3 vanishing moments and 2 derivative on type _Float64  
Testing integer grid with 3 vanishing moments and 2 derivative on type long double  
Testing integer grid with 4 vanishing moments and 2 derivative on type _Float32  
Testing integer grid with 4 vanishing moments and 2 derivative on type _Float64  
Testing integer grid with 4 vanishing moments and 2 derivative on type long double  
Testing integer grid with 5 vanishing moments and 2 derivative on type _Float32  
Testing integer grid with 5 vanishing moments and 2 derivative on type _Float64  
Testing integer grid with 5 vanishing moments and 2 derivative on type long double  
Testing integer grid with 6 vanishing moments and 2 derivative on type _Float32  
Testing integer grid with 6 vanishing moments and 2 derivative on type _Float64  
Testing integer grid with 6 vanishing moments and 2 derivative on type long double  
Testing integer grid with 7 vanishing moments and 2 derivative on type _Float32  
Testing integer grid with 7 vanishing moments and 2 derivative on type _Float64  
Testing integer grid with 7 vanishing moments and 2 derivative on type long double  
Testing integer grid with 8 vanishing moments and 2 derivative on type _Float32  
Testing integer grid with 8 vanishing moments and 2 derivative on type _Float64  
Testing integer grid with 8 vanishing moments and 2 derivative on type long double  
Testing integer grid with 9 vanishing moments and 2 derivative on type _Float32  
Testing integer grid with 9 vanishing moments and 2 derivative on type _Float64  
Testing integer grid with 9 vanishing moments and 2 derivative on type long double  
Testing integer grid with 10 vanishing moments and 2 derivative on type _Float32  
Testing integer grid with 10 vanishing moments and 2 derivative on type _Float64  
Testing integer grid with 10 vanishing moments and 2 derivative on type long double  
Testing integer grid with 11 vanishing moments and 2 derivative on type _Float32  
Testing integer grid with 11 vanishing moments and 2 derivative on type _Float64  
Testing integer grid with 11 vanishing moments and 2 derivative on type long double  
Testing integer grid with 12 vanishing moments and 2 derivative on type _Float32  
Testing integer grid with 12 vanishing moments and 2 derivative on type _Float64  
Testing integer grid with 12 vanishing moments and 2 derivative on type long double  
Testing integer grid with 13 vanishing moments and 2 derivative on type _Float32  
Testing integer grid with 13 vanishing moments and 2 derivative on type _Float64  
Testing integer grid with 13 vanishing moments and 2 derivative on type long double  
Testing integer grid with 14 vanishing moments and 2 derivative on type _Float32  
Testing integer grid with 14 vanishing moments and 2 derivative on type _Float64  
Testing integer grid with 14 vanishing moments and 2 derivative on type long double  
Testing integer grid with 15 vanishing moments and 2 derivative on type _Float32  
Testing integer grid with 15 vanishing moments and 2 derivative on type _Float64  
Testing integer grid with 15 vanishing moments and 2 derivative on type long double  
Testing integer grid with 16 vanishing moments and 2 derivative on type _Float32  
Testing integer grid with 16 vanishing moments and 2 derivative on type _Float64  
Testing integer grid with 16 vanishing moments and 2 derivative on type long double  
Testing integer grid with 17 vanishing moments and 2 derivative on type _Float32  
Testing integer grid with 17 vanishing moments and 2 derivative on type _Float64  
Testing integer grid with 17 vanishing moments and 2 derivative on type long double  
Testing integer grid with 18 vanishing moments and 2 derivative on type _Float32  
Testing integer grid with 18 vanishing moments and 2 derivative on type _Float64  
Testing integer grid with 18 vanishing moments and 2 derivative on type long double  
Testing integer grid with 19 vanishing moments and 2 derivative on type _Float32  
Testing integer grid with 19 vanishing moments and 2 derivative on type _Float64  
Testing integer grid with 19 vanishing moments and 2 derivative on type long double  
Testing integer grid with 4 vanishing moments and 3 derivative on type _Float32  
Testing integer grid with 4 vanishing moments and 3 derivative on type _Float64  
Testing integer grid with 4 vanishing moments and 3 derivative on type long double  
Testing integer grid with 5 vanishing moments and 3 derivative on type _Float32  
Testing integer grid with 5 vanishing moments and 3 derivative on type _Float64  
Testing integer grid with 5 vanishing moments and 3 derivative on type long double  
Testing integer grid with 6 vanishing moments and 3 derivative on type _Float32  
Testing integer grid with 6 vanishing moments and 3 derivative on type _Float64  
Testing integer grid with 6 vanishing moments and 3 derivative on type long double  
Testing integer grid with 7 vanishing moments and 3 derivative on type _Float32  
Testing integer grid with 7 vanishing moments and 3 derivative on type _Float64  
Testing integer grid with 7 vanishing moments and 3 derivative on type long double  
Testing integer grid with 8 vanishing moments and 3 derivative on type _Float32  
Testing integer grid with 8 vanishing moments and 3 derivative on type _Float64  
Testing integer grid with 8 vanishing moments and 3 derivative on type long double  
Testing integer grid with 9 vanishing moments and 3 derivative on type _Float32  
Testing integer grid with 9 vanishing moments and 3 derivative on type _Float64  
Testing integer grid with 9 vanishing moments and 3 derivative on type long double  
Testing integer grid with 10 vanishing moments and 3 derivative on type _Float32  
Testing integer grid with 10 vanishing moments and 3 derivative on type _Float64  
Testing integer grid with 10 vanishing moments and 3 derivative on type long double  
Testing integer grid with 11 vanishing moments and 3 derivative on type _Float32  
Testing integer grid with 11 vanishing moments and 3 derivative on type _Float64  
Testing integer grid with 11 vanishing moments and 3 derivative on type long double  
Testing integer grid with 12 vanishing moments and 3 derivative on type _Float32  
Testing integer grid with 12 vanishing moments and 3 derivative on type _Float64  
Testing integer grid with 12 vanishing moments and 3 derivative on type long double  
Testing integer grid with 13 vanishing moments and 3 derivative on type _Float32  
Testing integer grid with 13 vanishing moments and 3 derivative on type _Float64  
Testing integer grid with 13 vanishing moments and 3 derivative on type long double  
Testing integer grid with 14 vanishing moments and 3 derivative on type _Float32  
Testing integer grid with 14 vanishing moments and 3 derivative on type _Float64  
Testing integer grid with 14 vanishing moments and 3 derivative on type long double  
Testing integer grid with 15 vanishing moments and 3 derivative on type _Float32  
Testing integer grid with 15 vanishing moments and 3 derivative on type _Float64  
Testing integer grid with 15 vanishing moments and 3 derivative on type long double  
Testing integer grid with 16 vanishing moments and 3 derivative on type _Float32  
Testing integer grid with 16 vanishing moments and 3 derivative on type _Float64  
Testing integer grid with 16 vanishing moments and 3 derivative on type long double  
Testing integer grid with 17 vanishing moments and 3 derivative on type _Float32  
Testing integer grid with 17 vanishing moments and 3 derivative on type _Float64  
Testing integer grid with 17 vanishing moments and 3 derivative on type long double  
Testing integer grid with 18 vanishing moments and 3 derivative on type _Float32  
Testing integer grid with 18 vanishing moments and 3 derivative on type _Float64  
Testing integer grid with 18 vanishing moments and 3 derivative on type long double  
Testing integer grid with 19 vanishing moments and 3 derivative on type _Float32  
Testing integer grid with 19 vanishing moments and 3 derivative on type _Float64  
Testing integer grid with 19 vanishing moments and 3 derivative on type long double  
Testing integer grid with 5 vanishing moments and 4 derivative on type _Float32  
Testing integer grid with 5 vanishing moments and 4 derivative on type _Float64  
Testing integer grid with 5 vanishing moments and 4 derivative on type long double  
Testing integer grid with 6 vanishing moments and 4 derivative on type _Float32  
Testing integer grid with 6 vanishing moments and 4 derivative on type _Float64  
Testing integer grid with 6 vanishing moments and 4 derivative on type long double  
Testing integer grid with 7 vanishing moments and 4 derivative on type _Float32  
Testing integer grid with 7 vanishing moments and 4 derivative on type _Float64  
Testing integer grid with 7 vanishing moments and 4 derivative on type long double  
Testing integer grid with 8 vanishing moments and 4 derivative on type _Float32  
Testing integer grid with 8 vanishing moments and 4 derivative on type _Float64  
Testing integer grid with 8 vanishing moments and 4 derivative on type long double  
Testing integer grid with 9 vanishing moments and 4 derivative on type _Float32  
Testing integer grid with 9 vanishing moments and 4 derivative on type _Float64  
Testing integer grid with 9 vanishing moments and 4 derivative on type long double  
Testing integer grid with 10 vanishing moments and 4 derivative on type _Float32  
Testing integer grid with 10 vanishing moments and 4 derivative on type _Float64  
Testing integer grid with 10 vanishing moments and 4 derivative on type long double  
Testing integer grid with 11 vanishing moments and 4 derivative on type _Float32  
Testing integer grid with 11 vanishing moments and 4 derivative on type _Float64  
Testing integer grid with 11 vanishing moments and 4 derivative on type long double  
Testing integer grid with 12 vanishing moments and 4 derivative on type _Float32  
Testing integer grid with 12 vanishing moments and 4 derivative on type _Float64  
Testing integer grid with 12 vanishing moments and 4 derivative on type long double  
Testing integer grid with 13 vanishing moments and 4 derivative on type _Float32  
Testing integer grid with 13 vanishing moments and 4 derivative on type _Float64  
Testing integer grid with 13 vanishing moments and 4 derivative on type long double  
Testing integer grid with 14 vanishing moments and 4 derivative on type _Float32  
Testing integer grid with 14 vanishing moments and 4 derivative on type _Float64  
Testing integer grid with 14 vanishing moments and 4 derivative on type long double  
Testing integer grid with 15 vanishing moments and 4 derivative on type _Float32  
Testing integer grid with 15 vanishing moments and 4 derivative on type _Float64  
Testing integer grid with 15 vanishing moments and 4 derivative on type long double  
Testing integer grid with 16 vanishing moments and 4 derivative on type _Float32  
Testing integer grid with 16 vanishing moments and 4 derivative on type _Float64  
Testing integer grid with 16 vanishing moments and 4 derivative on type long double  
Testing integer grid with 17 vanishing moments and 4 derivative on type _Float32  
Testing integer grid with 17 vanishing moments and 4 derivative on type _Float64  
Testing integer grid with 17 vanishing moments and 4 derivative on type long double  
Testing integer grid with 18 vanishing moments and 4 derivative on type _Float32  
Testing integer grid with 18 vanishing moments and 4 derivative on type _Float64  
Testing integer grid with 18 vanishing moments and 4 derivative on type long double  
Testing integer grid with 19 vanishing moments and 4 derivative on type _Float32  
Testing integer grid with 19 vanishing moments and 4 derivative on type _Float64  
Testing integer grid with 19 vanishing moments and 4 derivative on type long double  
Testing dyadic grid on type _Float32  
Testing dyadic grid on type _Float64  
Testing dyadic grid on type long double  
Error count: 14, total ulp distance = 14738470912  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
  
Will investigate tomorrow.  
  
@NAThompson

---

## Comment 18

> Username: NAThompson  
> Created_at: 2023-05-17 18:47:14 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1551891994  

@mborland : Yeah that's gone from working to completely wrong. Bizarre.

---

## Comment 19

> Username: jzmaddock  
> Created_at: 2023-05-27 12:16:42 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1565385303  

What's still in the TODO list here?

---

## Comment 20

> Username: mborland  
> Created_at: 2023-05-30 07:42:37 UTC  
> Updated_at: 2023-05-30 15:31:01 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1567928045  

> What's still in the TODO list here?  
  
1. Fix the issue with daubechies_scaling_test and _Float32 from above. I couldn't find anything.  
2. Add tests in the autodiff test suite.  
3. Re-add all the removed drone configs.  
  
I think those two are really it unless something crazy pops up in the autodiff tests.

---

## Comment 21

> Username: jzmaddock  
> Created_at: 2023-05-30 08:31:36 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1567999790  

Thanks Matt: I won't have time to look at this now for a couple of weeks, but in daubechies_scaling_test there are no relevant changes that would effect it I think, and type *float* failed *only* when compiling with C++23.  That was as far as I got, if someone can reduce to a single test case we can probably track it down.

---

## Comment 22

> Username: mborland  
> Created_at: 2023-05-30 08:46:52 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1568022546  

I should have time this week to finish this out. There's not a ton of time until the merge window closes for the next release.

---

## Comment 23

> Username: mborland  
> Created_at: 2023-05-31 11:35:12 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1570024166  

The rest of the tests have been added. `test_autodiff_2` and `test_autodiff_3` both hang in the CI system and locally with the new floats.

---

## Comment 24

> Username: pulver  
> Created_at: 2023-05-31 12:03:41 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1570078972  

> `test_autodiff_2` and `test_autodiff_3` both hang in the CI system and locally with the new floats.  
  
Where are they hanging? If you can direct me to any diagnostic info, I can take a look - I'm having trouble locating any useful info from the CI runs.

---

## Comment 25

> Username: mborland  
> Created_at: 2023-05-31 12:27:00 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1570140916  

> > `test_autodiff_2` and `test_autodiff_3` both hang in the CI system and locally with the new floats.  
>   
> Where are they hanging? If you can direct me to any diagnostic info, I can take a look - I'm having trouble locating any useful info from the CI runs.  
  
`test_autodiff_2` hangs at `pow2` and `test_autodiff_3` hangs for digamma/tgamma/lgamma. I am pretty sure the reason in `test_autodiff_2` is the stack silently overflowing when testing the results of `pow(T, integer)`.

---

## Comment 26

> Username: pulver  
> Created_at: 2023-05-31 13:09:41 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1570207448  

> I am pretty sure the reason in `test_autodiff_2` is the stack silently overflowing when testing the results of `pow(T, integer)`.  
  
If I'm looking in the right place  
https://github.com/boostorg/math/blob/b66264fa9007e124e6b85c55d8e45202145fbcb7/test/test_autodiff_2.cpp#L136-L142  
  
In what sense/context can the second parameter to `pow()` be an `integer`?  
  
Basically this is testing multiple derivatives around the evaluation of `pow(2, 2.5)`.  
  
More specifically, the 36 values:  
![image](https://github.com/boostorg/math/assets/39707/1638bcad-a8b7-47d3-819d-56757e04835d)  
where N_6={0,1,2,3,4,5}.  
  
HTH but if not it may take me a few days to get to where I can try running the tests locally.

---

## Comment 27

> Username: mborland  
> Created_at: 2023-05-31 13:14:12 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1570214651  

> > I am pretty sure the reason in `test_autodiff_2` is the stack silently overflowing when testing the results of `pow(T, integer)`.  
>   
> If I'm looking in the right place  
>   
> https://github.com/boostorg/math/blob/b66264fa9007e124e6b85c55d8e45202145fbcb7/test/test_autodiff_2.cpp#L136-L142  
>   
> In what sense/context can the second parameter to `pow()` be an `integer`?  
>   
> Basically this is testing multiple derivatives around the evaluation of `pow(2, 2.5)`.  
>   
> More specifically, the 36 values: ![image](https://user-images.githubusercontent.com/39707/242284187-1638bcad-a8b7-47d3-819d-56757e04835d.png) where N_6={0,1,2,3,4,5}.  
>   
> HTH but if not it may take me a few days to get to where I can try running the tests locally.  
  
My mistake it's actually building an array in this case: https://github.com/boostorg/math/blob/b66264fa9007e124e6b85c55d8e45202145fbcb7/test/test_autodiff_2.cpp#L175  
  
The bug is here and will be resolved in the next GCC 13.X release: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=109883

---

## Comment 28

> Username: mborland  
> Created_at: 2023-05-31 15:07:27 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1570418363  

`test_autodiff_2` is good now. It looks like the remaining bugs are actually in the *gamma functions, and not autodiff.

---

## Comment 29

> Username: mborland  
> Created_at: 2023-06-05 13:17:04 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1576786980  

`test_autodiff_8` yields the following failures:  
  
```  
====== BEGIN OUTPUT ======  
Running 3 test cases...  
test_autodiff_8.cpp:19: error: in "test_autodiff_8/hermite_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:19: error: in "test_autodiff_8/hermite_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:19: error: in "test_autodiff_8/hermite_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:19: error: in "test_autodiff_8/hermite_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:19: error: in "test_autodiff_8/hermite_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:19: error: in "test_autodiff_8/hermite_hpp<_Float64>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:19: error: in "test_autodiff_8/hermite_hpp<_Float64>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:19: error: in "test_autodiff_8/hermite_hpp<_Float64>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:19: error: in "test_autodiff_8/hermite_hpp<_Float64>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
  
*** 9 failures are detected in the test module "test_autodiff"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
```  
  
```  
====== BEGIN OUTPUT ======  
Running 15 test cases...  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:152: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:152: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:152: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:152: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:152: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:152: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:152: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:152: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float32>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:152: error: in "test_autodiff_8/laguerre_hpp<_Float64>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float64>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:152: error: in "test_autodiff_8/laguerre_hpp<_Float64>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float64>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:152: error: in "test_autodiff_8/laguerre_hpp<_Float64>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float64>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:152: error: in "test_autodiff_8/laguerre_hpp<_Float64>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float64>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float64>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:152: error: in "test_autodiff_8/laguerre_hpp<_Float64>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<_Float64>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
  
*** 36 failures are detected in the test module "test_autodiff"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
```  
  
And like `test_autodiff_3` the *gamma functions all hang.

---

## Comment 30

> Username: mborland  
> Created_at: 2023-06-16 09:22:20 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1594386196  

All of the hanging tests have been fixed. The remaining errors are all out chebyshev:  
  
```  
====== BEGIN OUTPUT ======  
Running 15 test cases...  
test_autodiff_5.cpp(63): error: in "test_autodiff_5/chebyshev_hpp<_Float32>": difference{-nan} between boost::math::chebyshev_u(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_u(n, x){9.01137447} exceeds 0.000476837%  
test_autodiff_5.cpp(67): error: in "test_autodiff_5/chebyshev_hpp<_Float32>": difference{-nan} between boost::math::chebyshev_t_prime(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_t_prime(n, x){-6.32815123} exceeds 0.000476837%  
test_autodiff_5.cpp(63): error: in "test_autodiff_5/chebyshev_hpp<_Float32>": difference{-nan} between boost::math::chebyshev_u(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_u(n, x){11920.7471} exceeds 0.000476837%  
test_autodiff_5.cpp(67): error: in "test_autodiff_5/chebyshev_hpp<_Float32>": difference{-nan} between boost::math::chebyshev_t_prime(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_t_prime(n, x){-47441.875} exceeds 0.000476837%  
test_autodiff_5.cpp(63): error: in "test_autodiff_5/chebyshev_hpp<_Float32>": difference{-nan} between boost::math::chebyshev_u(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_u(n, x){-30.7728882} exceeds 0.000476837%  
test_autodiff_5.cpp(67): error: in "test_autodiff_5/chebyshev_hpp<_Float32>": difference{-nan} between boost::math::chebyshev_t_prime(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_t_prime(n, x){30.5897064} exceeds 0.000476837%  
test_autodiff_5.cpp(63): error: in "test_autodiff_5/chebyshev_hpp<_Float32>": difference{-nan} between boost::math::chebyshev_u(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_u(n, x){-2991.96802} exceeds 0.000476837%  
test_autodiff_5.cpp(67): error: in "test_autodiff_5/chebyshev_hpp<_Float32>": difference{-nan} between boost::math::chebyshev_t_prime(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_t_prime(n, x){11306.8506} exceeds 0.000476837%  
test_autodiff_5.cpp(63): error: in "test_autodiff_5/chebyshev_hpp<_Float32>": difference{-nan} between boost::math::chebyshev_u(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_u(n, x){6270.69922} exceeds 0.000476837%  
test_autodiff_5.cpp(67): error: in "test_autodiff_5/chebyshev_hpp<_Float32>": difference{-nan} between boost::math::chebyshev_t_prime(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_t_prime(n, x){-26684.5977} exceeds 0.000476837%  
test_autodiff_5.cpp(63): error: in "test_autodiff_5/chebyshev_hpp<_Float32>": difference{-nan} between boost::math::chebyshev_u(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_u(n, x){-2.22408295} exceeds 0.000476837%  
test_autodiff_5.cpp(67): error: in "test_autodiff_5/chebyshev_hpp<_Float32>": difference{-nan} between boost::math::chebyshev_t_prime(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_t_prime(n, x){1.00000012} exceeds 0.000476837%  
test_autodiff_5.cpp(63): error: in "test_autodiff_5/chebyshev_hpp<_Float32>": difference{-nan} between boost::math::chebyshev_u(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_u(n, x){31569.1934} exceeds 0.000476837%  
test_autodiff_5.cpp(67): error: in "test_autodiff_5/chebyshev_hpp<_Float32>": difference{-nan} between boost::math::chebyshev_t_prime(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_t_prime(n, x){-113594.961} exceeds 0.000476837%  
test_autodiff_5.cpp(63): error: in "test_autodiff_5/chebyshev_hpp<_Float32>": difference{-nan} between boost::math::chebyshev_u(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_u(n, x){100.86644} exceeds 0.000476837%  
test_autodiff_5.cpp(67): error: in "test_autodiff_5/chebyshev_hpp<_Float32>": difference{-nan} between boost::math::chebyshev_t_prime(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_t_prime(n, x){-293.264374} exceeds 0.000476837%  
test_autodiff_5.cpp(63): error: in "test_autodiff_5/chebyshev_hpp<_Float32>": difference{-nan} between boost::math::chebyshev_u(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_u(n, x){286.204926} exceeds 0.000476837%  
test_autodiff_5.cpp(67): error: in "test_autodiff_5/chebyshev_hpp<_Float32>": difference{-nan} between boost::math::chebyshev_t_prime(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_t_prime(n, x){-1172.71631} exceeds 0.000476837%  
test_autodiff_5.cpp(63): error: in "test_autodiff_5/chebyshev_hpp<_Float64>": difference{-nan} between boost::math::chebyshev_u(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_u(n, x){5.7738043295308845} exceeds 8.88178e-13%  
test_autodiff_5.cpp(67): error: in "test_autodiff_5/chebyshev_hpp<_Float64>": difference{-nan} between boost::math::chebyshev_t_prime(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_t_prime(n, x){-5.2053066497684393} exceeds 8.88178e-13%  
test_autodiff_5.cpp(63): error: in "test_autodiff_5/chebyshev_hpp<_Float64>": difference{-nan} between boost::math::chebyshev_u(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_u(n, x){199.38248633817307} exceeds 8.88178e-13%  
test_autodiff_5.cpp(67): error: in "test_autodiff_5/chebyshev_hpp<_Float64>": difference{-nan} between boost::math::chebyshev_t_prime(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_t_prime(n, x){-511.89720424407381} exceeds 8.88178e-13%  
test_autodiff_5.cpp(63): error: in "test_autodiff_5/chebyshev_hpp<_Float64>": difference{-nan} between boost::math::chebyshev_u(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_u(n, x){-12046.956380678164} exceeds 8.88178e-13%  
test_autodiff_5.cpp(67): error: in "test_autodiff_5/chebyshev_hpp<_Float64>": difference{-nan} between boost::math::chebyshev_t_prime(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_t_prime(n, x){38751.091796451481} exceeds 8.88178e-13%  
test_autodiff_5.cpp(63): error: in "test_autodiff_5/chebyshev_hpp<_Float64>": difference{-nan} between boost::math::chebyshev_u(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_u(n, x){38422.404679082363} exceeds 8.88178e-13%  
test_autodiff_5.cpp(67): error: in "test_autodiff_5/chebyshev_hpp<_Float64>": difference{-nan} between boost::math::chebyshev_t_prime(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_t_prime(n, x){-82928.550315927147} exceeds 8.88178e-13%  
test_autodiff_5.cpp(63): error: in "test_autodiff_5/chebyshev_hpp<_Float64>": difference{-nan} between boost::math::chebyshev_u(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_u(n, x){-3358.5034935958261} exceeds 8.88178e-13%  
test_autodiff_5.cpp(67): error: in "test_autodiff_5/chebyshev_hpp<_Float64>": difference{-nan} between boost::math::chebyshev_t_prime(n, make_fvar<T, m>(x)).derivative(0u){-nan} and boost::math::chebyshev_t_prime(n, x){7484.0733417252286} exceeds 8.88178e-13%  
test_autodiff_5.cpp(63): error: in "test_autodiff_5/chebyshev_hpp<boost__multiprecision__number<boost__multiprecision__backends__cpp_bin_float<50u_ (boost__multiprecision__backends__digit_base_type)10_ void_ int_ 0_ 0>_ (boost__multiprecision__expression_template_option)0>>": difference{nan} between boost::math::chebyshev_u(n, make_fvar<T, m>(x)).derivative(0u){nan} and boost::math::chebyshev_u(n, x){-389.8814389225778180667792605005027122200855724130772} exceeds 4.27642e-47%  
test_autodiff_5.cpp(67): error: in "test_autodiff_5/chebyshev_hpp<boost__multiprecision__number<boost__multiprecision__backends__cpp_bin_float<50u_ (boost__multiprecision__backends__digit_base_type)10_ void_ int_ 0_ 0>_ (boost__multiprecision__expression_template_option)0>>": difference{nan} between boost::math::chebyshev_t_prime(n, make_fvar<T, m>(x)).derivative(0u){nan} and boost::math::chebyshev_t_prime(n, x){603.1905345009690968940362923763626304892481824835757} exceeds 4.27642e-47%  
test_autodiff_5.cpp(63): error: in "test_autodiff_5/chebyshev_hpp<boost__multiprecision__number<boost__multiprecision__backends__cpp_bin_float<50u_ (boost__multiprecision__backends__digit_base_type)10_ void_ int_ 0_ 0>_ (boost__multiprecision__expression_template_option)0>>": difference{nan} between boost::math::chebyshev_u(n, make_fvar<T, m>(x)).derivative(0u){nan} and boost::math::chebyshev_u(n, x){1} exceeds 4.27642e-47%  
  
*** 31 failures are detected in the test module "test_autodiff"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
```

---

## Comment 31

> Username: mborland  
> Created_at: 2023-06-21 07:16:24 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1600306745  

@jzmaddock This is now down to a single concept failure in `chebyshev`. It should be a simple `pow(cpp_dec_float, unsigned)`, but I am not quite sure what this issue is and I have tried casting to several types. Do you have any insight? The fail log is here: https://github.com/boostorg/math/actions/runs/5321186931/jobs/9635987566?pr=978#step:17:1839

---

## Comment 32

> Username: mborland  
> Created_at: 2023-06-21 14:33:36 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1600953985  

@jzmaddock With your commit this is now green across the board. Would you please give it another once over? Master branch closes next week for major changes.

---

## Comment 33

> Username: mborland  
> Created_at: 2023-06-26 09:43:51 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1607097995  

Ping @jzmaddock. Master closes for beta on Wednesday.

---

## Review 34 [Commented]

> Username: NAThompson  
> Created_at: 2023-06-26 16:05:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/978#pullrequestreview-1498904079  

📁 include/boost/math/special_functions/beta.hpp

```diff
 588 |          else
 589 |-             result *= pow(cgh / bgh, b - 0.5f);
 589 |+             result *= pow(cgh / bgh, T(b - 0.5f));
```

> Username: NAThompson  
> Created_at: 2023-06-26 16:05:02 UTC  
> Url: https://github.com/boostorg/math/pull/978#discussion_r1242429789  

Nitpick: Since you're casting to T, should this still be `-0.5f`?


---

## Review 35 [Commented]

> Username: NAThompson  
> Created_at: 2023-06-26 16:06:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/978#pullrequestreview-1498906315  

📁 include/boost/math/special_functions/detail/bessel_jy_series.hpp

```diff
 238 |    {
 239 |-       auto p = static_cast<T>(pow(z / 2, n));
 239 |+       auto p = static_cast<T>(pow(z / 2, T(n)));
```

> Username: NAThompson  
> Created_at: 2023-06-26 16:06:20 UTC  
> Url: https://github.com/boostorg/math/pull/978#discussion_r1242431176  

pow(Real, int) is significantly faster than pow(Real, Real), so isn't this a pessimization?

> Username: mborland  
> Created_at: 2023-06-26 16:09:49 UTC  
> Url: https://github.com/boostorg/math/pull/978#discussion_r1242435073  

Pow(<stdfloat>, int) results in a Stack Overflow because of a promotion bug in GCC. Since it's C++23 I should be able to hide this behind if constexpr.


---

## Review 36 [Commented]

> Username: NAThompson  
> Created_at: 2023-06-26 16:06:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/978#pullrequestreview-1498906871  

📁 include/boost/math/special_functions/detail/hypergeometric_pFq_checked_series.hpp

```diff
 148 |               std::pair<Real, Real> r = hypergeometric_pFq_checked_series_impl(aj, bj, Real(1 / z), pol, termination, log_scale);
 149 |-               Real mul = pow(-z, -*aj.begin());
 149 |+               Real mul = pow(-z, Real(-*aj.begin()));
```

> Username: NAThompson  
> Created_at: 2023-06-26 16:06:39 UTC  
> Updated_at: 2023-06-26 16:06:40 UTC  
> Url: https://github.com/boostorg/math/pull/978#discussion_r1242431514  

Same pow comment here . .


---

## Comment 37

> Username: mborland  
> Created_at: 2023-06-28 06:02:15 UTC  
> Url: https://github.com/boostorg/math/pull/978#issuecomment-1610799393  

The one CI failure left on this is a network error. I am going to merge this in because master closes for major changes today.

---
