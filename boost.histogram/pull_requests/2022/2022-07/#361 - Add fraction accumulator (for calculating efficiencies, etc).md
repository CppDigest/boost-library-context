# #361 Add fraction accumulator (for calculating efficiencies, etc) [Merged]

> Username: gohil-jay  
> Created at: 2022-07-23 03:42:47 UTC  
> Updated at: 2022-09-28 09:18:46 UTC  
> Merged at: 2022-09-28 09:18:46 UTC  
> Closed at: 2022-09-28 09:18:46 UTC  
> Url: https://github.com/boostorg/histogram/pull/361  

Added standard efficiency accumulator with value(), variance() and confidence_interval()  
  
Working [here](https://godbolt.org/z/4dqbWh5jr).

---

## Review 1 [Changes requested]

> Username: HDembinski  
> Created_at: 2022-07-24 22:41:44 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1048525558  

📁 include/boost/histogram/accumulators/efficiency.hpp

```diff
   1 |+ // Copyright 2019 Hans Dembinski
```

> Username: HDembinski  
> Created_at: 2022-07-23 10:40:30 UTC  
> Updated_at: 2022-07-24 22:41:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r928108403  

Add your name here before mine, you wrote it :)

> Username: HDembinski  
> Created_at: 2022-07-23 10:40:45 UTC  
> Updated_at: 2022-07-24 22:41:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r928108417  

Also update the year to current year

---

📁 include/boost/histogram/accumulators/efficiency.hpp

```diff
   9 |+ 
  10 |+ #include <boost/core/nvp.hpp>
  11 |+ #include <boost/histogram/detail/atomic_number.hpp>
```

> Username: HDembinski  
> Created_at: 2022-07-23 10:41:08 UTC  
> Updated_at: 2022-07-24 22:41:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r928108470  

Remove this include (line 11)

---

📁 include/boost/histogram/accumulators/efficiency.hpp

```diff
  10 |+ #include <boost/core/nvp.hpp>
  11 |+ #include <boost/histogram/detail/atomic_number.hpp>
  12 |+ #include <boost/histogram/fwd.hpp> // for efficiency<>
```

> Username: HDembinski  
> Created_at: 2022-07-23 10:41:52 UTC  
> Updated_at: 2022-07-24 22:41:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r928108541  

line 12: You also need to add the efficiency class as a forward declaration to boost/histogram/fwd.hpp. Right now this line is lying.

> Username: gohil-jay  
> Created_at: 2022-07-26 23:26:31 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r930491689  

Fixed now.

---

📁 include/boost/histogram/accumulators/efficiency.hpp

```diff
  13 |+ #include <type_traits>
  14 |+ #include <boost/format.hpp>
  15 |+ #include <boost/histogram.hpp>
```

> Username: HDembinski  
> Created_at: 2022-07-23 10:42:59 UTC  
> Updated_at: 2022-07-24 22:41:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r928108644  

These three headers should be all removed. We only want the include headers that are actually needed for the code to compile.

---

📁 include/boost/histogram/accumulators/efficiency.hpp

```diff
  21 |+     Jeffreys=2,
  22 |+     ClopperPearson=3,
  23 |+     AgrestiCoull=4
```

> Username: HDembinski  
> Created_at: 2022-07-23 10:43:43 UTC  
> Updated_at: 2022-07-24 22:41:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r928108697  

Also start the enum values with a small letter in Boost.

> Username: HDembinski  
> Created_at: 2022-07-23 10:44:26 UTC  
> Updated_at: 2022-07-24 22:41:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r928108754  

Do not add the numbers explicitly, in other words, remove `=0` etc.

> Username: gohil-jay  
> Created_at: 2022-07-26 23:26:57 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r930491869  

Done :)

---

📁 include/boost/histogram/accumulators/efficiency.hpp

```diff
  16 |+ #include <cmath>
  17 |+ 
  18 |+ enum interval_type {
```

> Username: HDembinski  
> Created_at: 2022-07-23 10:44:01 UTC  
> Updated_at: 2022-07-24 22:41:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r928108728  

Make this an enum class

---

📁 include/boost/histogram/accumulators/efficiency.hpp

```diff
  25 |+ 
  26 |+ struct efficiency {
  27 |+     // return value is ignored, so we use void
```

> Username: HDembinski  
> Created_at: 2022-07-23 10:44:45 UTC  
> Updated_at: 2022-07-24 22:41:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r928108770  

This comment can be removed.

---

📁 include/boost/histogram/accumulators/efficiency.hpp

```diff
  33 |+     double value() const { return n_success_/(n_success_+n_failure_); }
  34 |+     
  35 |+     double variance() const { return n_success_*n_failure_*(n_success_+n_failure_); }
```

> Username: HDembinski  
> Created_at: 2022-07-23 10:45:39 UTC  
> Updated_at: 2022-07-24 22:41:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r928108885  

line 35: Please add a code comment where exactly you got this formula from.

> Username: gohil-jay  
> Created_at: 2022-07-26 23:27:36 UTC  
> Updated_at: 2022-07-26 23:27:37 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r930492125  

Added source Wikipedia in comment

---

📁 include/boost/histogram/accumulators/efficiency.hpp

```diff
  35 |+     double variance() const { return n_success_*n_failure_*(n_success_+n_failure_); }
  36 |+ 
  37 |+     double confidence_interval(interval_type CI_type = Wald, int conf_level = 95) const {
```

> Username: HDembinski  
> Created_at: 2022-07-23 10:48:08 UTC  
> Updated_at: 2022-07-24 22:41:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r928109134  

`conf_level` should be a double not an int, and since it is a probability, it should have the range [0, 1]. The standard confidence level should be 0.683, not 0.95.  
  
The name of the `interval_type` variable is not nice, it should be lowercase and short, I propose just `type`.

> Username: HDembinski  
> Created_at: 2022-07-23 10:48:25 UTC  
> Updated_at: 2022-07-24 22:41:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r928109160  

Similarly, `conf_level` should be `cl`

> Username: HDembinski  
> Created_at: 2022-07-23 11:01:01 UTC  
> Updated_at: 2022-07-24 22:41:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r928110284  

The return type of a confidence interval is an interval, not a number. You are returning double. Instead, you should return `std::pair<double, double>`. You need to `#include <utilities>` for use that type.

> Username: HDembinski  
> Created_at: 2022-07-24 16:10:12 UTC  
> Updated_at: 2022-07-24 22:41:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r928280215  

Note: We have a nice arithmetic interval type in Boost, see https://www.boost.org/doc/libs/1_79_0/libs/numeric/interval/doc/interval.htm, but again we don't want to use it here to not couple Boost Histogram to Boost Interval just for that. We aim to keep the dependencies between Boost libraries minimal.

> Username: gohil-jay  
> Created_at: 2022-07-26 23:32:46 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r930494382  

All variables changed.  
  
Changed to std::pair<double, double> too (godbolt [here](https://godbolt.org/z/sx4Edj41Y))

---

📁 include/boost/histogram/accumulators/efficiency.hpp

```diff
  40 |+         else if (conf_level >= 98 && conf_level < 99) {z = 2.326; }
  41 |+         else if (conf_level >= 95 && conf_level < 98) {z = 1.96; }
  42 |+         else {z = 1.645; }
```

> Username: HDembinski  
> Created_at: 2022-07-23 10:49:08 UTC  
> Updated_at: 2022-07-24 22:41:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r928109229  

This code does not work for arbitrary values of the confidence level, but that's the goal.

> Username: HDembinski  
> Created_at: 2022-07-23 11:15:55 UTC  
> Updated_at: 2022-07-24 22:41:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r928111645  

https://en.wikipedia.org/wiki/Probit  
  
To perform the calculation of z for arbitrary cl, we need an approximation to the inverse error function. There is no inverse error function in the C++ standard library, so we have to implement it ourselves. There is a high-quality inverse error function in the Boost Math library, but we don't want to make Boost Histogram depend on Boost Math just for that.  
  
Some simple implementations are listed here:  
https://stackoverflow.com/questions/27229371/inverse-error-function-in-c

> Username: gohil-jay  
> Created_at: 2022-07-26 23:33:18 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r930494631  

This was most fun, but done!

---

📁 include/boost/histogram/accumulators/efficiency.hpp

```diff
  48 |+             case Jeffreys: return 0; // implement if needed
  49 |+             case ClopperPearson: return 0; // implement if needed
  50 |+             case AgrestiCoull: return 0; // implement if needed
```

> Username: HDembinski  
> Created_at: 2022-07-23 10:51:08 UTC  
> Updated_at: 2022-07-24 22:41:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r928109399  

To make these formulas unit-testable individually, please implement them as free functions which are just called here. In Boost, we put such "private" functions in the `detail` namespace to indicate that users should not touch them.

> Username: HDembinski  
> Created_at: 2022-07-23 10:51:37 UTC  
> Updated_at: 2022-07-24 22:41:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r928109441  

If you browse the Boost Histogram source code you can see how the detail namespace is used in other places.

> Username: HDembinski  
> Created_at: 2022-07-23 11:05:27 UTC  
> Updated_at: 2022-07-24 22:41:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r928110692  

In C++, you are not allowed to use `pow`, you should use `std::pow`. Even if the code compiles, it is wrong in C++ to use `pow`, `log`, `exp` etc without the namespace `std`.

> Username: gohil-jay  
> Created_at: 2022-07-26 23:32:56 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r930494464  

All done

---

📁 include/boost/histogram/accumulators/efficiency.hpp

```diff
  50 |+             case AgrestiCoull: return 0; // implement if needed
  51 |+         };
  52 |+         return 0;
```

> Username: HDembinski  
> Created_at: 2022-07-23 11:00:00 UTC  
> Updated_at: 2022-07-24 22:41:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r928110225  

You may not return 0 here. If the code ever arrives here, it is a programmer error and therefore we need to produce some kind of error. You should add a comment `// code should never arrive here` and then return `std::numeric_limits<double>::quiet_NaN()` instead of zero. To make that work, you need to `#include <limits>`.

> Username: gohil-jay  
> Created_at: 2022-07-26 23:33:58 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r930494884  

Returned std::pair with both values `std::numeric_limits<double>::quiet_NaN()` !


---

## Review 2 [Commented]

> Username: HDembinski  
> Created_at: 2022-07-27 20:19:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1053177954  

📁 include/boost/histogram/detail/wilsonInterval.hpp

```diff
  15 |+ 
  16 |+ template <class T>
  17 |+ T wilsonInterval(T n_failure_, T n_success_, T z) {
```

> Username: HDembinski  
> Created_at: 2022-07-27 20:19:30 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r931546156  

looks good, but use snake_case: `wilson_interval`


---

## Review 3 [Commented]

> Username: HDembinski  
> Created_at: 2022-07-27 20:19:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1053178394  

📁 include/boost/histogram/detail/wilsonInterval.hpp

```diff
  16 |+ template <class T>
  17 |+ T wilsonInterval(T n_failure_, T n_success_, T z) {
  18 |+   return (z/(n_failure_+n_success_+(std::pow(z, 2))))*(std::pow(((n_failure_*n_success_)/(n_failure_ + n_success_))+((std::pow(z,2))/4), 0.5)); // Source: Wilson Interval from Confidence Interval | Wikipedia
```

> Username: HDembinski  
> Created_at: 2022-07-27 20:19:54 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r931546438  

Add a comment where you got that formula from (link to Wikipedia)


---

## Review 4 [Commented]

> Username: HDembinski  
> Created_at: 2022-07-27 20:20:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1053178880  

📁 include/boost/histogram/detail/waldInterval.hpp

```diff
  15 |+ 
  16 |+ template <class T>
  17 |+ T waldInterval(T n_failure_, T n_success_, T z) {
```

> Username: HDembinski  
> Created_at: 2022-07-27 20:20:22 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r931546781  

snake_case

> Username: HDembinski  
> Created_at: 2022-07-27 20:26:43 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r931551419  

We don't use the trailing underscore for parameters, only for private members of a class.


---

## Review 5 [Commented]

> Username: HDembinski  
> Created_at: 2022-07-27 20:20:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1053179181  

📁 include/boost/histogram/detail/waldInterval.hpp

```diff
  16 |+ template <class T>
  17 |+ T waldInterval(T n_failure_, T n_success_, T z) {
  18 |+   return (z*(std::pow((n_failure_*n_success_), 0.5))) / std::pow(n_failure_+n_success_, 1.5); // Source: Wald Interval from Confidence Interval | Wikipedia
```

> Username: HDembinski  
> Created_at: 2022-07-27 20:20:38 UTC  
> Updated_at: 2022-07-27 20:20:39 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r931546985  

comment where this formula comes from

> Username: HDembinski  
> Created_at: 2022-07-27 20:26:08 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r931551027  

T(0.5) instead of 0.5 etc.


---

## Review 6 [Commented]

> Username: HDembinski  
> Created_at: 2022-07-27 20:21:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1053179586  

📁 include/boost/histogram/accumulators/efficiency.hpp

```diff
  20 |+     wilson,
  21 |+     jeffreys,
  22 |+     agrestiCoull,
```

> Username: HDembinski  
> Created_at: 2022-07-27 20:21:02 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r931547253  

agresti_coull


---

## Review 7 [Commented]

> Username: HDembinski  
> Created_at: 2022-07-27 20:21:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1053179717  

📁 include/boost/histogram/accumulators/efficiency.hpp

```diff
  21 |+     jeffreys,
  22 |+     agrestiCoull,
  23 |+     clopperPearson
```

> Username: HDembinski  
> Created_at: 2022-07-27 20:21:09 UTC  
> Updated_at: 2022-07-27 20:21:10 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r931547332  

clopper_pearson


---

## Review 8 [Commented]

> Username: HDembinski  
> Created_at: 2022-07-27 20:25:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1053184407  

📁 include/boost/histogram/detail/probit.hpp

```diff
  32 |+ template <class T>
  33 |+ T probit(T x){
  34 |+    return (-1)*((std::pow(2,0.5))*ErfInv((2*((1-x)/2))-1)) ;
```

> Username: HDembinski  
> Created_at: 2022-07-27 20:25:41 UTC  
> Updated_at: 2022-07-27 20:25:42 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r931550684  

std::sqrt(2) is faster and more readable than std::pow(2, 0.5). Instead of dividing by 2 it is faster to multiply with 0.5. Avoid using integers, all literals in a templated function like this should be encapsulated with a T, for example, 1 becomes T(1) and 0.5 becomes T(0.5).


---

## Review 9 [Commented]

> Username: HDembinski  
> Created_at: 2022-07-27 20:26:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1053185701  

📁 include/boost/histogram/detail/probit.hpp

```diff
  17 |+ 
  18 |+ template <class T>
  19 |+ T ErfInv(T x){
```

> Username: HDembinski  
> Created_at: 2022-07-27 20:26:55 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r931551599  

snake_case


---

## Review 10 [Commented]

> Username: HDembinski  
> Created_at: 2022-07-27 20:27:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1053185922  

📁 include/boost/histogram/detail/probit.hpp

```diff
  22 |+ 
  23 |+    x = (1 - x)*(1 + x);        // x = 1 - x*x;
  24 |+    lnx = logf(x);
```

> Username: HDembinski  
> Created_at: 2022-07-27 20:27:08 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r931551746  

std:log instead of logf


---

## Review 11 [Commented]

> Username: HDembinski  
> Created_at: 2022-07-27 20:28:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1053187271  

📁 include/boost/histogram/detail/probit.hpp

```diff
  24 |+    lnx = logf(x);
  25 |+ 
  26 |+    tt1 = 2/(PI*0.15449436008930206298828125) + 0.5f * lnx;
```

> Username: HDembinski  
> Created_at: 2022-07-27 20:28:25 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r931552683  

`PI` is not available on all platforms. The best is to replace PI*0.15449436008930206298828125 by 0.4853583466776075

> Username: HDembinski  
> Created_at: 2022-07-27 20:29:14 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r931553318  

Or better just replace 2/(...) by 4.120666747961526


---

## Review 12 [Commented]

> Username: HDembinski  
> Created_at: 2022-07-27 20:30:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1053189885  

📁 include/boost/histogram/detail/probit.hpp

```diff
  19 |+ T ErfInv(T x){
  20 |+    T tt1, tt2, lnx, sgn;
  21 |+    sgn = (x < 0) ? -1.0f : 1.0f;
```

> Username: HDembinski  
> Created_at: 2022-07-27 20:30:53 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r931554559  

In C++, it is best to use std::copysign, https://en.cppreference.com/w/cpp/numeric/math/copysign


---

## Review 13 [Commented]

> Username: HDembinski  
> Created_at: 2022-07-27 20:31:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1053190959  

📁 include/boost/histogram/detail/probit.hpp

```diff
  10 |+ #include <cmath>
  11 |+ 
  12 |+ # define PI 3.14159265358979323846  /* pi */
```

> Username: HDembinski  
> Created_at: 2022-07-27 20:31:53 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r931555324  

We only use the preprocessor as last resort, because it is evil. Best is to just replace PI in the code below with a literal, see comment there. Please remove line 12.


---

## Review 14 [Commented]

> Username: HDembinski  
> Created_at: 2022-07-27 20:34:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1053193196  

📁 include/boost/histogram/accumulators/efficiency.hpp

```diff
  43 |+             case interval_type::jeffreys: return std::make_pair(0, 0); // implement if needed
  44 |+             case interval_type::clopperPearson: return std::make_pair(0, 0); // implement if needed
  45 |+             case interval_type::agrestiCoull: return std::make_pair(0, 0); // implement if needed
```

> Username: HDembinski  
> Created_at: 2022-07-27 20:34:04 UTC  
> Updated_at: 2022-07-27 20:35:15 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r931556915  

I think it is easier and more readable if `detail::waldInterval` etc returns `std::pair`, which saves you some repetition here and some inefficiency: you are calling the function twice with the same arguments.

> Username: HDembinski  
> Created_at: 2022-07-27 20:34:59 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r931557548  

In other words, `detail::waldInterval` and the others should accepted `p`, `n_failure_`, `n_success_` and `z`.


---

## Review 15 [Commented]

> Username: HDembinski  
> Created_at: 2022-07-27 20:36:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1053195173  

📁 include/boost/histogram/accumulators/efficiency.hpp

```diff
  32 |+     double value() const { return n_success_/(n_success_+n_failure_); }
  33 |+     
  34 |+     double variance() const { return n_success_*n_failure_*(n_success_+n_failure_); } // Source: Variance from Binomial Distribution | Wikipedia
```

> Username: HDembinski  
> Created_at: 2022-07-27 20:36:03 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r931558351  

line too long, put the comment inside the function  
```  
double variance() const {  
   // Source ...  
   return n_success_ ...  
}


---

## Review 16 [Commented]

> Username: HDembinski  
> Created_at: 2022-07-27 20:37:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1053196335  

📁 include/boost/histogram/accumulators/efficiency.hpp

```diff
  24 |+ };
  25 |+ 
  26 |+ struct efficiency {
```

> Username: HDembinski  
> Created_at: 2022-07-27 20:37:11 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r931559194  

You describe efficiency in fwd.hpp as a templated type, but this type is not templated. We eventually want to make efficiency templated on the type of the counters, right now it is hard-coded to double. We want to eventually make efficiency templated, but I think you should first write some unit tests and then template this class once the unit tests are in place.


---

## Comment 17

> Username: HDembinski  
> Created_at: 2022-07-27 20:40:19 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1197343528  

Hi @gohil-jay , great progress! I added new comments. I think the next step for you is to write some unit tests. The tests are located in the folder tests. We do the testing with the lightweight tests in Boost Core. Basic documentation is here: https://www.boost.org/doc/libs/1_79_0/libs/core/doc/html/core/lightweight_test.html.  
  
Have a look how testing is done for other accumulators and try to make a minimal test for your efficiency accumulator. You could also start simpler and test your `detail::waldInterval` (that you should rename to detail::wald_interval`.

---

## Comment 18

> Username: HDembinski  
> Created_at: 2022-07-27 21:03:04 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1197365096  

Most of the tests are failing, because I made a mistake, I am fixing that now.

---

## Comment 19

> Username: HDembinski  
> Created_at: 2022-07-27 21:05:35 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1197367431  

Please merge the develop branch into this one to get the fix that I just commited. In the terminal, do `git merge develop` when the currently active checkout is your branch.

---

## Comment 20

> Username: gohil-jay  
> Created_at: 2022-07-27 21:12:09 UTC  
> Updated_at: 2022-07-27 21:16:22 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1197373439  

This is what I see...  
  
![image](https://user-images.githubusercontent.com/59703162/181373136-4e6d5237-6ed1-4b49-8081-88c68a687fed.png)  
  
Are we trying to merge the developV1 branch into develop branch?

---

## Comment 21

> Username: HDembinski  
> Created_at: 2022-07-28 15:00:23 UTC  
> Updated_at: 2022-07-30 17:03:59 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1198269301  

Edit: This has been fixed (temporarily?) by fixing the types of the helper functions to double, so can be regarded as resolved.  
  
I didn't find a nice way of fixing the warning, so you have to use the ugly way. Something along these lines will silence the warning:  
  
```cpp  
#include <array>  
#include <boost/config/workaround.hpp>  
  
int main() {  
  
#if BOOST_WORKAROUND(BOOST_MSVC, >= 0)  
#pragma warning(disable : 4305)  
#pragma warning(disable : 4838)  
#endif  
  std::array<float, 2> x = {1.5, 2.1};  
#if BOOST_WORKAROUND(BOOST_MSVC, >= 0)  
#pragma warning(default : 4305)  
#pragma warning(default : 4838)  
#endif  
  
  return 0;  
}  
```

---

## Comment 22

> Username: HDembinski  
> Created_at: 2022-07-28 17:24:18 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1198431555  

Can you please do `git checkout develop & git pull & git merge develop` again? This will fix the issue you had with the DEBUG macro on Windows.

---

## Comment 23

> Username: gohil-jay  
> Created_at: 2022-07-28 18:07:24 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1198469387  

Done!

---

## Comment 24

> Username: HDembinski  
> Created_at: 2022-07-30 07:16:21 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1200107306  

Hi Jay, looks like all tests pass now, but you still need to fix the warning in detail_probit_test with the code in my earlier comment.  
  
I have been thinking about the interface of this class, and we need to iterate it some more. Here are the requirements.  
  
- The class should have minimal size, because we are potentially creating a lot of them. Minimal is two counters for successes and failures.  
- We do not want to compute the conversion from CL to z every time someone is calling `confidence_interval`, because this is going to be called a lot and the conversion is expensive. We could pre-compute this when the class is created, but this is still expensive, since the code will be run for each accumulator instance that is created, and we potentially create a lot of them. Also, we then have to store the result of the computation in the class, and then the class is not of minimal size.  
- Users are not going to switch the CL or the type of confidence interval dynamically, they will settle for one setting and then keep going with that. This means we can make these configurations part of the type, by using template parameters. Floating point numbers cannot be used as non-type template parameters, but we can use `std::ratio` instead https://stackoverflow.com/questions/2183087/why-cant-i-use-float-value-as-a-template-parameter.  
- We should allow users to pass `z` directly without going through the CL to `z` conversion, because at least in physics we often express confidence levels in `z` sigma  (number of standard deviations of a normal distribution). 5 sigma is easier to mentally parse than 0.9999994266968563.  
  
I will play around with a few ideas on Godbolt. Once I have something, I will present it here. You should then let me know when you are done with your current changes so that I can integrate this part without us creating conflicts for each other by making changes simultaneously.

---

## Comment 25

> Username: HDembinski  
> Created_at: 2022-07-30 09:02:17 UTC  
> Updated_at: 2022-07-30 09:06:55 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1200120592  

@gohil-jay After looking at the Clopper-Pearson interval, I think we cannot avoid to use Boost Math at least for this interval. I don't want to re-implement the regularised incomplete beta function in our `detail` namespace. But we can probably make it so that the user only has to have Boost Math available if they really want the Clopper-Pearson interval. I will investigate this. Furthermore, the beta distribution is need for the calculation of the Jeffreys interval.

---

## Review 26 [Commented]

> Username: HDembinski  
> Created_at: 2022-07-30 10:20:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1056259701  

📁 include/boost/histogram/detail/probit.hpp

```diff
  23 |+ 
  24 |+    return std::copysign(std::sqrt(-tt1 + std::sqrt(tt1*tt1 - tt2)), x);
  25 |+ }
```

> Username: HDembinski  
> Created_at: 2022-07-30 10:20:24 UTC  
> Updated_at: 2022-07-30 17:09:47 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r933782814  

Edit: This is now resolved by my patch below, nothing left to do here.  
  
I think it is sufficient to have erf_inv_approx in double precision, and I also think that the `approx` should be in front, so please replace this with this slightly more optimized version  
```cpp  
    inline double approx_erf_inv(double x) {  
        const double lnx = std::log((1 - x)*(1 + x));  
        const double tt1 = std::fma(0.5, lnx, 4.120666747961526);  
        const double tt2 = 6.47272819164 * lnx;  
        return std::copysign(std::sqrt(-tt1 + std::sqrt(std::fma(tt1, tt1, -tt2))), x);  
    }  
```  
`inline` is needed when you define a function inside a header and it is not templated.

> Username: HDembinski  
> Created_at: 2022-07-30 10:40:23 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r933785528  

std::fma does the operation a * x + b in one CPU cycle. We need to use this explicitly here, because the compiler cannot optimize a * x + b into the fma instruction automatically unless special compiler flags are used (which are usually not turned on). The optimizer is not allowed to perform code transformations that could produce a different result.

> Username: HDembinski  
> Created_at: 2022-07-30 12:28:23 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r933800112  

You can ignore this request, because I made the change in my commit to your PR. I replaced detail/probit.hpp with detail/normal.hpp and improved the erf_inv implementation. I still leave the comments here for you to read.


---

## Comment 27

> Username: HDembinski  
> Created_at: 2022-07-30 14:29:50 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1200167608  

@gohil-jay We need do some more work on this branch, but the "changes requested" warning is annoying me, so I accepted the changes you made so far.

---

## Comment 28

> Username: HDembinski  
> Created_at: 2022-07-30 16:03:32 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1200243318  

I added two new classes which allow us to define the interval via confidence level or via a multiple of the standard deviation. In physics, this is common can now be easily supported.

---

## Comment 29

> Username: HDembinski  
> Created_at: 2022-07-31 11:10:18 UTC  
> Updated_at: 2022-07-31 11:11:05 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1200402401  

I committed another large patch. The `efficiency` accumulator is now called `fraction`, since this is a more general name.  
  
The fraction accumulator only offers the default interval, which is the Wilson score interval. Users can compute other intervals with the interval functors in the utility namespace which are publically exposed. I implemented the wilson_interval as an example for you, @gohil-jay , could you please implement the others? You will need special functions from Boost Math for clopper_pearson_interval and jeffreys_interval.  
  
But first, please add some more tests to accumulators_fraction_test.cpp.

---

## Review 30 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-02 15:31:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1058981908  

📁 include/boost/histogram/utility/clopper_pearson_interval.hpp

```diff
  24 |+   using interval_type = typename base_t::interval_type;
  25 |+ 
  26 |+   explicit clopper_pearson_interval(deviation d = deviation{1.0}) noexcept
```

> Username: HDembinski  
> Created_at: 2022-08-02 15:31:17 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r935740876  

You should accept a `confidence_level` here instead of a `deviation`. Since `deviation` and `confidence_level` are programmed to be convertible into each other, you can chose where which suits you best / requires less conversion in your implementation below in `operator()`. The Clopper-Pearson interval naturally needs the CL to compute alpha.


---

## Review 31 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-02 15:31:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1058982412  

📁 include/boost/histogram/utility/clopper_pearson_interval.hpp

```diff
  33 |+     // Source: https://en.wikipedia.org/wiki/Binomial_proportion_confidence_interval
  34 |+     const value_type alpha1 = (one - d) * half;
  35 |+     const value_type alpha2 = one - ((one - d) * half);
```

> Username: HDembinski  
> Created_at: 2022-08-02 15:31:36 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r935741248  

This part looks wrong, there is only one alpha.

> Username: HDembinski  
> Created_at: 2022-08-02 15:32:02 UTC  
> Updated_at: 2022-08-02 15:32:03 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r935741734  

And you cannot compute alpha from d, also there is no variable d in this scope here?

> Username: gohil-jay  
> Created_at: 2022-08-03 19:26:29 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r937053035  

I should rename it, it's 1st arguments for low and high variables (α/2 and 1-α/2). And yes I interchanged d with cl, so I'll α from cl/2 as you mentioned!


---

## Review 32 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-02 15:35:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1058989443  

📁 include/boost/histogram/utility/clopper_pearson_interval.hpp

```diff
  41 |+     // Source: https://en.wikipedia.org/wiki/Beta_distribution
  42 |+     const value_type beta1 = (tgamma(m1)*tgamma(n1)) / tgamma(m1+n1);
  43 |+     const value_type beta2 = (tgamma(m2)*tgamma(n2)) / tgamma(m2+n2);
```

> Username: HDembinski  
> Created_at: 2022-08-02 15:35:44 UTC  
> Updated_at: 2022-08-02 15:37:21 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r935745597  

I am not sure what you are calculating here. You need the quantiles of the beta distribution to compute the interval.  
  
https://en.wikipedia.org/wiki/Quantile  
https://en.wikipedia.org/wiki/Beta_distribution  
  
You can get those from Boost Math. Look at the documentation of Boost Math:  
https://www.boost.org/doc/libs/1_79_0/libs/math/doc/html/dist.html for general advice and  
https://www.boost.org/doc/libs/1_79_0/libs/math/doc/html/math_toolkit/dist_ref/dists/beta_dist.html for the beta distribution.  
The docs have many examples.

> Username: HDembinski  
> Created_at: 2022-08-02 15:38:31 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r935748485  

With Boost Math, the implementation should be easy, you just need to call the quantile with the arguments listed here https://en.wikipedia.org/wiki/Binomial_proportion_confidence_interval#Clopper%E2%80%93Pearson_interval

> Username: gohil-jay  
> Created_at: 2022-08-03 19:28:29 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r937054532  

It's for calculating B(α, β) for f(x;α,β) for the intervals; = (α+1)!*(β+1)! / (α+β+1)! and tgamma(x) = (x-1)!  
  
(Can explain in a meet)

> Username: HDembinski  
> Created_at: 2022-08-04 08:31:59 UTC  
> Updated_at: 2022-08-04 08:32:00 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r937502636  

This is not calculating B(alpha, beta). B(alpha, beta) is the quantile of a pdf which does not have a closed form. You cannot implement the formula this way. In fact, you cannot implement the formula in code at all. Please read the references that I linked to.

> Username: HDembinski  
> Created_at: 2022-08-04 08:32:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r937503401  

Your only option is to use the beta distribution from Boost Math, so please do that instead of trying other solutions that do not work.


---

## Review 33 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-02 15:44:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1059002801  

📁 include/boost/histogram/utility/agresti_coull_interval.hpp

```diff
  32 |+     const value_type n = ns + nf;
  33 |+     const value_type n_ac = n + zsq;
  34 |+     const value_type a = (ns + (zsq * half)) / n_ac;
```

> Username: HDembinski  
> Created_at: 2022-08-02 15:44:33 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r935754713  

Please remove the superfluous parentheses.


---

## Review 34 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-02 15:46:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1059005761  

📁 include/boost/histogram/utility/jeffreys_interval.hpp

```diff
  25 |+ 
  26 |+   explicit jeffreys_interval(deviation d = deviation{1.0}) noexcept
  27 |+       : z_{static_cast<double>(d)} {}
```

> Username: HDembinski  
> Created_at: 2022-08-02 15:46:31 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r935756724  

Like for the Clopper-Pearson interval, you rather want to accept `confidence_level` here instead of `deviation`.


---

## Review 35 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-02 15:48:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1059009356  

📁 include/boost/histogram/utility/jeffreys_interval.hpp

```diff
  41 |+     // Source: https://repository.upenn.edu/cgi/viewcontent.cgi?article=1440&context=statistics_papers
  42 |+     const value_type a = (one / beta) * std::pow(alpha1, m - one) * std::pow(1 - alpha1, n - one);
  43 |+     const value_type b = (one / beta) * std::pow(alpha2, m - one) * std::pow(1 - alpha2, n - one);
```

> Username: HDembinski  
> Created_at: 2022-08-02 15:48:54 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r935759208  

Like the implementation of Clopper-Pearson, this is probably wrong. Also here, you need the quantiles of the beta distribution. See my comments in `clopper_pearson_interval`.


---

## Review 36 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-02 15:50:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1059011205  

📁 include/boost/histogram/utility/wald_interval.hpp

```diff
  30 |+     const value_type ns = successes, nf = failures;
  31 |+     const value_type n = ns + nf;
  32 |+     const value_type a = (ns) / (n);
```

> Username: HDembinski  
> Created_at: 2022-08-02 15:50:11 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r935760577  

Please remove the superfluous parentheses.


---

## Review 37 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-02 15:52:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1059014164  

📁 include/boost/histogram/utility/wald_interval.hpp

```diff
  31 |+     const value_type n = ns + nf;
  32 |+     const value_type a = (ns) / (n);
  33 |+     const value_type b = (z_ / (n) * std::sqrt(n)) * std::sqrt(ns * nf);
```

> Username: HDembinski  
> Created_at: 2022-08-02 15:52:11 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r935762633  

It is numerically more stable (after faster in this case, too) you group all the computations under the square roots together. You can do `sqrt(ns * nf / n)`.


---

## Review 38 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-02 15:59:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1059024878  

📁 include/boost/histogram/utility/binomial_proportion_interval.hpp

```diff
  19 |+ template <class ValueType>
  20 |+ class binomial_proportion_interval {
  21 |+ public:
```

> Username: HDembinski  
> Created_at: 2022-08-02 15:59:22 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r935770163  

@gohil-jay Please add a `static_assert` here to constrain the `ValueType` to floating point numbers. You can use the template functions in the header `type_traits` from the standard library to check whether `ValueType` is a floating point type.


---

## Review 39 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-02 16:02:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1059029950  

📁 test/accumulators_fraction_test.cpp

```diff
  22 |+     f_t f(1, 2);
  23 |+     BOOST_TEST_EQ(f.successes(), 1);
  24 |+     BOOST_TEST_EQ(f.failures(), 2);
```

> Username: HDembinski  
> Created_at: 2022-08-02 16:02:57 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r935773629  

Jay, I observe a tendency of you to tackle the difficult tasks before the easy tasks. Remember, I asked you to add tests here before implementing the intervals. Can you please add some tests here?

> Username: gohil-jay  
> Created_at: 2022-08-06 00:06:00 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r939452422  

Will do this next! Already started.


---

## Review 40 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-06 11:09:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1064297295  

📁 include/boost/histogram/utility/agresti_coull_interval.hpp

```diff
  28 |+ 
  29 |+   interval_type operator()(value_type successes, value_type failures) const noexcept {
  30 |+     const double half{0.5}, one{1.0}, zsq = z_ * z_;
```

> Username: HDembinski  
> Created_at: 2022-08-06 11:09:03 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r939516579  

if you use declare them to be `double`, you don't need the constants `half` and `one`, you can just use the numbers in the formula directly. But after switching back and forth, I think it is correct to replace `double` with `value_type` here.  
  
What we really want is to ensure that `value_type` is a floating_point number. The `value_type` of the interval is not necessarily the same value_type that the class `fraction` uses. The latter can be an integer, but the value_type of the interval classes must be of floating point type.

> Username: HDembinski  
> Created_at: 2022-08-06 11:10:04 UTC  
> Updated_at: 2022-08-06 11:10:05 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r939516683  

This is already ensured by the base class `binomial_proportion_interval`.

> Username: HDembinski  
> Created_at: 2022-08-06 11:14:31 UTC  
> Updated_at: 2022-08-06 11:14:32 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r939517042  

In other words, please revert this change.


---

## Review 41 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-06 11:13:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1064297619  

📁 include/boost/histogram/accumulators/fraction.hpp

```diff
  61 |-   value_type fail_ = 0;
  60 |+   double succ_ = 0;
  61 |+   double fail_ = 0;
```

> Username: HDembinski  
> Created_at: 2022-08-06 11:13:50 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r939516984  

This replacement is wrong, the others are correct. Please revert.


---

## Review 42 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-06 11:14:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1064297655  

📁 include/boost/histogram/utility/agresti_coull_interval.hpp

```diff
  35 |-     const value_type b = z_ * std::sqrt((one - a) * a / n_ac);
  34 |+     const double a = (ns + zsq * half) / n_ac;
  35 |+     const double b = z_ * std::sqrt((one - a) * a / n_ac);
```

> Username: HDembinski  
> Created_at: 2022-08-06 11:14:14 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r939517011  

This replacement is wrong, please revert.


---

## Review 43 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-06 11:15:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1064297768  

📁 include/boost/histogram/utility/clopper_pearson_interval.hpp

```diff
  29 |   interval_type operator()(value_type successes, value_type failures) const noexcept {
  30 |-     const value_type half{0.5}, one{1.0};
  30 |+     const double half{0.5}, one{1.0};
```

> Username: HDembinski  
> Created_at: 2022-08-06 11:15:35 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r939517133  

Please revert this.


---

## Review 44 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-06 11:16:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1064297808  

📁 include/boost/histogram/utility/clopper_pearson_interval.hpp

```diff
  48 |     // Source: https://en.wikipedia.org/wiki/Binomial_proportion_confidence_interval#Clopper%E2%80%93Pearson_interval
  45 |-     const value_type a = (one / beta1) * std::pow(alpha1, m1 - one) * std::pow(1 - alpha1, n1 - one);
  46 |-     const value_type b = (one / beta2) * std::pow(alpha2, m2 - one) * std::pow(1 - alpha2, n2 - one);
```

> Username: HDembinski  
> Created_at: 2022-08-06 11:16:02 UTC  
> Updated_at: 2022-08-06 11:16:03 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r939517165  

Use `value_type` instead of `double` everywhere.


---

## Review 45 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-06 11:16:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1064297832  

📁 include/boost/histogram/utility/clopper_pearson_interval.hpp

```diff
  54 | private:
  51 |-   double z_;
  55 |+   double cl_;
```

> Username: HDembinski  
> Created_at: 2022-08-06 11:16:19 UTC  
> Updated_at: 2022-08-06 11:16:20 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r939517212  

should also be `value_type`


---

## Review 46 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-06 11:25:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1064298495  

📁 include/boost/histogram/utility/wilson_interval.hpp

```diff
  42 |-     const value_type b = z_ / (n + zsq) * std::sqrt(ns * nf / n + quarter * zsq);
  41 |+     const double a = (ns + half * zsq) / (n + zsq);
  42 |+     const double b = z_ / (n + zsq) * std::sqrt(ns * nf / n + quarter * zsq);
```

> Username: HDembinski  
> Created_at: 2022-08-06 11:25:20 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r939517877  

please revert


---

## Review 47 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-06 11:25:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1064298527  

📁 include/boost/histogram/utility/wald_interval.hpp

```diff
  33 |-     const value_type b = (z_ / (n) * std::sqrt(n)) * std::sqrt(ns * nf);
  32 |+     const double a = ns / n;
  33 |+     const double b = (z_ / n) * std::sqrt(ns * nf / n);
```

> Username: HDembinski  
> Created_at: 2022-08-06 11:25:43 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r939517921  

use value_type not double, otherwise good

> Username: gohil-jay  
> Created_at: 2022-08-07 02:28:46 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r939597412  

Umm... But what if it's an int ?

> Username: gohil-jay  
> Created_at: 2022-08-07 02:30:21 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r939597468  

Got it nevermind.


---

## Review 48 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-06 11:26:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1064298608  

📁 test/Jamfile

```diff
  50 |     [ run accumulators_weighted_sum_test.cpp ]
  51 |-     [ run algorithm_project_test.cpp ]
  51 |+     # [ run algorithm_project_test.cpp ]
```

> Username: HDembinski  
> Created_at: 2022-08-06 11:26:35 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r939518029  

this should not be necessary

> Username: HDembinski  
> Created_at: 2022-08-06 11:36:40 UTC  
> Updated_at: 2022-08-06 11:36:41 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r939518905  

If you are still seeing this test failing, you need to use `cxxstd=17` instead of `cxxstd=latest` in your b2 call

> Username: HDembinski  
> Created_at: 2022-08-06 11:37:11 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r939518929  

This is a bug in the microsoft compiler

> Username: gohil-jay  
> Created_at: 2022-08-07 02:28:55 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r939597418  

Noted.


---

## Review 49 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-06 11:27:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1064298720  

📁 test/odr_test.cpp

```diff
  25 |+ #include <boost/histogram/utility/wilson_interval.hpp>
  26 |+ #include <boost/histogram/detail/normal.hpp>
  27 |+ #include <boost/histogram/detail/erf_inv.hpp>
```

> Username: HDembinski  
> Created_at: 2022-08-06 11:27:53 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r939518167  

these changes are not necessary, please revert


---

## Comment 50

> Username: HDembinski  
> Created_at: 2022-08-06 11:35:26 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1207199139  

The new intervals start to look good but you started to make changes that broke things that were previously correct. I am talking especially about the changes to `fraction` and `odr_test.cpp`

---

## Review 51 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-09 13:14:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1066702842  

📁 test/accumulators_fraction_test.cpp

```diff
  25 |+     BOOST_TEST_EQ(f1.successes(), 3); BOOST_TEST_EQ(f1.failures(), 1); BOOST_TEST_EQ(f1.successes(), 3.0); BOOST_TEST_EQ(f1.failures(), 1.0);
  26 |+     BOOST_TEST_EQ(f2.successes(), 3); BOOST_TEST_EQ(f2.failures(), 5); BOOST_TEST_EQ(f2.successes(), 3.0); BOOST_TEST_EQ(f2.failures(), 5.0);
  27 |+     BOOST_TEST_EQ(f3.successes(), 1); BOOST_TEST_EQ(f3.failures(), 3); BOOST_TEST_EQ(f3.successes(), 1.0); BOOST_TEST_EQ(f3.failures(), 3.0);
```

> Username: HDembinski  
> Created_at: 2022-08-09 13:14:38 UTC  
> Updated_at: 2022-08-09 13:14:39 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r941325097  

You do not need to compare to 3 **and** 3.0,  etc., this is guaranteed by C++. Please remove the superfluous tests.

> Username: HDembinski  
> Created_at: 2022-08-09 13:14:54 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r941325363  

Otherwise this looks good.


---

## Review 52 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-09 13:15:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1066704366  

📁 test/accumulators_fraction_test.cpp

```diff
  31 |+     BOOST_TEST_EQ(f1.variance(), 12); BOOST_TEST_EQ(f1.variance(), 12.0);
  32 |+     BOOST_TEST_EQ(f2.variance(), 120); BOOST_TEST_EQ(f2.variance(), 120.0);
  33 |+     BOOST_TEST_EQ(f3.variance(), 12); BOOST_TEST_EQ(f3.variance(), 12.0);
```

> Username: HDembinski  
> Created_at: 2022-08-09 13:15:35 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r941326092  

Also here, do not compare with 12 **and** 12.0, only the first.


---

## Review 53 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-09 13:15:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1066705003  

📁 test/accumulators_fraction_test.cpp

```diff
  32 |+     BOOST_TEST_EQ(f2.variance(), 120); BOOST_TEST_EQ(f2.variance(), 120.0);
  33 |+     BOOST_TEST_EQ(f3.variance(), 12); BOOST_TEST_EQ(f3.variance(), 12.0);
  34 |+     // BOOST_TEST_EQ(f1.confidence_interval().first, 0.5); BOOST_TEST_EQ(f1.confidence_interval().second, 0.9);
```

> Username: HDembinski  
> Created_at: 2022-08-09 13:15:58 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r941326573  

You need BOOST_TEST_IS_CLOSE here.


---

## Review 54 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-09 13:18:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1066709837  

📁 test/accumulators_fraction_test.cpp

```diff
  39 | 
  30 |-   run_tests<int>();
  40 |+   // run_tests<int>(); // confidence_interval() throws error due to static_assert in binomial_proportion_interval()
```

> Username: HDembinski  
> Created_at: 2022-08-09 13:18:50 UTC  
> Updated_at: 2022-08-09 13:18:51 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r941329830  

This test exposes an error in the implementation in `fraction`. If `value_type` is an integer, which is perfectly valid, then the confidence interval computer should still return an interval in `double`. To get this working correctly requires a bit of metaprogramming that we can resolve together tomorrow in our call.


---

## Review 55 [Commented]

> Username: HDembinski  
> Created_at: 2022-08-12 08:06:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1070736524  

📁 test/accumulators_fraction_test.cpp

```diff
  44 |+     BOOST_TEST_EQ(f.successes(), 5);
  45 |+     BOOST_TEST_EQ(f.failures(), 3);
  46 |+   }
```

> Username: HDembinski  
> Created_at: 2022-08-12 08:06:12 UTC  
> Updated_at: 2022-08-12 08:06:13 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r944215558  

that's not how the converting constructor works. What you are testing here always works, even without making a special constructor. You need to test `f_t1 f1(5, 3); f_t2 f2(f1);`

> Username: HDembinski  
> Created_at: 2022-08-12 08:16:08 UTC  
> Updated_at: 2022-08-12 08:16:09 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r944223227  

The other test you added looks good. :+1:


---

## Comment 56

> Username: HDembinski  
> Created_at: 2022-08-16 15:03:29 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1216763275  

@gohil-jay Can you work on fixing the failing tests? appveyor is complaining about possible loss of data  
```  
C:\projects\boost\boost/histogram/accumulators/fraction.hpp(35): error C2220: the following warning is treated as an error  
test\accumulators_fraction_test.cpp(43): note: see reference to function template instantiation 'boost::histogram::accumulators::fraction<T>::fraction<double>(const boost::histogram::accumulators::fraction<double> &) noexcept' being compiled  
        with  
        [  
            T=int  
        ]  
test\accumulators_fraction_test.cpp(43): note: see reference to function template instantiation 'boost::histogram::accumulators::fraction<T>::fraction<double>(const boost::histogram::accumulators::fraction<double> &) noexcept' being compiled  
        with  
        [  
            T=int  
        ]  
test\accumulators_fraction_test.cpp(77): note: see reference to function template instantiation 'void run_tests<int>(void)' being compiled  
C:\projects\boost\boost/histogram/accumulators/fraction.hpp(35): warning C4244: 'argument': conversion from 'double' to 'const int', possible loss of data  
C:\projects\boost\boost/histogram/accumulators/fraction.hpp(35): warning C4244: 'argument': conversion from 'double' to 'const int', possible loss of data  
```

---

## Review 57 [Commented]

> Username: HDembinski  
> Created_at: 2022-09-18 07:56:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1111504521  

📁 test/utility_clopper_pearson_interval_test.cpp

```diff
  30 |+     BOOST_TEST_IS_CLOSE(x.first, 0.1586552539314571, deps);
  31 |+     BOOST_TEST_IS_CLOSE(x.second, 1.0, deps);
  32 |+   }
```

> Username: HDembinski  
> Created_at: 2022-09-18 07:56:24 UTC  
> Updated_at: 2022-09-18 07:56:25 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r973677767  

Why this change? You should not change the original test condition, because that should have been correct.

> Username: gohil-jay  
> Created_at: 2022-09-19 06:19:51 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r973893917  

This is a mistake on my end, I changed this while finding where the issue came from (and realized it wasn't this) and missed changing it back. I see you've fixed it.


---

## Review 58 [Commented]

> Username: HDembinski  
> Created_at: 2022-09-18 08:02:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1111505183  

📁 test/accumulators_fraction_test.cpp

```diff
  70 |+     BOOST_TEST_IS_CLOSE(ci.first, expected.first, eps);
  71 |+     BOOST_TEST_IS_CLOSE(ci.second, expected.second, eps);
  72 |+   }
```

> Username: HDembinski  
> Created_at: 2022-09-18 08:02:56 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r973678553  

This test seems superfluous. You should rather check what happens in the special case when both successes and failures is zero.

> Username: gohil-jay  
> Created_at: 2022-09-19 06:20:07 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r973894043  

Agreed. Interestingly it should show errors for 0/0 among others. I will look into it.


---

## Review 59 [Commented]

> Username: HDembinski  
> Created_at: 2022-09-18 08:03:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1111505251  

📁 test/utility_clopper_pearson_interval_test.cpp

```diff
  16 |+ 
  17 |+   // const double deps = std::numeric_limits<double>::epsilon();
  18 |+   const double deps = 0.1;
```

> Username: HDembinski  
> Created_at: 2022-09-18 08:03:28 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r973678621  

This requires a comment why deps is so large.

> Username: gohil-jay  
> Created_at: 2022-09-19 06:22:48 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r973895313  

I see your improved implementation now covers 0.001. I will take a look on how you did it.

> Username: HDembinski  
> Created_at: 2022-09-19 08:03:59 UTC  
> Updated_at: 2022-09-19 08:04:12 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r973966036  

You used alpha = CL =0.68. instead of alpha = 1 - CL =0.32. The implementation was wrong.


---

## Review 60 [Commented]

> Username: HDembinski  
> Created_at: 2022-09-18 08:03:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/361#pullrequestreview-1111505292  

📁 test/utility_jeffreys_interval_test.cpp

```diff
  16 |+ 
  17 |+   // const double deps = std::numeric_limits<double>::epsilon();
  18 |+   const double deps = 0.1;
```

> Username: HDembinski  
> Created_at: 2022-09-18 08:03:48 UTC  
> Updated_at: 2022-09-18 08:03:49 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r973678662  

This requires a comment why deps has to be so large.

> Username: gohil-jay  
> Created_at: 2022-09-19 06:22:53 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#discussion_r973895352  

I see your improved implementation now covers 0.001. I will take a look on how you did it.


---

## Comment 61

> Username: HDembinski  
> Created_at: 2022-09-18 08:05:59 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1250216598  

Hi Jay, the comments that you added look ok. I am concerned about the test that you changed, and that we do not have an explanation for the large deviations of the jeffreys and clopper pearson intervals from the reference numbers. It is ok to have deviations of about 1e-7, but not 0.1.

---

## Comment 62

> Username: HDembinski  
> Created_at: 2022-09-18 08:26:45 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1250220113  

The error in Slow/gcc10 is not caused by us, but in boostorg/math. I send a PR with a fix to boostorg/math.

---

## Comment 63

> Username: HDembinski  
> Created_at: 2022-09-18 14:03:02 UTC  
> Updated_at: 2022-09-18 14:03:24 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1250315534  

@gohil-jay I fixed the jefferys_interval, you don't need to change this anymore. While reading up on it, I found a minor enhancement for this interval, which I implemented. It is all documented in the class.  
  
While fixing the jeffreys_interval, I noticed why you were getting wrong results. You treated CL as alpha, but alpha = 1 - CL. This is also the reason why your implementation of the clopper_pearson interval gives wrong results. We can and should precompute alpha_half in the constructor of the interval class, because alpha_half is constant during the life-time of the interval maker. This is why we use a class, to be able to put computations that only need to be done once into the constructor. Please have a look how I did it in the jeffreys_interval class, and copy that for the clopper_pearson_interval.

---

## Comment 64

> Username: HDembinski  
> Created_at: 2022-09-18 14:29:02 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1250320606  

I now also fixed the clopper-pearson interval. You don't need to change anything there anymore. Since the beta distribution from boost::math cannot handle the case where the successes or failures are zero, I use an explicit formula for that special case, which is described in the wikipedia.

---

## Comment 65

> Username: gohil-jay  
> Created_at: 2022-09-19 06:24:50 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1250615997  

I will take a look at all the changes including one to boost::math to see how you overcame beta_distribution limit (maybe conditional implementation for those inputs or something I missed from docs) and rest of others.

---

## Comment 66

> Username: HDembinski  
> Created_at: 2022-09-19 08:05:56 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1250697107  

> I will take a look at all the changes including one to boost::math to see how you overcame beta_distribution limit (maybe conditional implementation for those inputs or something I missed from docs) and rest of others.  
  
Yes, I special-cased this. For successes = 0 (or failures = 0), one can compute the interval boundaries analytically as described in the Wikipedia.

---

## Comment 67

> Username: HDembinski  
> Created_at: 2022-09-19 16:48:44 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1251279699  

@henryiii Can you please look over the interface of accumulators/fraction.hpp and let us know if you see any issues? The interface to compute `confidence_interval` is intentionally not flexible, since people should use the wilson interval unless they really know what they are doing. We provide more ways to compute intervals with the interval computers in `boost/histogram/utility`.

---

## Comment 68

> Username: henryiii  
> Created_at: 2022-09-26 13:45:41 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1258067602  

Is there an integration test somewhere (maybe I’m just missing it)? I see unit tests for pieces, but is there a test were a histogram with the new accumulator is tested? I wanted to see the bool sample being used, I’m thinking this is the first storage where a bool sample is expected. That's the most important usage from boost-histogram's perspective.  
  
No docs yet?  
  
To be sure the interface works in boost-histogram, we can always make a branch of boost-histogram building on this branch. But I don’t see anything that would be likely to be a problem.

---

## Comment 69

> Username: HDembinski  
> Created_at: 2022-09-27 15:20:46 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1259664571  

@henryiii Thank you for the comments.  
  
> Is there an integration test somewhere (maybe I’m just missing it)? I see unit tests for pieces, but is there a test were a histogram with the new accumulator is tested? I wanted to see the bool sample being used, I’m thinking this is the first storage where a bool sample is expected. That's the most important usage from boost-histogram's perspective.  
  
Good catch, I think we tested this in the beginning interactively on godbolt, but we don't have this integration test. Needs to be added.  
   
> No docs yet?  
  
Do you mean the user guide? All new classes/functions are documented with doxygen strings. I also checked the generated docs, they look ok. But it is still missing from the guide, that's true.  
  
> To be sure the interface works in boost-histogram, we can always make a branch of boost-histogram building on this branch. But I don’t see anything that would be likely to be a problem.  
  
Yes and until the next boost release, we can also make breaking changes.

---

## Comment 70

> Username: henryiii  
> Created_at: 2022-09-27 15:26:54 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1259672850  

> Do you mean the user guide?  
  
Yes. Not at all a blocker, can be added later, just should be on a todo list somewhere.

---

## Comment 71

> Username: HDembinski  
> Created_at: 2022-09-27 15:45:14 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1259696356  

There is indeed a problem with `std::vector<bool>`. Because of the specialization of vector, one cannot pass `std::vector<bool>` to `histogram::fill` since it is not compatible with std::span (or boost::span). It is possible with any contiguous containers of boolean values that does not use the vector specialization. Supporting `std::vector<bool>` in fill is difficult, I would have to create a special path just for that. What works is to use `std::vector<char>` instead of `std::vector<bool>`.  
  
In boost-histogram, you want to pass a numpy array of bool, which in memory looks like `std::vector<char>`. Conversion to `std::vector<bool>` would actually invoke a costly copy. So supporting `std::vector<bool>` does not make much sense. This should be documented, of course.

---

## Comment 72

> Username: henryiii  
> Created_at: 2022-09-27 16:59:16 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1259793478  

Looks great. Boost-histogram doesn't actually use std::vector (except for strings), so I think we'll be fine there.

---

## Comment 73

> Username: HDembinski  
> Created_at: 2022-09-28 08:42:48 UTC  
> Url: https://github.com/boostorg/histogram/pull/361#issuecomment-1260581879  

Docs were improved, integration test added, and some convenience to allow the interval computers to accept a fraction accumulator directly. I will merge as soon as this passes all tests. Thanks @gohil-jay @henryiii

---
