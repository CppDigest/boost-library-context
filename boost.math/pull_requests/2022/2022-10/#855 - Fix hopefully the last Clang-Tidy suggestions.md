# #855 Fix hopefully the last Clang-Tidy suggestions [Closed]

> Username: SiliconA-Z  
> Created at: 2022-10-25 15:47:38 UTC  
> Updated at: 2022-10-29 08:27:04 UTC  
> Closed at: 2022-10-29 01:49:58 UTC  
> Url: https://github.com/boostorg/math/pull/855  

There were some last second things I missed.  
  
Among these warnings were "redundant static casts" and "deprecated C-headers"  
  
There were others too but those warnings were false flags so I kept the ones that actually made sense.

---

## Review 1 [Commented]

> Username: pulver  
> Created_at: 2022-10-25 15:52:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/855#pullrequestreview-1155078382  

📁 include/boost/math/special_functions/gamma.hpp

```diff
  60 |    int i = static_cast<int>(v);
  61 |-    return i&1;
  61 |+    return i & 1 == 1;
```

> Username: pulver  
> Created_at: 2022-10-25 15:52:49 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1004669906  

`==` has higher precedence than `&` so this is parsed as  
```  
return i & (1 == 1);  
```  
Personally I prefer the current version: `return i&1;`.


---

## Review 2 [Changes requested]

> Username: mborland  
> Created_at: 2022-10-25 16:27:20 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/math/pull/855#pullrequestreview-1155127213  

📁 include/boost/math/quadrature/naive_monte_carlo.hpp

```diff
 167 |+         m_thread_calls = std::make_unique<std::atomic<uint64_t>[]>(threads);
 168 |+         m_thread_Ss = std::make_unique<std::atomic<Real>[]>(threads);
 169 |+         m_thread_averages = std::make_unique<std::atomic<Real>[]>(threads);
```

> Username: mborland  
> Created_at: 2022-10-25 16:25:04 UTC  
> Updated_at: 2022-10-25 16:27:20 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1004703754  

This change will break compatibility with C++11. We will not be moving to C++14 until Boost 1.82


---

## Review 3 [Commented]

> Username: jzmaddock  
> Created_at: 2022-10-26 11:34:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/855#pullrequestreview-1156340004  

📁 include/boost/math/quadrature/gauss.hpp

```diff
  49 |       std::vector<Real> result(abscissa().size(), 0);
  50 |-       for (unsigned i = 0; i < abscissa().size(); ++i)
  50 |+       for (size_t i = 0; i < abscissa().size(); ++i)
```

> Username: jzmaddock  
> Created_at: 2022-10-26 11:34:37 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1005563247  

shouldn't that be std::size_t?


---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2022-10-26 11:37:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/855#pullrequestreview-1156343431  

📁 include/boost/math/quadrature/gauss_kronrod.hpp

```diff
  57 |       }
  58 |-       for (unsigned i = gauss_start ? 0 : 1; i < abscissa().size(); i += 2)
  58 |+       for (size_t i = gauss_start ^ 1; i < abscissa().size(); i += 2)
```

> Username: jzmaddock  
> Created_at: 2022-10-26 11:37:21 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1005565652  

Surely this is simply not the same thing?

> Username: SiliconA-Z  
> Created_at: 2022-10-26 13:53:48 UTC  
> Updated_at: 2022-10-26 13:53:49 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1005716625  

Yeah it is.

> Username: SiliconA-Z  
> Created_at: 2022-10-26 13:55:49 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1005719233  

guass_start is 0 or 1. Though, I wonder if it may be optimized to the same thing anyway.


---

## Review 5 [Commented]

> Username: jzmaddock  
> Created_at: 2022-10-26 11:37:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/855#pullrequestreview-1156343849  

📁 include/boost/math/quadrature/gauss_kronrod.hpp

```diff
1800 |       Real L1 = abs(kronrod_result);
1801 |-       for (unsigned i = gauss_start; i < base::abscissa().size(); i += 2)
1801 |+       for (size_t i = gauss_start; i < base::abscissa().size(); i += 2)
```

> Username: jzmaddock  
> Created_at: 2022-10-26 11:37:41 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1005565917  

std::size_t


---

## Review 6 [Commented]

> Username: jzmaddock  
> Created_at: 2022-10-26 11:40:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/855#pullrequestreview-1156347768  

📁 include/boost/math/special_functions/bessel.hpp

```diff
 182 |          T r = cyl_bessel_i_imp(v, T(-x), pol);
 183 |-          if(iround(v, pol) & 1)
 183 |+          if((iround(v, pol) & 1) == 1)
```

> Username: jzmaddock  
> Created_at: 2022-10-26 11:40:57 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1005568595  

Stupid question: I know compilers are super-smart these days, but has someone checked that this doesn't generate dis-optimised code?  At the assembly level `x&1` sets the necessary processor flags for the branch to happen, the comparison to 1 is completely superfluous.  A long long time ago when I started programming we used to have to worry about this ;)

> Username: SiliconA-Z  
> Created_at: 2022-10-26 13:53:35 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1005716356  

I did this to avoid pedantic warnings about implicit casts to bool. You are right though.


---

## Review 7 [Commented]

> Username: jzmaddock  
> Created_at: 2022-10-26 11:56:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/855#pullrequestreview-1156367195  

📁 include/boost/math/special_functions/detail/hypergeometric_pFq_checked_series.hpp

```diff
 362 |-               if (z < 0)
 363 |-                  s1 *= (s & 1 ? -1 : 1);
 362 |+               if (z < 0 && (s & 1) == 1)
```

> Username: jzmaddock  
> Created_at: 2022-10-26 11:56:14 UTC  
> Updated_at: 2022-10-26 11:56:15 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1005582003  

This isn't correct: operator precedence means the && is evaluated before the <.


---

## Review 8 [Commented]

> Username: jzmaddock  
> Created_at: 2022-10-26 11:56:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/855#pullrequestreview-1156367673  

📁 include/boost/math/special_functions/detail/hypergeometric_series.hpp

```diff
 237 |-            if (s)
 238 |-               *s *= (n & 1 ? -1 : 1);
 237 |+            if (s && (n & 1) == 1)
```

> Username: jzmaddock  
> Created_at: 2022-10-26 11:56:37 UTC  
> Updated_at: 2022-10-26 11:56:38 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1005582344  

operator precedence.


---

## Review 9 [Commented]

> Username: jzmaddock  
> Created_at: 2022-10-26 12:01:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/855#pullrequestreview-1156373798  

📁 include/boost/math/special_functions/detail/polygamma.hpp

```diff
 150 | 
 151 |-     const int minus_m_minus_one = -m - 1;
 151 |+     const int minus_m_minus_one = ~m;
```

> Username: jzmaddock  
> Created_at: 2022-10-26 12:01:19 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1005586556  

I need someone to convince me that this doesn't rely on undefined or implementation defined behaviour (2's complement).

> Username: SiliconA-Z  
> Created_at: 2022-10-26 14:33:27 UTC  
> Updated_at: 2022-10-26 14:33:28 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1005765943  

To be fair, a lot of the code assumes 2's complement, even if it doesn't look like it.  
  
See: the & 1 meaning odd. 1's complement wouldn't work, but since the compiler knows to transform this, I just reverted back just in case.


---

## Review 10 [Commented]

> Username: jzmaddock  
> Created_at: 2022-10-26 12:01:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/855#pullrequestreview-1156374271  

📁 include/boost/math/special_functions/detail/polygamma.hpp

```diff
 224 |      T sum = prefix;
 225 |-      for(unsigned k = 0;;)
 225 |+      for(size_t k = 0;;)
```

> Username: jzmaddock  
> Created_at: 2022-10-26 12:01:41 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1005586867  

std::


---

## Review 11 [Commented]

> Username: jzmaddock  
> Created_at: 2022-10-26 12:02:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/855#pullrequestreview-1156375673  

📁 include/boost/math/special_functions/detail/unchecked_factorial.hpp

```diff
 949 |          digits = current_digits;
 950 |-          for(unsigned k = 0; k < sizeof(factorials) / sizeof(factorials[0]); ++k)
 950 |+          for(size_t k = 0; k < sizeof(factorials) / sizeof(factorials[0]); ++k)
```

> Username: jzmaddock  
> Created_at: 2022-10-26 12:02:43 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1005587837  

std::


---

## Review 12 [Commented]

> Username: jzmaddock  
> Created_at: 2022-10-26 12:33:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/855#pullrequestreview-1156419088  

📁 include/boost/math/special_functions/legendre.hpp

```diff
 173 |-     int k;
 174 |-     if (n & 1)
 173 |+     size_t k;
```

> Username: jzmaddock  
> Created_at: 2022-10-26 12:33:52 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1005618210  

std::

> Username: jzmaddock  
> Created_at: 2022-10-26 12:37:27 UTC  
> Updated_at: 2022-10-26 12:37:28 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1005621992  

Never mind, I see you've change the definition of k.


---

## Review 13 [Commented]

> Username: jzmaddock  
> Created_at: 2022-10-26 12:35:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/855#pullrequestreview-1156421092  

📁 include/boost/math/special_functions/legendre.hpp

```diff
 186 | 
 187 |-     while (k < (int)zeros.size())
 187 |+     while (k < zeros.size())
```

> Username: jzmaddock  
> Created_at: 2022-10-26 12:35:12 UTC  
> Updated_at: 2022-10-26 12:35:13 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1005619571  

I believe this change will result in a signed/unsigned comparison warning on msvc?


---

## Review 14 [Commented]

> Username: jzmaddock  
> Created_at: 2022-10-26 12:35:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/855#pullrequestreview-1156422204  

📁 include/boost/math/special_functions/legendre.hpp

```diff
 212 |-         zeros[k] = x_nk;
 213 |-         ++k;
 212 |+         zeros[k++] = x_nk;
```

> Username: jzmaddock  
> Created_at: 2022-10-26 12:35:57 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1005620370  

Cute, but I actually prefer the readability of the original.


---

## Review 15 [Commented]

> Username: jzmaddock  
> Created_at: 2022-10-26 12:39:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/855#pullrequestreview-1156426823  

📁 include/boost/math/special_functions/zeta.hpp

```diff
 906 |       digits = current_digits;
 907 |-       for(unsigned k = 0; k < sizeof(results) / sizeof(results[0]); ++k)
 907 |+       for(size_t k = 0; k < sizeof(results) / sizeof(results[0]); ++k)
```

> Username: jzmaddock  
> Created_at: 2022-10-26 12:39:01 UTC  
> Updated_at: 2022-10-26 12:39:02 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1005623557  

std::


---

## Comment 16

> Username: mborland  
> Created_at: 2022-10-26 15:24:14 UTC  
> Url: https://github.com/boostorg/math/pull/855#issuecomment-1292219430  

@AtariDreams when pushing large numbers of commits please mark all but the final one with `[ci skip]` in the commit message. That way you don't overload the CI system which automatically runs to completion on every pushed commit to a PR.

---

## Comment 17

> Username: SiliconA-Z  
> Created_at: 2022-10-28 16:34:53 UTC  
> Url: https://github.com/boostorg/math/pull/855#issuecomment-1295205854  

Okay, this is ready for review!

---

## Review 18 [Commented]

> Username: mborland  
> Created_at: 2022-10-29 01:45:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/855#pullrequestreview-1160881542  

📁 include/boost/math/ccmath/isnormal.hpp

```diff
  23 |+     {
  24 |+         return x != T(0) && !boost::math::ccmath::isinf(x) && !boost::math::ccmath::isnan(x) &&
  25 |+                boost::math::ccmath::abs(x) >= (std::numeric_limits<T>::min)();
```

> Username: mborland  
> Created_at: 2022-10-29 01:19:19 UTC  
> Updated_at: 2022-10-29 01:45:56 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1008602104  

I'm not convinced this is clearer.


📁 include/boost/math/constants/calculate_constants.hpp

```diff
 630 |       T d = 1;
 631 |-       for(unsigned int i = 1; i <= (n+n + 1); ++i) // (2n + 1)
 631 |+       for(unsigned int i = 1; i <= (2 * n + 1); ++i) // (2n + 1)
```

> Username: mborland  
> Created_at: 2022-10-29 01:20:44 UTC  
> Updated_at: 2022-10-29 01:45:56 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1008602238  

Addition is historically a faster instruction than multiplication.

---

📁 include/boost/math/constants/calculate_constants.hpp

```diff
 639 |-       { //term *= -1;
 640 |-         sum -= term;
 638 |+       if ((n & 1) == 0)
```

> Username: mborland  
> Created_at: 2022-10-29 01:23:51 UTC  
> Updated_at: 2022-10-29 01:45:56 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1008602534  

Like @jzmaddock said this requires two's complement. C++20 and C23 require that, but previous language standards do not. The intent is less clear as well.


📁 include/boost/math/cstdfloat/cstdfloat_complex_std.hpp

```diff
 527 |           // The variable xn stores the binary powers of x.
 528 |-           complex<BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE> result(((p % 2) != 0) ? x : complex<BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE>(BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE(1)));
 528 |+           complex<BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE> result(((p & 1) == 1) ? x : complex<BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE>(BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE(1)));
```

> Username: mborland  
> Created_at: 2022-10-29 01:25:26 UTC  
> Updated_at: 2022-10-29 01:45:56 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1008602745  

Same discussion on two's complement and readability.


📁 include/boost/math/cstdfloat/cstdfloat_cmath.hpp

```diff
  91 |                      // The variable xn stores the binary powers of x.
  92 |-                      float_type result(((p % integer_type(2)) != integer_type(0)) ? x : float_type(1));
  92 |+                      float_type result(((p & 1) == integer_type(1)) ? x : float_type(1));
```

> Username: mborland  
> Created_at: 2022-10-29 01:27:39 UTC  
> Updated_at: 2022-10-29 01:45:56 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1008603017  

Same discussion on two's complement and readability.


📁 include/boost/math/interpolators/vector_barycentric_rational.hpp

```diff
  27 | 
  28 |-     void operator()(Point& x, Real t) const;
  28 |+     void operator()(Point& p, Real t) const;
```

> Username: mborland  
> Created_at: 2022-10-29 01:30:56 UTC  
> Updated_at: 2022-10-29 01:45:56 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1008603759  

Why change this?


📁 include/boost/math/quadrature/gauss.hpp

```diff
1184 |       using std::abs;
1185 |-       unsigned non_zero_start = 1;
1185 |+       unsigned non_zero_start;
```

> Username: mborland  
> Created_at: 2022-10-29 01:33:04 UTC  
> Updated_at: 2022-10-29 01:45:56 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1008604042  

Why remove initialization?


📁 include/boost/math/quadrature/gauss_kronrod.hpp

```diff
  47 |       unsigned gauss_order = (N - 1) / 2;
  48 |-       unsigned gauss_start = gauss_order & 1 ? 0 : 1;
  48 |+       unsigned gauss_start = (gauss_order & 1) ^ 1;
```

> Username: mborland  
> Created_at: 2022-10-29 01:33:25 UTC  
> Updated_at: 2022-10-29 01:45:56 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1008604076  

Same discussion on two's complement and readability.

---

📁 include/boost/math/quadrature/gauss_kronrod.hpp

```diff
1800 |       Real L1 = abs(kronrod_result);
1801 |-       for (unsigned i = gauss_start; i < base::abscissa().size(); i += 2)
1801 |+       for (decltype(base::abscissa().size()) i = gauss_start; i < base::abscissa().size(); i += 2)
```

> Username: mborland  
> Created_at: 2022-10-29 01:33:59 UTC  
> Updated_at: 2022-10-29 01:45:56 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1008604150  

`auto` would be much more appropriate.


📁 include/boost/math/quadrature/naive_monte_carlo.hpp

```diff
  47 |         using std::sqrt;
  48 |-         uint64_t n = bounds.size();
  48 |+         size_t n = bounds.size();
```

> Username: mborland  
> Created_at: 2022-10-29 01:36:33 UTC  
> Updated_at: 2022-10-29 01:45:56 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1008604406  

`size_t` is not guaranteed to be any specific width.


📁 include/boost/math/special_functions/detail/bernoulli_details.hpp

```diff
 325 |             m_overflow_limit = i;
 326 |-             while(i < m)
 326 |+             for (; i < m; ++i)
```

> Username: mborland  
> Created_at: 2022-10-29 01:40:58 UTC  
> Updated_at: 2022-10-29 01:45:56 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1008604934  

I wouldn't change this for stylistic preference.


📁 include/boost/math/special_functions/detail/bessel_jy_series.hpp

```diff
 118 | private:
 124 |-    unsigned N;
 119 |+    unsigned N{0};
```

> Username: mborland  
> Created_at: 2022-10-29 01:44:50 UTC  
> Updated_at: 2022-10-29 01:45:56 UTC  
> Url: https://github.com/boostorg/math/pull/855#discussion_r1008605382  

You don't need to add the 0 with braced initialization.


---

## Comment 19

> Username: mborland  
> Created_at: 2022-10-29 01:49:58 UTC  
> Url: https://github.com/boostorg/math/pull/855#issuecomment-1295683715  

@AtariDreams I've read through a decent chunk of this. Some of it is useful like updating the deprecated c-headers, but the majority seems to be stylistic preference. This PR started with ~5 files and is now at almost 100. If you would like please submit tightly scoped PRs like updating the C headers. I am going to close this one. Again a good place to look if you would like to contribute is the pinned issue for feature wishlist.

---

## Comment 20

> Username: jzmaddock  
> Created_at: 2022-10-29 08:27:04 UTC  
> Url: https://github.com/boostorg/math/pull/855#issuecomment-1295771261  

I fear Matt's correct: there's just way too much here to be able to review accurately in one go.  There are useful nuggets in here, but there's a lot of cute changes too which require a lot of head scratching to figure out if they're correct, and look mostly stylistic.  
  
So... smaller diff's please, try and concentrate on the important stuff (anything that's a clear error like including the same file twice), and please try not to bombard the system with dozens of pushes: CI is a shared resource for the whole of Boost, and this one PR pretty much ground everything to a halt!

---
