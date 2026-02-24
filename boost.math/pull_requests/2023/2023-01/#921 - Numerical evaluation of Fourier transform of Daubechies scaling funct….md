# #921 Numerical evaluation of Fourier transform of Daubechies scaling funct… [Merged]

> Username: NAThompson  
> Created at: 2023-01-17 03:29:02 UTC  
> Updated at: 2023-06-13 15:05:08 UTC  
> Merged at: 2023-06-13 15:05:00 UTC  
> Closed at: 2023-06-13 15:05:00 UTC  
> Url: https://github.com/boostorg/math/pull/921  

…ions.

---

## Review 1 [Commented]

> Username: mborland  
> Created_at: 2023-01-17 16:47:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/921#pullrequestreview-1251971067  

General pedantic things. I'll read the paper this week so I can be more constructive.

📁 include/boost/math/special_functions/fourier_transform_daubechies_scaling.hpp

```diff
  43 |+    // See the Table 6.2 of Daubechies, Ten Lectures on Wavelets.
  44 |+    // I'll implement more accurate tables once we know this method works!
  45 |+    const std::array<Real, 3> lxi{static_cast<Real>(2.6613644236)/sqrt(Real(2)), static_cast<Real>(-1.52896119631)/sqrt(Real(2)), static_cast<Real>(0.281810335086)/sqrt(Real(2))};
```

> Username: mborland  
> Created_at: 2023-01-17 16:44:13 UTC  
> Updated_at: 2023-01-17 16:47:52 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1072449637  

```diff  
+using boost::math::constants::root_two;  
+   constexpr std::array<Real, 3> lxi{static_cast<Real>(2.6613644236)/root_two<Real>()), static_cast<Real>(-1.52896119631)/root_two<Real>()), static_cast<Real>(0.281810335086)/root_two<Real>())};  
```

> Username: NAThompson  
> Created_at: 2023-01-18 04:24:05 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1073082502  

Refactored into a free function.

---

📁 include/boost/math/special_functions/fourier_transform_daubechies_scaling.hpp

```diff
  12 |+ #include <iostream>
  13 |+ #include <boost/math/constants/constants.hpp>
  14 |+ #include <boost/math/filters/daubechies.hpp>
```

> Username: mborland  
> Created_at: 2023-01-17 16:44:48 UTC  
> Updated_at: 2023-01-17 16:47:52 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1072450235  

```diff  
+#include <cmath>  
+#include <complex>  
+#include <iostream>  
+#include <array>  
+#include <limits>  
+#include <stdexcept>  
+#include <boost/math/constants/constants.hpp>  
+#include <boost/math/filters/daubechies.hpp>  
```

---

📁 include/boost/math/special_functions/fourier_transform_daubechies_scaling.hpp

```diff
  50 |+    do {
  51 |+       std::complex<Real> arg{0, xi};
  52 |+       auto z = std::exp(arg);
```

> Username: mborland  
> Created_at: 2023-01-17 16:46:07 UTC  
> Updated_at: 2023-01-17 16:47:52 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1072451837  

```diff  
-      auto z = std::exp(arg);  
+      auto z = exp(arg);  
```

---

📁 include/boost/math/special_functions/fourier_transform_daubechies_scaling.hpp

```diff
  62 |+       xi /= 2;
  63 |+    } while (abs(xi) > std::numeric_limits<Real>::epsilon());
  64 |+    return phi*static_cast<std::complex<Real>>(std::pow(prefactor, p));
```

> Username: mborland  
> Created_at: 2023-01-17 16:46:22 UTC  
> Updated_at: 2023-01-17 16:47:52 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1072452239  

```diff  
-   return phi*static_cast<std::complex<Real>>(std::pow(prefactor, p));  
+   return phi*static_cast<std::complex<Real>>(pow(prefactor, p));  
```

> Username: NAThompson  
> Created_at: 2023-01-18 04:23:39 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1073082332  

Fixed.

---

📁 include/boost/math/special_functions/fourier_transform_daubechies_scaling.hpp

```diff
  39 |+    using std::abs;
  40 |+    using std::norm;
  41 |+    using std::pow;
```

> Username: mborland  
> Created_at: 2023-01-17 16:46:38 UTC  
> Updated_at: 2023-01-17 16:47:52 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1072452671  

```diff  
+   using std::sqrt;  
+   using std::abs;  
+   using std::norm;  
+   using std::pow;  
+   using std::exp;  
```


---

## Comment 2

> Username: NAThompson  
> Created_at: 2023-01-18 04:26:37 UTC  
> Url: https://github.com/boostorg/math/pull/921#issuecomment-1386469689  

@jeremy-murphy : Your polynomial division algorithm is *amazing*; super fast and it looks like every recovered digit is correct.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2023-01-18 08:49:25 UTC  
> Url: https://github.com/boostorg/math/pull/921#issuecomment-1386691872  

> @jeremy-murphy : Your polynomial division algorithm is *amazing*; super fast and it looks like every recovered digit is correct.  
  
Are you serious? I actually wouldn't have expected it.  
  
Credit for the algorithm goes to Knuth, I just implemented it.

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2023-01-18 09:58:02 UTC  
> Url: https://github.com/boostorg/math/pull/921#issuecomment-1386781598  

Anyway, if I stop being grumpy for a moment: that's great news! :)

---

## Review 5 [Commented]

> Username: mborland  
> Created_at: 2023-01-18 20:54:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/921#pullrequestreview-1260753926  

📁 include/boost/math/special_functions/fourier_transform_daubechies_scaling.hpp

```diff
  28 |+     }
  29 |+     else {
  30 |+        throw std::domain_error("Not yet implemented.");
```

> Username: mborland  
> Created_at: 2023-01-18 20:54:13 UTC  
> Updated_at: 2023-01-18 20:54:22 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1080544344  

You can't throw in a constexpr function. If you replace this with an assertion it'll be fine.

> Username: jeremy-murphy  
> Created_at: 2023-01-18 23:58:53 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1080680291  

Simplest thing might be to remove the `size_t N` template parameter until it is actually ready for other values.  
  
Anyway, I'll go back to my own thing now.  :)

> Username: NAThompson  
> Created_at: 2023-01-19 03:31:03 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1080773676  

@jeremy-murphy : lol; all I had to do to get the correct values was just pump it through your polynomial division code.  
  
@mborland : Thanks! Fixed.

> Username: jzmaddock  
> Created_at: 2023-01-19 11:59:42 UTC  
> Updated_at: 2023-01-19 11:59:43 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1081169896  

Actually you can throw in a constexpr function: but it will result in a compiler error if that branch is evaluated (which is what you want anyway).


---

## Comment 6

> Username: NAThompson  
> Created_at: 2023-01-21 19:19:02 UTC  
> Url: https://github.com/boostorg/math/pull/921#issuecomment-1399315600  

@jeremy-murphy , @jzmaddock , @mborland , @ckormanyos : Obviously don't want to burden you guys with a problem you think is uninteresting, but I figured I'd at least try to plea for help. The accuracy here without argument promotion just seems unacceptable-for instance without arg promotion it appears that up to 8 mantissa bits are incorrect in float precision. Arg promotion leads to extreme additional computational expense.  
  
Maybe this is a "no solutions, only tradeoffs" situation, but I'd figure I'd at least see if there's anything obvious we can do.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2023-01-21 20:07:34 UTC  
> Url: https://github.com/boostorg/math/pull/921#issuecomment-1399323668  

I'll try and take a look.

---

## Comment 8

> Username: NAThompson  
> Created_at: 2023-01-21 22:05:06 UTC  
> Url: https://github.com/boostorg/math/pull/921#issuecomment-1399341435  

<img width="1102" alt="Screenshot 2023-01-21 at 2 02 54 PM" src="https://user-images.githubusercontent.com/5459618/213888673-cfb987f7-80d6-4f09-af55-707d5c722934.png">  
  
This is an ULP plot of the 6-vanishing moment implementation; you can see it's all over the place. The red line over the top indicates that worse values have been clipped.

---

## Comment 9

> Username: mborland  
> Created_at: 2023-01-21 22:07:48 UTC  
> Url: https://github.com/boostorg/math/pull/921#issuecomment-1399341825  

> <img width="1102" alt="Screenshot 2023-01-21 at 2 02 54 PM" src="https://user-images.githubusercontent.com/5459618/213888673-cfb987f7-80d6-4f09-af55-707d5c722934.png">  
>   
>   
>   
> This is an ULP plot of the 6-vanishing moment implementation; you can see it's all over the place. The red line over the top indicates that worse values have been clipped.  
  
Is this with or without promotion in place?

---

## Comment 10

> Username: NAThompson  
> Created_at: 2023-01-21 22:09:33 UTC  
> Url: https://github.com/boostorg/math/pull/921#issuecomment-1399342239  

> Is this with or without promotion in place?   
  
No arg promotion-actually with arg promotion this would be tautologically 0 everywhere.

---

## Comment 11

> Username: NAThompson  
> Created_at: 2023-01-21 22:23:24 UTC  
> Url: https://github.com/boostorg/math/pull/921#issuecomment-1399344173  

As you might expect, we do better with fewer moments, but still not up to snuff:  
  
<img width="1109" alt="Screenshot 2023-01-21 at 2 22 52 PM" src="https://user-images.githubusercontent.com/5459618/213889282-29f54934-0364-4130-9c9f-8777b7b9a3af.png">

---

## Comment 12

> Username: NAThompson  
> Created_at: 2023-01-21 22:26:55 UTC  
> Url: https://github.com/boostorg/math/pull/921#issuecomment-1399344667  

Condition number of function evaluation revealed to be fairly bad by putting the ULP envelope around it:  
  
   
<img width="1104" alt="Screenshot 2023-01-21 at 2 26 21 PM" src="https://user-images.githubusercontent.com/5459618/213889393-fd2958e5-f031-44b2-aad9-d2ef3696dd0c.png">

---

## Comment 13

> Username: jeremy-murphy  
> Created_at: 2023-01-25 03:22:44 UTC  
> Url: https://github.com/boostorg/math/pull/921#issuecomment-1403046677  

> @jeremy-murphy , @jzmaddock , @mborland , @ckormanyos : Obviously don't want to burden you guys with a problem you think is uninteresting, but I figured I'd at least try to plea for help. The accuracy here without argument promotion just seems unacceptable-for instance without arg promotion it appears that up to 8 mantissa bits are incorrect in float precision. Arg promotion leads to extreme additional computational expense.  
>   
> Maybe this is a "no solutions, only tradeoffs" situation, but I'd figure I'd at least see if there's anything obvious we can do.  
  
On the contrary, it sounds very interesting, and I wish I had time to help, but I don't.  
  
Maybe only certain operations need the extra accuracy afforded by arg promotion?

---

## Comment 14

> Username: jeremy-murphy  
> Created_at: 2023-01-26 11:48:22 UTC  
> Url: https://github.com/boostorg/math/pull/921#issuecomment-1404892915  

I haven't found the literature that defines the ratio for choosing an algorithm for polynomial evaluation, but I did find this reference, which may help you:  
  
S. Graillat, P. Langlois, and N. Louvet. Algorithms for accurate, vali-  
dated and fast computations with polynomials. Japan Journal of Indus-  
trial and Applied Mathematics, 26(2):215–231, 2009.  
  
The textbook I got that reference from (Handbook of Floating-Point Arithmetic, 2nd ed.) suggests to take the "Graillat–Langlois–Louvet error-free transformation" and make it a compensated summation using the same principle as Kahan's summation. I can give you more details if you're interested.

---

## Comment 15

> Username: NAThompson  
> Created_at: 2023-01-26 16:03:25 UTC  
> Url: https://github.com/boostorg/math/pull/921#issuecomment-1405233080  

>  I can give you more details if you're interested.  
  
Yes, I'm interested. This particular algorithm has some trouble with compensated summation, because it's actually an infinite product where each term is a small polynomial. I think I might actually need some sort of "compensated product", if that even makes sense in floating point.

---

## Comment 16

> Username: NAThompson  
> Created_at: 2023-02-04 21:49:06 UTC  
> Updated_at: 2023-02-04 21:49:20 UTC  
> Url: https://github.com/boostorg/math/pull/921#issuecomment-1416857582  

Well the color scheme is absolutely grotesque, but it appears to be correct:  
  
<img width="1303" alt="Screenshot 2023-02-04 at 1 48 23 PM" src="https://user-images.githubusercontent.com/5459618/216790918-43e324c4-54c8-4d71-a4bc-aa1591c3448c.png">

---

## Comment 17

> Username: NAThompson  
> Created_at: 2023-06-11 20:56:30 UTC  
> Url: https://github.com/boostorg/math/pull/921#issuecomment-1586334227  

@jzmaddock , @mborland : I've finally gotten this working; mind giving it a look?

---

## Review 18 [Commented]

> Username: mborland  
> Created_at: 2023-06-12 07:30:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/921#pullrequestreview-1474190754  

📁 example/calculate_fourier_transform_daubechies_constants.cpp

```diff
   1 |+ #include <utility>
```

> Username: mborland  
> Created_at: 2023-06-12 07:14:05 UTC  
> Updated_at: 2023-06-12 07:30:03 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1226187628  

```diff  
-#include <utility>  
+//  (C) Copyright Nick Thompson 2023.  
+//  Use, modification and distribution are subject to the  
+//  Boost Software License, Version 1.0. (See accompanying file  
+//  LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
+#include <utility>  
```


📁 example/fourier_transform_daubechies_ulp_plot.cpp

```diff
   1 |+ #include <boost/math/special_functions/fourier_transform_daubechies_scaling.hpp>
```

> Username: mborland  
> Created_at: 2023-06-12 07:14:38 UTC  
> Updated_at: 2023-06-12 07:30:04 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1226188140  

```diff  
-#include <boost/math/special_functions/fourier_transform_daubechies_scaling.hpp>  
+//  (C) Copyright Nick Thompson 2023.  
+//  Use, modification and distribution are subject to the  
+//  Boost Software License, Version 1.0. (See accompanying file  
+//  LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
+#include <boost/math/special_functions/fourier_transform_daubechies_scaling.hpp>  
```

---

📁 example/fourier_transform_daubechies_ulp_plot.cpp

```diff
  40 |+    plot.add_fn(phi_imag_lo_acc);
  41 |+    plot.clip(100);
  42 |+    plot.title("Accuracy of 𝕴(𝓕[𝜙](ω)) with " + std::to_string(p) + " vanishing moments.");
```

> Username: mborland  
> Created_at: 2023-06-12 07:15:53 UTC  
> Updated_at: 2023-06-12 07:30:04 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1226189342  

There is no particularly good replacement, but the document inspector doesn't approve of non-ASCII characters

> Username: NAThompson  
> Created_at: 2023-06-12 15:33:09 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1226858464  

ehhh. . . is the document inspector wrong about this? I've never had any problems with a unicode character in a string?

> Username: mborland  
> Created_at: 2023-06-12 15:40:51 UTC  
> Updated_at: 2023-06-12 15:40:52 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1226868285  

You can add:  
  
```  
// boost-no-inspect  
```  
  
to the top of every file you want non-ASCII characters in. Here is the initial list: https://app.circleci.com/pipelines/github/boostorg/math/1307/workflows/87577a8f-9f8f-4f96-9b65-d8f14a0a14a1/jobs/1295


📁 include/boost/math/special_functions/fourier_transform_daubechies_scaling.hpp

```diff
  25 |+ // we'd immediately just have to divide through by 1/sqrt(2).
  26 |+ // These numbers agree with Table 6.2, but are generated via example/calculate_fourier_transform_daubechies_constants.cpp
  27 |+ template <typename Real, unsigned N> constexpr const std::array<Real, N> ft_daubechies_scaling_polynomial_coefficients() {
```

> Username: mborland  
> Created_at: 2023-06-12 07:16:56 UTC  
> Updated_at: 2023-06-12 07:30:04 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1226190432  

```diff  
-template <typename Real, unsigned N> constexpr const std::array<Real, N> ft_daubechies_scaling_polynomial_coefficients() {  
+template <typename Real, unsigned N> constexpr std::array<Real, N> ft_daubechies_scaling_polynomial_coefficients() {  
```  
  
`constexpr const` is redundant from C++14.

---

📁 include/boost/math/special_functions/fourier_transform_daubechies_scaling.hpp

```diff
  26 |+ // These numbers agree with Table 6.2, but are generated via example/calculate_fourier_transform_daubechies_constants.cpp
  27 |+ template <typename Real, unsigned N> constexpr const std::array<Real, N> ft_daubechies_scaling_polynomial_coefficients() {
  28 |+   static_assert(N >= 1 && N <= 10, "Scaling function only implemented for 1-10 vanishing moments.");
```

> Username: mborland  
> Created_at: 2023-06-12 07:18:38 UTC  
> Updated_at: 2023-06-12 07:30:04 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1226192016  

Is there a way to compute the constants outside of this range on an as-needed basis like `calculate_constants`?

> Username: NAThompson  
> Created_at: 2023-06-12 16:07:30 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1226899764  

Not really, especially since I want it in a `constexpr std::array` . . .  
  
The calculation is rather involved.. .

---

📁 include/boost/math/special_functions/fourier_transform_daubechies_scaling.hpp

```diff
 205 |+   // Now, should we probably do a fairly involved, exhaustive calculation to see where exactly we should set this threshold
 206 |+   // and store them in a table? .... yes.
 207 |+   if (abs(omega) >= sqrt(std::numeric_limits<Real>::max())) {
```

> Username: mborland  
> Created_at: 2023-06-12 07:19:30 UTC  
> Updated_at: 2023-06-12 07:30:04 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1226192866  

```diff  
-  if (abs(omega) >= sqrt(std::numeric_limits<Real>::max())) {  
+  if (abs(omega) >= sqrt((std::numeric_limits<Real>::max)())) {  
```

---

📁 include/boost/math/special_functions/fourier_transform_daubechies_scaling.hpp

```diff
 169 |+ 
 170 |+ /*
 171 |+  * Given ω∈ℝ, computes a numerical approximation to 𝓕[𝜙](ω),
```

> Username: mborland  
> Created_at: 2023-06-12 07:20:07 UTC  
> Updated_at: 2023-06-12 07:30:04 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1226193466  

Same comment about non-ASCII characters

---

📁 include/boost/math/special_functions/fourier_transform_daubechies_scaling.hpp

```diff
 208 |+        return std::complex<Real>(0, 0);
 209 |+   }
 210 |+   auto const constexpr lxi = detail::ft_daubechies_scaling_polynomial_coefficients<Real, p>();
```

> Username: mborland  
> Created_at: 2023-06-12 07:20:31 UTC  
> Updated_at: 2023-06-12 07:30:04 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1226193855  

```diff  
-  auto const constexpr lxi = detail::ft_daubechies_scaling_polynomial_coefficients<Real, p>();  
+  auto constexpr lxi = detail::ft_daubechies_scaling_polynomial_coefficients<Real, p>();  
```

---

📁 include/boost/math/special_functions/fourier_transform_daubechies_scaling.hpp

```diff
 219 |+   std::complex<Real> arg{0, omega};
 220 |+   // There is no std::expm1 for complex numbers.
 221 |+   // We may therefore be leaving accuracy gains on the table for small |ω|:
```

> Username: mborland  
> Created_at: 2023-06-12 07:23:30 UTC  
> Updated_at: 2023-06-12 07:30:04 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1226197079  

Is it worth adding the complex case to Boost.Math::expm1? This Julia PR doing the same thing makes it seem not overly difficult https://github.com/JuliaLang/julia/pull/6904/files.

---

📁 include/boost/math/special_functions/fourier_transform_daubechies_scaling.hpp

```diff
 239 |+   auto z = phase; // strange coincidence.
 240 |+   //auto z = exp(std::complex<Real>(0, -omega/2 - boost::math::constants::pi<Real>()));
 241 |+   auto const constexpr lxi = detail::ft_daubechies_scaling_polynomial_coefficients<Real, p>();
```

> Username: mborland  
> Created_at: 2023-06-12 07:24:16 UTC  
> Updated_at: 2023-06-12 07:30:04 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1226198027  

```diff  
-  auto const constexpr lxi = detail::ft_daubechies_scaling_polynomial_coefficients<Real, p>();  
+  auto constexpr lxi = detail::ft_daubechies_scaling_polynomial_coefficients<Real, p>();  
```


📁 test/fourier_transform_daubechies_scaling_test.cpp

```diff
  31 |+ using boost::math::constants::one_div_root_two_pi;
  32 |+ using boost::math::quadrature::trapezoidal;
  33 |+ // 𝓕[φ](-ω) = 𝓕[φ](ω)*
```

> Username: mborland  
> Created_at: 2023-06-12 07:25:48 UTC  
> Updated_at: 2023-06-12 07:30:04 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1226200260  

ASCII

---

📁 test/fourier_transform_daubechies_scaling_test.cpp

```diff
 215 |+ 
 216 |+   // Phase convention still failing fml:
 217 |+   test_wavelet_quadrature<9>();
```

> Username: mborland  
> Created_at: 2023-06-12 07:27:12 UTC  
> Updated_at: 2023-06-12 07:30:04 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1226201950  

Is this still failing?

> Username: NAThompson  
> Created_at: 2023-06-12 15:57:50 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1226888923  

Nope, fixed.


📁 reporting/performance/fourier_transform_daubechies_scaling_performance.cpp

```diff
  49 |+ 
  50 |+ 
  51 |+ BENCHMARK_MAIN();
```

> Username: mborland  
> Created_at: 2023-06-12 07:26:38 UTC  
> Updated_at: 2023-06-12 07:30:04 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1226201115  

Is it worth pasting the benchmark results in the bottom of this file or in the docs?

> Username: NAThompson  
> Created_at: 2023-06-12 16:06:46 UTC  
> Updated_at: 2023-06-12 16:06:47 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1226898993  

Yes-done.


📁 test/math_unit_test.hpp

```diff
  92 |     if (!std::is_integral<PreciseReal>::value) {
  93 |-         BOOST_MATH_ASSERT_MSG(!isnan(expected1), "Expected value cannot be a nan.");
  93 |+ 	if (isnan(expected1)) {
```

> Username: mborland  
> Created_at: 2023-06-12 07:28:35 UTC  
> Updated_at: 2023-06-12 07:30:04 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1226203816  

```diff  
-	if (isnan(expected1)) {  
+	if (boost::math::isnan(expected1)) {  
```  
  
Even the new clang based intel compilers replace `std::isnan` with false.

> Username: NAThompson  
> Created_at: 2023-06-12 15:35:33 UTC  
> Updated_at: 2023-06-12 15:35:34 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1226861312  

That should only be with `-ffast-math` correct?

> Username: mborland  
> Created_at: 2023-06-12 15:38:04 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1226864410  

I believe so, but it warns it is going to do it at every optimization level.

> Username: NAThompson  
> Created_at: 2023-06-12 16:08:26 UTC  
> Url: https://github.com/boostorg/math/pull/921#discussion_r1226900940  

Dear God, that is the absolute worst decision I've ever seen.


---
