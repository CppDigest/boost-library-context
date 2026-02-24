# #1012 More efficient bisection for 1D Newton root finder [Open]

> Username: ryanelandt  
> Created at: 2023-08-05 00:42:04 UTC  
> Updated at: 2023-09-09 21:59:26 UTC  
> Url: https://github.com/boostorg/math/pull/1012  

This is chunk #4 for #1000. Also closes #1008.  
  
This PR adds tools that enable efficient bisection for the 1D Newton root finder.  
  
### The Situation  
  
The 1D Newton root finder has a bisection fallback mechanism. If one of the bounds is large (i.e., `std::numeric_limits::max()`), naively bisecting the bounds is slow. For example, for type `double`, bisecting from `1.79769e+308` to `1` takes `1024` iterations. The situation is even worse for types with more precision. When the Newton solver runs out of (e.g., 200) iterations during bisection, the solver declares success even if far from a root. Again, see issue #1008.  
  
### Functionality  
  
What's the maximum number of bisections needed to root find a `double`? A `double` is 64 bits. So the maximum number of iterations needed is 64. Efficient bisection is bisection in bit space. An interesting consequence of bisection in bitspace, is that infinity is a bisectable bound.  
  
For types that do not conform to the standard (i.e., IEEE 754), an approximate version of the bisection algorithm above is used. The approximate version supports bisection with infinity if the type is specialized (i.e., if `std::numeric_limits<T>::is_specialized = true`).  
  
### Fun Facts  
  
The mean of `0` and `infinity` is:  
- `infinity` (arithmetic mean)  
- `NaN` (geometric mean)  
- 1.5 (bitspace mean for `double` and `float`)  
- 1.0 (bitspace mean for `long double` using the approximate algorithm)

---

## Review 1 [Changes requested]

> Username: mborland  
> Created_at: 2023-08-07 14:04:12 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/math/pull/1012#pullrequestreview-1565367597  

📁 include/boost/math/tools/roots.hpp

```diff
 288 |+ 
 289 |+    public:
 290 |+       static T solve(T x, T X) {
```

> Username: mborland  
> Created_at: 2023-08-07 13:45:35 UTC  
> Updated_at: 2023-08-07 14:04:12 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1285890166  

Please rename these parameters. Reading this with only capitalization difference is not easy.

> Username: ryanelandt  
> Created_at: 2023-08-07 19:58:10 UTC  
> Updated_at: 2023-08-07 19:58:11 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1286332943  

I changed `X` to `y`.

---

📁 include/boost/math/tools/roots.hpp

```diff
 319 |+    private:
 320 |+       static_assert(!std::is_same<T, float>::value, "Need to use Midpoint754 solver when T is float");
 321 |+       static_assert(!std::is_same<T, double>::value, "Need to use Midpoint754 solver when T is double");
```

> Username: mborland  
> Created_at: 2023-08-07 13:46:33 UTC  
> Updated_at: 2023-08-07 14:04:13 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1285891307  

Should there be an assertion for long double as well?

> Username: ryanelandt  
> Created_at: 2023-08-07 19:59:12 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1286333804  

I added an explanation as to why `long double` does not use the `Midpoint754` solver.

> Username: mborland  
> Created_at: 2023-08-08 13:12:38 UTC  
> Updated_at: 2023-08-08 13:15:32 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1287099783  

A guess there are a couple cases:  
  
1. On some systems (e.g. Windows and MacOS) `long doubles` are just aliases to `double`. When long doubles are 64-bits we could just cast to double and use `Midpoint754` to speed things up.  
  
2. On x86_64 linux systems you have the 80-bit `long double`. Normally you have `unsigned __int128` but you'd have to shift the bits around to cast it back properly. Likely not worth the magic required.  
  
3. On systems with IEEE 128-bit long doubles (e.g. ARM and S390x) you have access to `unsigned __int128` which should generally work. We have both of the aforementioned architectures running natively in the CI system.  
  
[Here](https://github.com/boostorg/math/blob/develop/include/boost/math/ccmath/signbit.hpp#L94) is where I have defined macros before for determining the size of long double.

> Username: ryanelandt  
> Created_at: 2023-08-18 19:45:53 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1298814970  

> On some systems (e.g. Windows and MacOS) `long doubles` are just aliases to `double`. When long doubles are 64-bits we could just cast to double and use `Midpoint754` to speed things up.  
  
These systems will use the specialization for `double`.  
   
> On x86_64 linux systems you have the 80-bit `long double`. Normally you have `unsigned __int128` but you'd have to shift the bits around to cast it back properly. Likely not worth the magic required.  
  
I tried to make this work. Starting with `numeric_limits<long double>::max()` and adding 1 bit gives `NaN`, not `Inf` as it does for `float`, `double` and `__float128`. It's just not meant to be.  
  
> On systems with IEEE 128-bit long doubles (e.g. ARM and S390x) you have access to `unsigned __int128` which should generally work. We have both of the aforementioned architectures running natively in the CI system.  
  
I got this to work for both `_float128` and `boost::multiprecision::float128` (wrapper for `__float128`). The implementation does not require `libquadmath`.

---

📁 include/boost/math/tools/roots.hpp

```diff
 342 |+ 
 343 |+       struct EqInf {
 344 |+          EqInf(T x) { BOOST_MATH_ASSERT(x == static_cast<T>(std::numeric_limits<double>::infinity()) && "x must be infinity."); }
```

> Username: mborland  
> Created_at: 2023-08-07 13:47:46 UTC  
> Updated_at: 2023-08-07 14:04:13 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1285892804  

```diff  
-         EqInf(T x) { BOOST_MATH_ASSERT(x == static_cast<T>(std::numeric_limits<double>::infinity()) && "x must be infinity."); }  
+         EqInf(T x) { BOOST_MATH_ASSERT(x == std::numeric_limits<T>::infinity() && "x must be infinity."); }  
```

> Username: ryanelandt  
> Created_at: 2023-08-07 20:02:53 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1286336457  

This awkwardness was there to support types (e.g., boost::math::concepts::real_concept) for which infinity exists, but std::numeric_limits<T>::infinity() returns 0. I switched to using `boost::math::isfinite` when checking for infinity at most places.

---

📁 include/boost/math/tools/roots.hpp

```diff
 349 |+          PosFinite(T x) : x_(x) {
 350 |+             BOOST_MATH_ASSERT(0 < x && "x must be positive.");
 351 |+             BOOST_MATH_ASSERT(x < std::numeric_limits<float>::infinity() && "x must be less than infinity.");
```

> Username: mborland  
> Created_at: 2023-08-07 13:49:37 UTC  
> Updated_at: 2023-08-07 14:04:13 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1285895041  

```diff  
-            BOOST_MATH_ASSERT(x < std::numeric_limits<float>::infinity() && "x must be less than infinity.");  
+            BOOST_MATH_ASSERT(x < std::numeric_limits<T>::infinity() && "x must be less than infinity.");  
```

---

📁 include/boost/math/tools/roots.hpp

```diff
 393 |+       // Zero unknowns
 394 |+       template <typename U = T>
 395 |+       static typename std::enable_if<std::numeric_limits<U>::is_specialized, T>::type
```

> Username: mborland  
> Created_at: 2023-08-07 13:52:43 UTC  
> Updated_at: 2023-08-07 14:04:13 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1285899005  

I would also add `std::numeric_limits<U>::has_infinity` since you are relying on comparison to infinity. Then you can also switch all of the infinities above like the suggested boxes. I am not sure you can reason about the result of double infinity compared to a UDT without infinity.

---

📁 include/boost/math/tools/roots.hpp

```diff
 398 |+       }
 399 |+       template <typename U = T>
 400 |+       static typename std::enable_if<!std::numeric_limits<U>::is_specialized, T>::type
```

> Username: mborland  
> Created_at: 2023-08-07 13:54:20 UTC  
> Updated_at: 2023-08-07 14:04:13 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1285900982  

`std::numeric_limits<T>::has_signaling_NaN`.

---

📁 include/boost/math/tools/roots.hpp

```diff
 400 |+       static typename std::enable_if<!std::numeric_limits<U>::is_specialized, T>::type
 401 |+       do_solve(PosFinite x, EqInf X) {
 402 |+           BOOST_MATH_ASSERT(false && "infinite bounds support requires specialization.");
```

> Username: mborland  
> Created_at: 2023-08-07 13:57:36 UTC  
> Updated_at: 2023-08-07 14:04:13 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1285905351  

```diff  
-          BOOST_MATH_ASSERT(false && "infinite bounds support requires specialization.");  
+          BOOST_MATH_ASSERT_MSG(false, "infinite bounds support requires specialization.");  
```

---

📁 include/boost/math/tools/roots.hpp

```diff
 414 |+             if (min != 0) { return min; }
 415 |+ 
 416 |+             BOOST_MATH_ASSERT(false && "denorm_min and min are both zero.");
```

> Username: mborland  
> Created_at: 2023-08-07 13:57:55 UTC  
> Updated_at: 2023-08-07 14:04:13 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1285905791  

```diff  
-            BOOST_MATH_ASSERT(false && "denorm_min and min are both zero.");  
+            BOOST_MATH_ASSERT_MSG(false, "denorm_min and min are both zero.");  
```

---

📁 include/boost/math/tools/roots.hpp

```diff
 405 |+ 
 406 |+       template <typename U = T>
 407 |+       static typename std::enable_if<std::numeric_limits<U>::is_specialized, U>::type
```

> Username: mborland  
> Created_at: 2023-08-07 13:58:55 UTC  
> Updated_at: 2023-08-07 14:04:13 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1285907018  

You may be able to get away without checking `has_denorm` because it was deprecated in C++23

> Username: ryanelandt  
> Created_at: 2023-08-07 23:42:05 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1286468370  

We want to be able to bisect with denormals because the solution to the root finding problem could be a denormal number.  
  
Reading the [depreciation document](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2022/p2614r2.pdf), it seems like the depreciation of `has_denorm` has a lot to do with it being a `static constexpr`. This is problematic because the actual behavior can change at runtime with e.g., `_MM_SET_FLUSH_ZERO_MODE`.  
  
I think we can actually delete `std::numeric_limits::min()` because `denorm_min` returns `numeric_limits::min()` if denormals are off. Does that seen sensible?

> Username: mborland  
> Created_at: 2023-08-08 13:15:02 UTC  
> Updated_at: 2023-08-08 13:15:32 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1287102732  

>   
> I think we can actually delete `std::numeric_limits::min()` because `denorm_min` returns `numeric_limits::min()` if denormals are off. Does that seen sensible?  
  
I think so. Since you are already check for `is_specialized` you should avoid ever getting 0 instead of a finite minimum value.

> Username: ryanelandt  
> Created_at: 2023-08-18 19:48:46 UTC  
> Updated_at: 2023-08-18 19:48:47 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1298817001  

> I think so. Since you are already check for `is_specialized` you should avoid ever getting 0 instead of a finite minimum value.  
  
https://github.com/boostorg/multiprecision/pull/562


📁 test/test_roots.cpp

```diff
 721 |+ }
 722 |+ 
 723 |+ void test_bisect_all_cases() {
```

> Username: mborland  
> Created_at: 2023-08-07 14:02:21 UTC  
> Updated_at: 2023-08-07 14:04:13 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1285911540  

Can you add `std::float32_t` and `std::float64_t` from C++23's `<stdfloat>`. You can test `__has_include(<stdfloat>)` and then the macros that each of the types define. We have g++13 in the CI system to cover those.

> Username: ryanelandt  
> Created_at: 2023-08-07 20:05:27 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1286338212  

I think I was able to do this. Let me know if I didn't do the right thing.

---

📁 test/test_roots.cpp

```diff
 766 |+    test_count();
 767 |+    test_bisect_all_cases();
 768 |+ #if 0
```

> Username: mborland  
> Created_at: 2023-08-07 14:02:54 UTC  
> Updated_at: 2023-08-07 14:04:13 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1285912201  

Do the following tests fail with this new method?

> Username: ryanelandt  
> Created_at: 2023-08-07 20:05:53 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1286338531  

I accidentally left this in. Good catch.


---

## Comment 2

> Username: mborland  
> Created_at: 2023-08-07 14:05:53 UTC  
> Url: https://github.com/boostorg/math/pull/1012#issuecomment-1667926199  

@NAThompson I think it would be worthwhile for you to take a look at this as well, availability dependent of course.

---

## Comment 3

> Username: ryanelandt  
> Created_at: 2023-08-07 20:43:55 UTC  
> Url: https://github.com/boostorg/math/pull/1012#issuecomment-1668553544  

@mborland thank you for the review. Please let me know if there are additional changes.

---

## Comment 4

> Username: ryanelandt  
> Created_at: 2023-08-18 20:01:58 UTC  
> Url: https://github.com/boostorg/math/pull/1012#issuecomment-1684379966  

I added IEEE754 bisection support for `__float128`. I also updated the reason why the 80 bit `long double` cannot use the IEEE754 solver. Please take another look when you can.

---

## Comment 5

> Username: ryanelandt  
> Created_at: 2023-08-22 01:27:23 UTC  
> Url: https://github.com/boostorg/math/pull/1012#issuecomment-1687276208  

I realized that the code overlap between what is needed for find the midpoint between two IEEE754 floats and the code needed to find the fast float distance between two IEEE754 floats is high. I'm in the process of refactoring this functionality into a few classes that when operated on allow the following operations to be performed in a few lines of code: `midpoint`, `float_distance`, and `float_advance`. I'm putting this PR on hold until I can get this done.

---

## Comment 6

> Username: ryanelandt  
> Created_at: 2023-08-22 22:52:47 UTC  
> Url: https://github.com/boostorg/math/pull/1012#issuecomment-1689027323  

The introduced tools in `ieee754_linear` lower the programming effort required to perform bit-themed operations on IEEE754 types that include: `float`, `double`, and `__float128`. Runtime-detected value of denorm support are used in calculations. These tools were used to calculate the midpoint in bitspace for the above mentioned types. The tools can be applied in a slightly different way to calculate fast float distance:  
```c++  
      template <typename T, typename U>  
      static T fast_float_distance(T x, T y, boost::math::tools::detail::ieee754_linear::Layout_IEEE754Linear<T, U>) {  
         using boost::math::tools::detail::ieee754_linear::BitsInflated;  
         using boost::math::tools::detail::ieee754_linear::Denormflator;  
         const auto df = Denormflator<T, U>();  // Calculates if `has_denorm` is true at this instant  
  
         // Step 1 -- Calculate inflated representations  
         const BitsInflated<T, U> y_inflated(y);  
         const BitsInflated<T, U> x_inflated(x);  
  
         // Step 2 -- Calculate deflated representations  
         const auto y_def = df.deflate(y_inflated);  
         const auto x_def = df.deflate(x_inflated);  
  
         // Step 3 -- Subtract the two deflated representations  
         const auto xy_def_distance = (y_def - x_def)  
  
         // Step 4 -- The distance between the deflated points is the fast float distance  
         return xy_def_distance.static_cast_int_value_to_float();  
      }  
```  
Or it can be used to perform the float advance operation:  
```c++  
      template <typename T, typename U>  
      static T float_advance(T x, int n, boost::math::tools::detail::ieee754_linear::Layout_IEEE754Linear<T, U>) {  
         using boost::math::tools::detail::ieee754_linear::BitsInflated;  
         using boost::math::tools::detail::ieee754_linear::Denormflator;  
         const auto df = Denormflator<T, U>();  // Calculates if `has_denorm` is true at this instant  
  
         // Step 1 -- Calculate inflated representation  
         const BitsInflated<T, U> x_inflated(x);  
  
         // Step 2 -- Calculate deflated representation  
         const auto x_def = df.deflate(x_inflated);  
  
         // Step 3 -- Move over n bits  
         const auto x_plus_n_def = x_def + n;  
  
         // Step 4 -- Inflate  
         const auto x_plus_n = df.inflate(x_plus_n_def);  
  
         // Step 5 -- Convert to float  
         return x_plus_n.reinterpret_as_float();  
      }  
```

---

## Comment 7

> Username: mborland  
> Created_at: 2023-08-23 13:21:22 UTC  
> Url: https://github.com/boostorg/math/pull/1012#issuecomment-1689955765  

If you are looking for a different quick way to calculate float distance @NAThompson showed me this trick: https://github.com/boostorg/math/pull/814/files#diff-45f56077fb4aa15ca1aca5025321e5c275fd20b3e161585a25442eaa2c7952a3R724

---

## Comment 8

> Username: ryanelandt  
> Created_at: 2023-08-23 13:41:00 UTC  
> Url: https://github.com/boostorg/math/pull/1012#issuecomment-1689987701  

The same idea of viewing floats in bitspace is used here. Seeing that trick used in #814 inspired this refactor. In addition, this  implementation attempts to:  
- augment calculations for denorm support as detected at runtime  
- operate on numbers with different signs  
- use template parameters to allow it to work for a variety of types (e.g., `std::float32_t` and `std::float64_t`, or even `boost::math::std_real_concept` if it's set up to emulate a float type).

---

## Review 9 [Commented]

> Username: jzmaddock  
> Created_at: 2023-08-31 15:19:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1012#pullrequestreview-1605015827  

📁 include/boost/math/tools/ieee754_linear.hpp

```diff
  15 |+ 
  16 |+ #ifdef BOOST_HAS_FLOAT128
  17 |+ #include <boost/multiprecision/float128.hpp>
```

> Username: jzmaddock  
> Created_at: 2023-08-31 15:19:30 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1311799758  

I think this breaks standalone mode as BOOST_HAS_FLOAR128 may be set but the multiprecision headers will not be available.  We also try to avoid mutual/cyclic dependencies like these.  What is the include actually used for?

> Username: ryanelandt  
> Created_at: 2023-08-31 17:57:16 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1312000748  

Good catch with the dependency. I've removed this include and replaced it with the following includes:  
```c++  
#include <boost/math/tools/config.hpp>  // For BOOST_MATH_FLOAT128_TYPE  
#include <boost/cstdfloat.hpp>  // For numeric_limits support for 128 bit floats  
```  
There may be a better way of including the needed files that I'm unaware of.

> Username: ryanelandt  
> Created_at: 2023-08-31 18:06:35 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1312011409  

This include, or one like it appears to be necessary in order to pass the `std_real_concept_check_128` test associated with the file `compile_test/std_real_concept_check.cpp`.

> Username: jzmaddock  
> Created_at: 2023-09-01 09:05:43 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1312777195  

Unless you've changed something in that test, it uses nothing from multiprecision at all so the include should not be needed.

> Username: ryanelandt  
> Created_at: 2023-09-01 12:08:51 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1312946506  

As stated in a previous comment, I've removed the header `#include <boost/multiprecision/float128.hpp>` and replaced it with `#include <boost/cstdfloat.hpp>  // For numeric_limits support for 128 bit floats`. The `std_real_concept_check_128` test needs for `std::numeric_limits` specializations to be defined for the `BOOST_MATH_FLOAT128_TYPE`. If this header is removed, then test will **not** compile. (I tried this)  
  
Why this occurs: the `std_real_concept_check_128` test makes the `std_real_concept` emulate a 128 bit floating point type. The tools in `ieee754_linear` recognize this as an IEEE 754 floating point type. The comment on line 133 goes on to say:   
```c++  
// NOTE: returns Layout_IEEE754Linear<BOOST_MATH_FLOAT128_TYPE, ...> instead of Layout_IEEE754Linear<T, ...>  
//       in order to work with non-trivial types that wrap trivial 128 bit floating point types.  
```

> Username: jzmaddock  
> Created_at: 2023-09-01 16:04:46 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1313219210  

OK, I see the issue, but I think that we're looking at this the wrong way:  
  
1) Not depending on boost/cstdfloat.hpp is a "good thing" because while that header is genuinely very useful, it's also rather fragile to changes in GCC releases.  
2) I think the assumptions in `IsFloat128` (and maybe elsewhere) are wrong: just because a type has the correct size and properties, does NOT mean it's a trivial wrapper around a native type.  It could for example be a pointer (or two) to the actual implementation which just happens to have the same size.  mpfr_t is 24 bytes on my system so doesn't quite qualify, but if I'm reading it correctly, MPFR could be configured in a such a way that it was a 16 bye / 128 bit type which was set up to emulate a 128 bit float.  Another way of looking at this is that std_real_concept_type and the other concept checking types should be viewed as black boxes - their purpose is to emulate to some degree or another, some other random type about which we know nothing that the library may be instantiated with in the future.  You may be able to actually see their internals, but you really shouldn't look ;)  
  
So I would change  
  
```  
   class IsFloat128 {  
   public:  
      template <typename T>  
      static constexpr bool value() {  
         return std::numeric_limits<T>::is_iec559 &&  
                std::numeric_limits<T>::radix == 2 &&  
                std::numeric_limits<T>::digits == 113 &&  // Mantissa has 112 bits + 1 implicit bit  
                sizeof(T) == 16;  
      }  
   };  
```  
  
To maybe:  
  
```  
   class IsFloat128 {  
   public:  
      template <typename T>  
      static constexpr bool value() {  
         return std::numeric_limits<T>::is_iec559 &&  
                std::numeric_limits<T>::radix == 2 &&  
                std::numeric_limits<T>::digits == 113 &&  // Mantissa has 112 bits + 1 implicit bit  
#ifdef BOOST_HAS_FLOAT128  
                (std::is_floating_Point<T>::value || std::is_same<__float128, T>::value)  
#else  
                std::is_floating_Point<T>::value  
#endif  
      }  
   };  
```  
  
Likewise for the other IsFloatXX classes.  
  
Then change:  
  
```  
   template <typename T, typename U>  
   class Layout_IEEE754Linear {  
      static_assert(std::numeric_limits<T>::is_iec559, "Type must be IEEE 754 floating point.");  
      static_assert(std::is_unsigned<U>::value, "U must be an unsigned integer type.");  
      static_assert(sizeof(T) == sizeof(U), "Type and uint size must be the same.");  
  
   public:  
      using type_float = T;  // The linear floating point type  
   };  
```  
  
To:  
  
```  
   template <typename T, typename U>  
   class Layout_IEEE754Linear {  
      static_assert(std::numeric_limits<T>::is_iec559   
#ifdef BOOST_HAS_FLOAT128  
       || std::is_same<T, __float128>::value  
#endif   
       , "Type must be IEEE 754 floating point.");  
      static_assert(std::is_unsigned<U>::value, "U must be an unsigned integer type.");  
      static_assert(sizeof(T) == sizeof(U), "Type and uint size must be the same.");  
  
   public:  
      using type_float = T;  // The linear floating point type  
   };  
```  
  
Which would allow the header dependency to be removed as well.  I do appreciate it's less clean, but I suspect it's a safer option in the long run!

> Username: ryanelandt  
> Created_at: 2023-09-01 17:30:42 UTC  
> Updated_at: 2023-09-01 17:37:00 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1313326709  

> Not depending on boost/cstdfloat.hpp is a "good thing" because while that header is genuinely very useful, it's also rather fragile to changes in GCC releases.  
  
Does the following change make things less fragile? Changing the include from:  
```c++  
#include <boost/cstdfloat.hpp>  
```  
to   
```c++  
#if defined(BOOST_HAS_INT128) && defined(BOOST_HAS_FLOAT128)  
#if !defined(BOOST_CSTDFLOAT_NO_LIBQUADMATH_LIMITS)  
  #include <boost/math/cstdfloat/cstdfloat_limits.hpp>  
#endif  
#endif  
```  
  
>I think the assumptions in IsFloat128 (and maybe elsewhere) are wrong: just because a type has the correct size and properties, does NOT mean it's a trivial wrapper around a native type. It could for example be a pointer (or two) to the actual implementation which just happens to have the same size. [...] MPFR could be configured in a such a way [...] to emulate a 128 bit float.   
  
For the `value()` function of any of the `IsFloat` classes to return true, the following statement needs to be true:  
```  
std::numeric_limits<T>::is_iec559  
```  
The 32, 64, and 128 bit IEEE floats have a specific layout consisting of a sign bit, an exponent, and a fraction. A number type containing two pointers will not meet this standard. Skimming the MPFR codebase, this library is clearly set up with an awareness of IEEE 754 arithmetic. They don't appear to specialize numeric limits, and therefore don't set `std::numeric_limits<T>::is_iec559 = true`. I think the check that `std::numeric_limits<T>::is_iec559 = true` provides reasonable protection against accidental use of this functionality.

> Username: jzmaddock  
> Created_at: 2023-09-09 10:27:46 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1320534192  

Yup apologies, you're correct that `std::numeric_limits<T>::is_iec559` should provide a reasonable check given that that std defines a binary layout.  
  
I would still prefer to avoid the cstdfloat includes if possible though - and we *know* that __float128 is an iec559 compatible type, so including it for that one case is overkill when we can just use `is_same<T, __float128>::value` ?  
  
Other than that and one minor nit about <stdfloat> inclusion this looks good to go.

> Username: ryanelandt  
> Created_at: 2023-09-09 16:09:16 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1320599110  

I don't see a good way to do this. The code operates by duck typing a float type `T`. For example, something is determined to be a 32 bit binary floating point type based on the value of `IsFloat32::value<T>()`. Something is determined to be such a type if:   
```c++  
std::numeric_limits<T>::is_iec559 &&  
std::numeric_limits<T>::radix == 2 &&  
std::numeric_limits<T>::digits == 24 &&  // Mantissa has 23 bits + 1 implicit bit  
sizeof(T) == 4;  
```  
evaluates to true. If any of the four conditions above cannot be evaluated, then the code will fail to compile. The include is needed for it to compile, even if the method were to be unused.  
  
----------  
  
The alternative way of structuring this code is to manually match up binary floating point types with the appropriate methods, but this is messy and tends to miss valid use cases. `is_same<T, __float128>::value` might work on GCC, but what about an Intel compiler?  
  
-----------  
  
I'd like to avoid this include here too as it shouldn't be necessary. Perhaps the issue occurs here:  
https://github.com/boostorg/math/blob/09d82da6e9884da86495279b331ff038dd1330b8/include/boost/math/tools/config.hpp#L399-L415  
where a 128 bit floating point type is defined without defining `std::numeric_limits` for that type.


---

## Review 10 [Commented]

> Username: jzmaddock  
> Created_at: 2023-08-31 15:22:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1012#pullrequestreview-1605022410  

📁 include/boost/math/tools/roots.hpp

```diff
  29 |+ #ifdef BOOST_HAS_FLOAT128
  30 |+ #include <boost/multiprecision/float128.hpp>
  31 |+ using boost::multiprecision::float128;
```

> Username: jzmaddock  
> Created_at: 2023-08-31 15:22:24 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1311803576  

As above, we shouldn't be forcing multiprecision headers to be included here, plus the using declaration at global header scope is evil ;)

> Username: ryanelandt  
> Created_at: 2023-08-31 17:58:52 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1312003707  

This header and using declaration was unneeded. Good catch.


---

## Review 11 [Commented]

> Username: jzmaddock  
> Created_at: 2023-09-09 10:31:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1012#pullrequestreview-1618610616  

📁 test/ieee754_linear_test.cpp

```diff
  18 |+ #endif
  19 |+ 
  20 |+ #if __has_include(<stdfloat>)
```

> Username: jzmaddock  
> Created_at: 2023-09-09 10:31:35 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1320534704  

It's unfortunate, but __has_include is an insufficient check, because it may pass even when the translation mode is not C++23, so we will need to check the value of BOOST_CXX_VERSION as well (unfortunately __cplusplus is non-reliable for MSVC).  I yes, must get around to adding proper Boost.Config macros and tests for these new C++23 headers sometime!

> Username: ryanelandt  
> Created_at: 2023-09-09 15:08:23 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1320587418  

The include pattern:  
```c++  
#if __has_include(<stdfloat>)  
#  include <stdfloat>  
#endif  
```  
occurs in `include/boost/math/tools/promotion.hpp`, `include/boost/math/concepts/real_concept.hpp`, and about 30 test files including `test/jacobi_test.cpp`. I think it makes sense to allow it here for consistency. If required it should be fixed across the codebase in a separate PR.

> Username: jzmaddock  
> Created_at: 2023-09-09 15:45:01 UTC  
> Updated_at: 2023-09-09 15:45:02 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1320594313  

This came up here: https://github.com/boostorg/math/issues/1023 and the headers were fixed at that time.  I hadn't realised this had been sprinkled across all the tests as well :(  
  
The only remaining header I can see which is incorrect is here: https://github.com/boostorg/math/blob/09d82da6e9884da86495279b331ff038dd1330b8/include/boost/math/ccmath/signbit.hpp#L28  @mborland ?

> Username: ryanelandt  
> Created_at: 2023-09-09 17:05:11 UTC  
> Updated_at: 2023-09-09 17:05:12 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1320609910  

It might be desirable for the pattern:  
```c++  
#if defined __has_include  
#  if __cplusplus > 202002L || _MSVC_LANG > 202002L   
#    if __has_include (<X>)  
#    include <X>  
#    endif  
#  endif  
#endif  
```  
to me made into a macro similar to  
```c++  
INCLUDE_CPP23_HEADER(X)  
```

> Username: jzmaddock  
> Created_at: 2023-09-09 17:43:36 UTC  
> Updated_at: 2023-09-09 17:43:37 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1320616492  

Yes, but you still need to be able to determine whether the header has been included or not (ie whether you can use those contents or not), so the normal method is:  
  
```  
#ifndef BOOST_NO_CXX23_STDFLOAT  
#include <stdfloat>  
#endif   
  
  
  
#ifndef BOOST_NO_CXX23_STDFLOAT  
  
// do something that uses the header  
  
#endif   
```  
  
I'm working on the C++23 macros for Boost.Config now...

> Username: ryanelandt  
> Created_at: 2023-09-09 21:59:26 UTC  
> Updated_at: 2023-09-09 22:38:48 UTC  
> Url: https://github.com/boostorg/math/pull/1012#discussion_r1320658708  

The approach described above typically applies when one is manually defining methods. A manually defined method will only compile if certain headers have been included. For example, one might manually define a method for type `__float128`, another for `_Quad`, and another for `std::float128_t`. Careful preprocessor use is required because certain methods will only compile if the correct headers have been included.  
  
In contrast to this approach, this approach relies on duck typing. In C++ duck typing happens at compile time and is implemented with templates and SFINAE. It also allows a single templatized method to be used for various types. For example, the 128 bit type is used by the following types: `__float128`, `_Quad`, `std::float128_t`, `multiprecision::float128`, as well as `std_real_concept` when it's set up to emulate a 128 bit float. I may be missing some, but the compiler will not.  
  
The benefit of the duck type approach that _the code only needs to compile for the template parameters that are actually used_. For this reason, the approach does not require the careful preprocessor use of the manual approach.   
  
Also, thank you for working on the C++23 macros.


---
