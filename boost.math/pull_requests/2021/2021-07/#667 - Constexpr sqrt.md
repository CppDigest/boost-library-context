# #667 Constexpr sqrt [Merged]

> Username: mborland  
> Created at: 2021-07-20 19:15:35 UTC  
> Updated at: 2021-08-11 18:37:13 UTC  
> Merged at: 2021-08-11 18:17:31 UTC  
> Closed at: 2021-08-11 18:17:31 UTC  
> Url: https://github.com/boostorg/math/pull/667  

A constexpr (C++11 definition) implementation of sqrt with associated helper functions. Will continue to work through the rest of cmath.

---

## Comment 1

> Username: mborland  
> Created_at: 2021-07-22 17:50:23 UTC  
> Url: https://github.com/boostorg/math/pull/667#issuecomment-885099581  

@NAThompson I think this is ready for review. Needed to fix some small CI errors.

---

## Review 2 [Commented]

> Username: NAThompson  
> Created_at: 2021-07-22 17:53:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/667#pullrequestreview-713080149  

📁 doc/sf/ccmath.qbk

```diff
  29 |+ 
  30 |+         template <typename Z>
  31 |+         inline constexpr double sqrt(Real x);
```

> Username: NAThompson  
> Created_at: 2021-07-22 17:53:07 UTC  
> Updated_at: 2021-07-22 17:53:08 UTC  
> Url: https://github.com/boostorg/math/pull/667#discussion_r675039823  

Typo here: Should be `sqrt(Z x);`


---

## Review 3 [Commented]

> Username: NAThompson  
> Created_at: 2021-07-22 17:53:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/667#pullrequestreview-713081853  

📁 doc/sf/ccmath.qbk

```diff
  42 |+ [heading Description]
  43 |+ 
  44 |+ The following functions require C++17 or greater. Placeholder.
```

> Username: NAThompson  
> Created_at: 2021-07-22 17:53:57 UTC  
> Url: https://github.com/boostorg/math/pull/667#discussion_r675040758  

Let's just do everything in C++17. This will keep us from being constrained if we need better functionality. Surveys indicate that basically nobody is on C++14.


---

## Review 4 [Commented]

> Username: NAThompson  
> Created_at: 2021-07-22 17:55:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/667#pullrequestreview-713083431  

📁 include/boost/math/ccmath/isnan.hpp

```diff
  12 |+ inline constexpr bool isnan(T x)
  13 |+ {
  14 |+     return x != x;
```

> Username: NAThompson  
> Created_at: 2021-07-22 17:55:42 UTC  
> Updated_at: 2021-07-22 17:55:43 UTC  
> Url: https://github.com/boostorg/math/pull/667#discussion_r675043116  

Have you tested with a bunch of compiler flags? e.g., `-ffast-math -O3 -march=native -fno-finite-math-only`?


---

## Review 5 [Commented]

> Username: NAThompson  
> Created_at: 2021-07-22 17:56:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/667#pullrequestreview-713084315  

📁 include/boost/math/ccmath/sqrt.hpp

```diff
  14 |+ #include <boost/math/ccmath/isinf.hpp>
  15 |+ 
  16 |+ namespace boost { namespace math { namespace ccmath {
```

> Username: NAThompson  
> Created_at: 2021-07-22 17:56:44 UTC  
> Updated_at: 2021-07-22 18:02:08 UTC  
> Url: https://github.com/boostorg/math/pull/667#discussion_r675044453  

I'd do `namespace boost::math::ccmath` just so if you need to make backwards incompatible changes in C++17 mode you don't surprise anyone.


---

## Review 6 [Commented]

> Username: NAThompson  
> Created_at: 2021-07-22 17:57:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/667#pullrequestreview-713085284  

📁 test/isnan_test.cpp

```diff
  34 |+     #ifndef BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS
  35 |+     test<long double>();
  36 |+     #endif
```

> Username: NAThompson  
> Created_at: 2021-07-22 17:57:48 UTC  
> Url: https://github.com/boostorg/math/pull/667#discussion_r675045809  

Can you get this to work with libquadmath/float128 as well?


---

## Review 7 [Commented]

> Username: NAThompson  
> Created_at: 2021-07-22 17:58:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/667#pullrequestreview-713086234  

📁 test/sqrt_test.cpp

```diff
  22 |+ 
  23 |+     Real known_val = std::sqrt(Real(2));
  24 |+     BOOST_TEST(abs(test_val - known_val) < tol);
```

> Username: NAThompson  
> Created_at: 2021-07-22 17:58:49 UTC  
> Updated_at: 2021-07-22 17:59:19 UTC  
> Url: https://github.com/boostorg/math/pull/667#discussion_r675047110  

I would highly recommend using my `math_unit_test.hpp` for this one. The expectation will be that this will be half-ulp accurate. Use:  
  
`CHECK_ULP_CLOSE(test_val, known_val, 0)`


---

## Comment 8

> Username: NAThompson  
> Created_at: 2021-07-22 18:03:25 UTC  
> Updated_at: 2021-07-22 18:12:14 UTC  
> Url: https://github.com/boostorg/math/pull/667#issuecomment-885123079  

How are you verifying that these are actually being computed at compile time? Could you provide some godbolt links?  
  
I do wonder how this is achieved without using compiler facilities. . .   
  
Also; how should this interact with compiler flags? https://stackoverflow.com/questions/57894032/floating-point-comparison-with-quiet-nan-on-gcc-with-ffast-math-always-yields  
  
It appears there will be different behavior at runtime vs compile time which could be very surprising.  
  
Finally, isn't there already an `isnan`, `sqrt` in boost.math? Couldn't we `constexpr` those? @jzmaddock might know more on that one . . .

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2021-07-23 15:51:31 UTC  
> Url: https://github.com/boostorg/math/pull/667#issuecomment-885733393  

Thanks @NAThompson for giving this the once over - some good comments there.  
  
>Finally, isn't there already an isnan, sqrt in boost.math? Couldn't we constexpr those?  
  
Sadly not.  They examine the bit-pattern of the float, which makes them immune to -ffast-math like optimisations, but the type punning is illegal in a constexpr context.  
  
> How are you verifying that these are actually being computed at compile time? Could you provide some godbolt links?  
  
I notice the tests have a mix of runtime and compile tests, only those that explicitly create a constexpr value will be evaluated at compile time.  
  
In fact testing constexpr functions is actually really hard, arguably impossibly so, we need to:  
  
* Call the function in a constexpr context, otherwise we don't actually test that the function is constexpr safe, and....  
* If the function has multiple branches, we should ideally have one constexpr test for each branch... otherwise unevaluated branches are simply discarded (when in a constexpr context), and could contain all manner of strange illegal code and we would never know.  Note that compiling and testing all the branches at runtime is NOT equivalent, since some legal runtime constructs are not legal when consstexpr :(  
  
>Also; how should this interact with compiler flags? https://stackoverflow.com/questions/57894032/floating-point-comparison-with-quiet-nan-on-gcc-with-ffast-math-always-yields  
>  
>It appears there will be different behavior at runtime vs compile time which could be very surprising.  
  
Ugh, don't get me started!  In theory, when in a constexpr context we should get more consistent behaviour, which is immune to the vagaries of strange compiler flag or architecture behaviour.  But without a *very* careful reading of the standard, I wouldn't want to be definitive about that, it wouldn't surprise me if there aren't still dark underspecified corners lurking somewhere...  
  
This also brings up a slightly more philosophical design question:  How should these functions behave when called NOT in a constexpr context?  
  
I see three options:  
  
1) Call the std lib function, as in:  
  
```  
template <class F>  
inline constexpr F foo(F f)  
{  
   if(!std::is_constant_evaluated()) return std::foo(f);  
   else return constexpr_foo(f);  
}  
```  
  
2) Call the constexpr implementation - this is what happens currently.  
  
3) Make it an error, either by marking the function as `consteval`, or with a `if(!std::is_constant_evaluated()) static_assert(0, "Function foo called in non-constexpr context")` in the function body.  
  
BTW, while `consteval` requires building in C++20 mode, `std::is_constant_evaluated` can be simulated in C++11 mode and later on all recent compilers, see: https://github.com/boostorg/multiprecision/blob/d3c9d4a479a86b87b2fa56ddf494887e11018f31/include/boost/multiprecision/detail/number_base.hpp#L96-L132  
  
Personally, I don't much like (2), as we potentially end up calling an inferior runtime implementation by mistake (sqrt is a good example of this - it's a single instruction on most architectures, but not if we call an all C++ constexpr version).  
  
And finally... I note that implementing the whole std lib in this way is a very significant piece of work, that doesn't mean we can't concentrate on a few simple and frequently used functions in the first place.

---

## Comment 10

> Username: mborland  
> Created_at: 2021-07-24 13:02:15 UTC  
> Url: https://github.com/boostorg/math/pull/667#issuecomment-886050625  

As always I appreciate the comments @jzmaddock and @NAThompson. This latest couple commits should get after most of the comments.  
  
1) Replaced a lot of the instance of `BOOST_TEST` with `static_assert`. This should force a `constexpr` evaluation.   
2) Lifted the definition of `IS_CONSTANT_EVALUATED` from multiprecision. Now the functions will use the std lib for non-constexpr branches  
3) Everything has been converted to C++17. This should help in the future because the definition of `constexpr` is a lot more liberal than it was in C++11.

---

## Review 11 [Commented]

> Username: NAThompson  
> Created_at: 2021-07-24 16:24:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/667#pullrequestreview-714245479  

📁 include/boost/math/ccmath/isinf.hpp

```diff
  17 | {
  16 |-     return x == std::numeric_limits<T>::infinity() || -x == std::numeric_limits<T>::infinity();
  18 |+     if(BOOST_MATH_IS_CONSTANT_EVALUATED(x))
```

> Username: NAThompson  
> Created_at: 2021-07-24 16:24:13 UTC  
> Url: https://github.com/boostorg/math/pull/667#discussion_r676018126  

Shouldn't this be an `if constexpr`?

> Username: mborland  
> Created_at: 2021-07-24 16:31:38 UTC  
> Url: https://github.com/boostorg/math/pull/667#discussion_r676018876  

I thought the same thing. Here is the [SO answer.](https://stackoverflow.com/questions/54251530/stdis-constant-evaluated-behavior)

> Username: jzmaddock  
> Created_at: 2021-07-24 17:06:24 UTC  
> Url: https://github.com/boostorg/math/pull/667#discussion_r676022333  

Yeah, it's a weird corner of the language for sure, but it must absolutely not be constexpr!

> Username: NAThompson  
> Created_at: 2021-07-24 20:42:05 UTC  
> Url: https://github.com/boostorg/math/pull/667#discussion_r676042561  

Wowsers, well good thing we have some real programmers eyes on this one! I would've Pied Piper'd us on this one. . .


---

## Comment 12

> Username: mborland  
> Created_at: 2021-07-25 12:30:45 UTC  
> Url: https://github.com/boostorg/math/pull/667#issuecomment-886195133  

Opened https://github.com/boostorg/multiprecision/pull/348 in an effort to get this working with `float128` as requested by @NAThompson. CI is clean with the builtin floating point types.

---

## Comment 13

> Username: mborland  
> Created_at: 2021-07-31 09:31:03 UTC  
> Url: https://github.com/boostorg/math/pull/667#issuecomment-890319373  

@jzmaddock Can you take a look at this [one outstanding failure](https://drone.cpp.al/boostorg/math/331/37/2)? It looks like g++8 is attempting to compile the branch that is not consteval. It looks like GCC8 has [ __builtin_constant_p](https://gcc.gnu.org/onlinedocs/gcc-8.4.0/gcc/Other-Builtins.html) but not `__builtin_constant_evaluated`. Is this worth restricting to g++9 and greater, or do you know of another workaround?

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2021-07-31 12:33:10 UTC  
> Url: https://github.com/boostorg/math/pull/667#issuecomment-890341693  

OK, the issue is that `BOOST_MATH_IS_CONSTANT_EVALUATED(x)` is called with x not a builtin type and ` __builtin_constant_p` only works for builtin types.  In other words it's a poor-man's substitute for `__builtin_constant_evaluated` which mostly works, but not in this case :(  
  
I think I'd just disable testing float128 when `__builtin_constant_evaluated` is not available.

---

## Comment 15

> Username: mborland  
> Created_at: 2021-08-01 09:24:48 UTC  
> Url: https://github.com/boostorg/math/pull/667#issuecomment-890484027  

Pending further review this is now clean on CI.

---

## Comment 16

> Username: mborland  
> Created_at: 2021-08-10 18:52:07 UTC  
> Url: https://github.com/boostorg/math/pull/667#issuecomment-896232934  

Ping. Any other changes required for this one?

---

## Comment 17

> Username: jzmaddock  
> Created_at: 2021-08-11 18:17:24 UTC  
> Url: https://github.com/boostorg/math/pull/667#issuecomment-897046271  

Looks good to me, merging...

---

## Comment 18

> Username: jzmaddock  
> Created_at: 2021-08-11 18:19:16 UTC  
> Url: https://github.com/boostorg/math/pull/667#issuecomment-897047519  

fabs/abs, plus isfinite/isnormal would be trivial to add next?

---

## Comment 19

> Username: mborland  
> Created_at: 2021-08-11 18:37:13 UTC  
> Url: https://github.com/boostorg/math/pull/667#issuecomment-897059267  

> fabs/abs, plus isfinite/isnormal would be trivial to add next?  
  
Just opened a PR for fabs/abs. isfinite/isnormal will be next.

---
