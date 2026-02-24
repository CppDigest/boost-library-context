# #419 - ulp_plot is slow [Open]

> Username: jzmaddock  
> Created at: 2020-08-14 16:36:16 UTC  
> Updated at: 2020-09-02 17:42:52 UTC  
> Url: https://github.com/boostorg/math/issues/419  

@NAThompson:  
  
Test program below for erfc, but the issue is twofold:  
  
* Condition numbers are calculated numerically - which is great when there's no choice - but terribly slow compared to calculating the derivative algebraically, especially for something like erf/erfc where the derivative is trivial: https://www.wolframalpha.com/input/?i=d%2Fdx+erfc%28x%29  
* Unless I'm mistaken, we're calculating condition numbers for ever point at which we evaluate f(x), but can we not just evaluate at a few points and fit a bezier through them?  Or failing that set a "stride" value, and calculate the condition value at every n'th point?  
  
Otherwise almost all of the runtime in the program below is in thrashing through erf calls calculating condition numbers.  
  
```  
  
//  (C) Copyright Nick Thompson 2020.  
//  (C) Copyright John Maddock 2020.  
//  Use, modification and distribution are subject to the  
//  Boost Software License, Version 1.0. (See accompanying file  
//  LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
#include <iostream>  
#include <boost/math/tools/ulps_plot.hpp>  
#include <boost/core/demangle.hpp>  
#include <boost/multiprecision/mpfr.hpp>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#ifdef BOOST_HAS_FLOAT128  
#include <boost/multiprecision/float128.hpp>  
#endif  
  
using namespace boost::multiprecision;  
  
#ifndef TEST_TYPE  
#define TEST_TYPE cpp_bin_float_50  
#endif  
  
std::string test_type_name(BOOST_STRINGIZE(TEST_TYPE));  
std::string test_type_filename(BOOST_STRINGIZE(TEST_TYPE));  
  
using boost::math::tools::ulps_plot;  
  
int main()   
{  
   std::string::size_type n;  
   while ((n = test_type_filename.find_first_not_of("_qwertyuiopasdfghjklzxcvbnmQWERTYUIOPASDFGHJKLZXCVBNM1234567890")) != std::string::npos)  
   {  
      test_type_filename[n] = '_';  
   }  
  
   using PreciseReal = boost::multiprecision::mpfr_float_100;  
   using CoarseReal = TEST_TYPE;  
  
   typedef boost::math::policies::policy<  
      boost::math::policies::promote_float<false>,  
      boost::math::policies::promote_double<false> >  
      no_promote_policy;  
  
   auto ai_coarse = [](CoarseReal const& x)->CoarseReal {  
      return erfc(x);  
   };  
   auto ai_precise = [](PreciseReal const& x)->PreciseReal {  
      return erfc(x);  
   };  
  
   std::string filename = "erfc_errors_";  
   filename += test_type_filename;  
   filename += ".svg";  
   int samples = 100000;  
   // How many pixels wide do you want your .svg?  
   int width = 700;  
   // Near a root, we have unbounded relative error. So for functions with roots, we define an ULP clip:  
   PreciseReal clip = 40;  
   // Should we perturb the abscissas? i.e., should we compute the high precision function f at x,  
   // and the low precision function at the nearest representable x̂ to x?  
   // Or should we compute both the high precision and low precision function at a low precision representable x̂?  
   bool perturb_abscissas = false;  
   auto plot = ulps_plot<decltype(ai_precise), PreciseReal, CoarseReal>(ai_precise, CoarseReal(-10), CoarseReal(30), samples, perturb_abscissas);  
   // Note the argument chaining:  
   plot.clip(clip).width(width);  
   plot.background_color("white").font_color("black");  
   // Sometimes it's useful to set a title, but in many cases it's more useful to just use a caption.  
   std::string title = "Erfc ULP plot at " + test_type_name + " precision";  
   plot.title(title);  
   plot.vertical_lines(6);  
   plot.add_fn(ai_coarse);  
   // You can write the plot to a stream:  
   //std::cout << plot;  
   // Or to a file:  
   plot.write(filename);  
}  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-08-14 16:38:32 UTC  
> Url: https://github.com/boostorg/math/issues/419#issuecomment-674160337  

Actually.... that program may not build with gcc/clang but does with msvc: something I need to investigate!  
  
It *will* build with -DTEST_TYPE=double though.

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-08-14 17:25:24 UTC  
> Updated at: 2020-08-14 17:36:44 UTC  
> Url: https://github.com/boostorg/math/issues/419#issuecomment-674180126  

@jzmaddock : Yeah, numerically evaluating the condition number is a bit of a problem. We could decrease the order of the derivative to get a quick win and probably get a nearly identical graph; for example; we evaluate `f(x)`, so `f(x+h)` for `h = sqrt(eps)` will lose half the digits of precision, but assuming minimum double precision for `PreciseReal`, who cares? You can't see it.  
  
The reason I did it this way is that I didn't want to clutter the API by forcing users to pass in derivatives as well.  
  
Maybe autodiffing is a sensible option?  
  
I'm actually somewhat surprised it matters; my own experience is that parsing the `.svg` and rendering it limits you to ~100,000 points, so it always has felt instantaneous to me . . .  
  
>     Unless I'm mistaken, we're calculating condition numbers for ever point at which we evaluate f(x), but can we not just evaluate at a few points and fit a bezier through them? Or failing that set a "stride" value, and calculate the condition value at every n'th point?  
  
Absolutely sensible options, but requires a sensible way to define smoothness. If you look at Figure 3c of [this](https://arxiv.org/ftp/arxiv/papers/2005/2005.05424.pdf) you'll see that the condition number can get *super* wild. So defining the correct amount of decimation would be difficult.

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-08-14 18:09:23 UTC  
> Updated at: 2020-08-14 18:16:00 UTC  
> Url: https://github.com/boostorg/math/issues/419#issuecomment-674198399  

Ok, just checked with this diff:  
  
```  
diff --git a/example/airy_ulps_plot.cpp b/example/airy_ulps_plot.cpp  
index 695d49287..298eae417 100644  
--- a/example/airy_ulps_plot.cpp  
+++ b/example/airy_ulps_plot.cpp  
@@ -6,12 +6,13 @@  
 #include <boost/math/tools/ulps_plot.hpp>  
 #include <boost/core/demangle.hpp>  
 #include <boost/math/special_functions/airy.hpp>  
+#include <boost/multiprecision/float128.hpp>  
  
 using boost::math::tools::ulps_plot;  
  
 int main() {  
-    using PreciseReal = long double;  
-    using CoarseReal = float;  
+    using PreciseReal = boost::multiprecision::float128;  
+    using CoarseReal = double;  
  
     typedef boost::math::policies::policy<  
       boost::math::policies::promote_float<false>,  
@@ -26,7 +27,7 @@ int main() {  
     };  
  
     std::string filename = "airy_ai_" + boost::core::demangle(typeid(CoarseReal).name()) + ".svg";  
-    int samples = 10000;  
+    int samples = 100000;  
```  
  
we have:  
  
```  
$ time ./example/airy_ai_ulps_plot.x  
./example/airy_ulps_plot.x  66.77s user 0.20s system 99% cpu 1:07.11 total  
```  
  
Adding in this diff:  
  
  
```  
diff --git a/include/boost/math/tools/ulps_plot.hpp b/include/boost/math/tools/ulps_plot.hpp  
index 3d941f444..195dfaa44 100644  
--- a/include/boost/math/tools/ulps_plot.hpp  
+++ b/include/boost/math/tools/ulps_plot.hpp  
@@ -528,7 +528,8 @@ ulps_plot<F, PreciseReal, CoarseReal>::ulps_plot(F hi_acc_impl, CoarseReal a, Co  
         if (y != 0)  
         {  
             // Maybe cond_ is badly names; should it be half_cond_?  
-            cond_[i] = boost::math::tools::evaluation_condition_number(hi_acc_impl, precise_abscissas_[i])/2;  
+            //cond_[i] = boost::math::tools::evaluation_condition_number(hi_acc_impl, precise_abscissas_[i])/2;  
+            cond_[i] = abs(precise_abscissas_[i]*boost::math::differentiation::finite_difference_derivative<decltype(hi_acc_impl), PreciseReal, 1>(hi_acc_impl, precise_abscissas_[i])/y)/2;  
```  
  
we get a visually identical `ulps_plot`, but:  
  
```  
time ./example/airy_ulps_plot.x  
./example/airy_ulps_plot.x  23.03s user 0.11s system 98% cpu 23.420 total  
```  
  
and this diff  
  
```  
diff --git a/include/boost/math/tools/ulps_plot.hpp b/include/boost/math/tools/ulps_plot.hpp  
index 3d941f444..841c3c3b3 100644  
--- a/include/boost/math/tools/ulps_plot.hpp  
+++ b/include/boost/math/tools/ulps_plot.hpp  
@@ -522,13 +522,16 @@ ulps_plot<F, PreciseReal, CoarseReal>::ulps_plot(F hi_acc_impl, CoarseReal a, Co  
     }  
  
     cond_.resize(samples, std::numeric_limits<PreciseReal>::quiet_NaN());  
+    using std::sqrt;  
+    PreciseReal h = sqrt(std::numeric_limits<PreciseReal>::epsilon());  
     for (size_t i = 0 ; i < samples; ++i)  
     {  
         PreciseReal y = precise_ordinates_[i];  
         if (y != 0)  
         {  
             // Maybe cond_ is badly names; should it be half_cond_?  
-            cond_[i] = boost::math::tools::evaluation_condition_number(hi_acc_impl, precise_abscissas_[i])/2;  
+            PreciseReal df = (hi_acc_impl(precise_abscissas_[i] + h) - y)/h;  
+            cond_[i] = abs(precise_abscissas_[i]*df/y)/2;  
```  
  
gets us  
  
```bash  
$ time ./example/airy_ulps_plot.x  
./example/airy_ulps_plot.x  15.18s user 0.09s system 99% cpu 15.274 total  
```

---

## Comment 4

> Username: NAThompson  
> Created at: 2020-09-02 14:22:32 UTC  
> Url: https://github.com/boostorg/math/issues/419#issuecomment-685770236  

@jzmaddock : Do any of these fixes look appealing to hou?

---

## Comment 5

> Username: jzmaddock  
> Created at: 2020-09-02 17:42:52 UTC  
> Url: https://github.com/boostorg/math/issues/419#issuecomment-685893680  

Apologies, I haven't had a chance to try them out - hopefully soon - honest!
