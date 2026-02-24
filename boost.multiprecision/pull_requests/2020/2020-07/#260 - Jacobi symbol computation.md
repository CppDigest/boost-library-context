# #260 Jacobi symbol computation [Open]

> Username: nemothenoone  
> Created at: 2020-07-19 14:14:07 UTC  
> Updated at: 2021-12-22 18:36:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260  

Resolves #170 and https://github.com/NilFoundation/multiprecision/issues/30. Depends on #161 (not really with the functionality, but it serves the same purpose as modular adaptor does) and #143.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2020-07-19 15:16:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#issuecomment-660661362  

@nemothenoone : The CMakeLists.txt is a nice addition; but does it make the PR lose focus? Should it be split into another PR?

---

## Comment 2

> Username: nemothenoone  
> Created_at: 2020-07-19 15:35:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#issuecomment-660664519  

@NAThompson Sounds reasonable. Well, there are two views on that.  
  
First one is we simply use the CMake-based toolchain for development, which makes the CMake build necessary. And, as much as you can see, everything I bring is connected to each other, so anyway it will come as a whole package for the necessary functionality to be present.  
  
Second one is this particular PR is a Jacobi symbol computation-only and does not depend on anything else we bring. So for this particular case I could cut the CMake out (to get it merged back again later in the `develop` branch).

---

## Comment 3

> Username: nemothenoone  
> Created_at: 2020-07-19 15:39:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#issuecomment-660665115  

@NAThompson And, yes, it is already present as a separate PR here: https://github.com/boostorg/multiprecision/pull/143

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2020-07-21 18:52:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#issuecomment-662043983  

OK, on the one hand this should be relatively easy to merge, on the other hand it has some of the same issues as the modular adaptor PR.  
  
First off the big one: all the CMake code.  As you know I'm sympathetic to having a CMake.txt file in Multiprecision, but that PR should be orthogonal to everything else.  More to the point, I think the CMake PR does too much - Boost may at some point switch to using CMake for testing, but we don't at present, and when we do, Multiprecision should use whatever "official" testing CMake macros are introduced.  
  
On the Jacobi code:    
  
* Couldn't gmp_int use mpz_jacobi ?  
* For personal preference I'd like to see some more tests, the big matrix of values at https://en.wikipedia.org/wiki/Jacobi_symbol would be a good start.  Or use a "known good" (Wolfram Alpha?) to churn out a table of test values.  
* As noted above, like it or not, Boost.Build is still our testing tool: the test will need to be added to the Jamfile (this also helps us check that CI passes OK).  
* I believe Jacobi symbol computation is based on the Euclid GCD algorithm?  Does the Lehmer GCD code help here (recently added via https://github.com/boostorg/multiprecision/pull/250)?  It's way way faster than plain Euclid.  
* We also need documentation - if you really really don't want to mess with the doc quickbook sources, let me have some plain text or rst markup and I'll translate.

---

## Comment 5

> Username: pabristow  
> Created_at: 2020-07-22 08:49:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#issuecomment-662329110  

Or I could draft up some Quickbook Jacobi docs for you from some notes, saving John for more 'interesting' stuff?

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2020-07-22 18:16:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#issuecomment-662608053  

I see a lot of compiler errors inside Jacobi.hpp, individual comments coming up....

---

## Review 7 [Commented]

> Username: jzmaddock  
> Created_at: 2020-07-22 18:17:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/260#pullrequestreview-453569630  

📁 include/boost/multiprecision/jacobi.hpp

```diff
  24 |+    using default_ops::eval_lsb;
  25 |+    using default_ops::eval_lt;
  26 |+    using default_ops::eval_modulus;
```

> Username: jzmaddock  
> Created_at: 2020-07-22 18:17:29 UTC  
> Updated_at: 2020-11-03 07:17:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#discussion_r458991065  

I need eval_divide in this list as well to compile.

> Username: nemothenoone  
> Created_at: 2020-07-22 20:39:31 UTC  
> Updated_at: 2020-11-03 07:17:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#discussion_r459069160  

Agreed. Missed it during review. Fixed.


---

## Review 8 [Commented]

> Username: jzmaddock  
> Created_at: 2020-07-22 18:18:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/260#pullrequestreview-453570612  

📁 include/boost/multiprecision/jacobi.hpp

```diff
  37 |+ 
  38 |+    Backend   x = a, y = n;
  39 |+    limb_type J = 1;
```

> Username: jzmaddock  
> Created_at: 2020-07-22 18:18:57 UTC  
> Updated_at: 2020-11-03 07:17:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#discussion_r458991806  

limb_type is an unsigned type, and is not guaranteed to interoperate with anything except cpp_int anyway, try `typename mpl::front<typename Backend::signed_types>::type`.  (and yes I realise that's a mouthful!)

> Username: nemothenoone  
> Created_at: 2020-07-22 20:41:25 UTC  
> Updated_at: 2020-11-03 07:17:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#discussion_r459070222  

Well, there is no need in storing some data in the actual `limb_type` so I've just replaced it with `int`. Should work fine.  
  
By the way, thank you for the suggestion on backend-independent way of getting the `limb_type`. I know where I'ill use it.


---

## Review 9 [Commented]

> Username: jzmaddock  
> Created_at: 2020-07-22 18:19:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/260#pullrequestreview-453571156  

📁 include/boost/multiprecision/jacobi.hpp

```diff
  44 |+ 
  45 |+       Backend yd2 = y;
  46 |+       eval_divide(yd2, 2);
```

> Username: jzmaddock  
> Created_at: 2020-07-22 18:19:45 UTC  
> Updated_at: 2020-11-03 07:17:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#discussion_r458992265  

I changed this to `eval_right_shift(yd2, 1)` which I think should be more efficient.

> Username: nemothenoone  
> Created_at: 2020-07-22 20:41:50 UTC  
> Updated_at: 2020-11-03 07:17:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#discussion_r459070456  

Agreed. Changed it as well.


---

## Review 10 [Commented]

> Username: jzmaddock  
> Created_at: 2020-07-22 18:21:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/260#pullrequestreview-453572259  

📁 include/boost/multiprecision/jacobi.hpp

```diff
  61 |+       size_t shifts = eval_lsb(x);
  62 |+       eval_right_shift(x, shifts);
  63 |+       if (eval_integer_modulus(shifts, 2))
```

> Username: jzmaddock  
> Created_at: 2020-07-22 18:21:19 UTC  
> Updated_at: 2020-11-03 07:17:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#discussion_r458993214  

This doesn't compile: `shifts` is a native integer type, and eval_integer_modulus is for multiprecision types where the _result_ is known to be a native integer.  I assume this should be `if (shifts % 2)`

> Username: jzmaddock  
> Created_at: 2020-07-22 18:22:11 UTC  
> Updated_at: 2020-11-03 07:17:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#discussion_r458993729  

Or indeed `if (shifts & 1)`, not sure which is more efficient, or maybe it makes no difference?

> Username: nemothenoone  
> Created_at: 2020-07-22 20:42:21 UTC  
> Updated_at: 2020-11-03 07:17:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#discussion_r459070730  

More minor mistakes I've missed during review. Changed that to `if (shifts & 1)`.


---

## Review 11 [Commented]

> Username: jzmaddock  
> Created_at: 2020-07-22 18:23:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/260#pullrequestreview-453573970  

📁 include/boost/multiprecision/jacobi.hpp

```diff
  93 |+     const number<Backend, ExpressionTemplates>& a, const number<Backend, ExpressionTemplates>& n)
  94 |+ {
  95 |+    return number<Backend, ExpressionTemplates>(eval_jacobi(a.backend(), n.backend()));
```

> Username: jzmaddock  
> Created_at: 2020-07-22 18:23:39 UTC  
> Updated_at: 2020-11-03 07:17:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#discussion_r458994561  

This should be `return eval_jacobi(a.backend(), n.backend());`.  The cast to a multiprecision type and back to an `int` is superfluous, and does not compile in any case (not without an explicit cast anyway).

> Username: nemothenoone  
> Created_at: 2020-07-22 20:42:56 UTC  
> Updated_at: 2020-11-03 07:17:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#discussion_r459071063  

Agreed. Missed that one too. Thank you for the review! Fixed it.


---

## Comment 12

> Username: jzmaddock  
> Created_at: 2020-07-22 18:26:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#issuecomment-662612994  

Some performance results:  
  
```  
Run on (8 X 1992 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x4)  
  L1 Instruction 32K (x4)  
  L2 Unified 262K (x4)  
  L3 Unified 8388K (x1)  
------------------------------------------------------------------------------------------  
Benchmark                                                Time             CPU   Iterations  
------------------------------------------------------------------------------------------  
BM_jacobi<boost::multiprecision::cpp_int>/512          125 ms          122 ms           34  
BM_jacobi<boost::multiprecision::cpp_int>/1024         432 ms          390 ms           15  
BM_jacobi<boost::multiprecision::cpp_int>/2048         877 ms          838 ms            5  
BM_jacobi<boost::multiprecision::cpp_int>/4096        1778 ms         1688 ms            2  
BM_jacobi<boost::multiprecision::cpp_int>/8192        5898 ms         5594 ms            1  
BM_jacobi<boost::multiprecision::cpp_int>/16384      16440 ms        15969 ms            1  
BM_jacobi<boost::multiprecision::cpp_int>/32768      55550 ms        54672 ms            1  
BM_jacobi<boost::multiprecision::cpp_int>_BigO       52.44 N^2       51.55 N^2  
BM_jacobi<boost::multiprecision::cpp_int>_RMS           12 %            11 %  
BM_jacobi<boost::multiprecision::mpz_int>/512         9.95 ms         9.93 ms          420  
BM_jacobi<boost::multiprecision::mpz_int>/1024        21.5 ms         21.5 ms          195  
BM_jacobi<boost::multiprecision::mpz_int>/2048        48.9 ms         48.7 ms           84  
BM_jacobi<boost::multiprecision::mpz_int>/4096         121 ms          121 ms           35  
BM_jacobi<boost::multiprecision::mpz_int>/8192         329 ms          328 ms           13  
BM_jacobi<boost::multiprecision::mpz_int>/16384       1005 ms         1004 ms            4  
BM_jacobi<boost::multiprecision::mpz_int>/32768       3407 ms         3406 ms            1  
BM_jacobi<boost::multiprecision::mpz_int>_BigO        3.21 N^2        3.21 N^2  
BM_jacobi<boost::multiprecision::mpz_int>_RMS           11 %            11 %  
```  
  
So the cpp code is at least 15 times slower than GMP.  I suspect GMP is using a Lehmer-GCD-like algorithm here?  
  
Here's the benchmark program:  
  
```  
//  Copyright 2020 John Maddock. Distributed under the Boost  
//  Software License, Version 1.0. (See accompanying file  
//  LICENSE_1_0.txt or copy at https://www.boost.org/LICENSE_1_0.txt  
  
#include <iostream>  
#include <benchmark/benchmark.h>  
#include <boost/multiprecision/cpp_int.hpp>  
#include <boost/multiprecision/gmp.hpp>  
#include <boost/multiprecision/jacobi.hpp>  
#include <boost/random.hpp>  
#include <cmath>  
  
  
template <class T>  
std::tuple<std::vector<T>, std::vector<T>, std::vector<T> >& get_test_vector(unsigned bits)  
{  
   static std::map<unsigned, std::tuple<std::vector<T>, std::vector<T>, std::vector<T> > > data;  
  
   std::tuple<std::vector<T>, std::vector<T>, std::vector<T> >& result = data[bits];  
  
   if (std::get<0>(result).size() == 0)  
   {  
      boost::mt19937                     mt;  
      boost::random::uniform_int_distribution<T> ui(T(1) << (bits - 1), T(1) << bits);  
  
      std::vector<T>& a = std::get<0>(result);  
      std::vector<T>& b = std::get<1>(result);  
      std::vector<T>& c = std::get<2>(result);  
  
      for (unsigned i = 0; i < 1000; ++i)  
      {  
         auto av = ui(mt);  
         a.push_back(av);  
         b.push_back((boost::random::uniform_int_distribution<T>(T(1), av)(mt)) | 1);  
         c.push_back(0);  
      }  
   }  
   return result;  
}  
  
template <class T>  
std::vector<T>& get_test_vector_a(unsigned bits)  
{  
   return std::get<0>(get_test_vector<T>(bits));  
}  
template <class T>  
std::vector<T>& get_test_vector_b(unsigned bits)  
{  
   return std::get<1>(get_test_vector<T>(bits));  
}  
template <class T>  
std::vector<T>& get_test_vector_c(unsigned bits)  
{  
   return std::get<2>(get_test_vector<T>(bits));  
}  
  
  
template <typename T>  
static void BM_jacobi(benchmark::State& state)  
{  
   int                         bits = state.range(0);  
  
   std::vector<T>& a = get_test_vector_a<T>(bits);  
   std::vector<T>& b = get_test_vector_b<T>(bits);  
   std::vector<T>& c = get_test_vector_c<T>(bits);  
  
   for (auto _ : state)  
   {  
      for (unsigned i = 0; i < a.size(); ++i)  
         c[i] = jacobi(a[i], b[i]);  
   }  
   state.SetComplexityN(bits);  
}  
  
  
constexpr unsigned lower_range = 512;  
constexpr unsigned upper_range = 1 << 15;  
  
BENCHMARK_TEMPLATE(BM_jacobi, boost::multiprecision::cpp_int)->RangeMultiplier(2)->Range(lower_range, upper_range)->Unit(benchmark::kMillisecond)->Complexity();  
BENCHMARK_TEMPLATE(BM_jacobi, boost::multiprecision::mpz_int)->RangeMultiplier(2)->Range(lower_range, upper_range)->Unit(benchmark::kMillisecond)->Complexity();  
  
BENCHMARK_MAIN();  
```

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2020-07-22 19:00:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#issuecomment-662629460  

I haven't tried to investigate why, but running tommath through your jacobi code is slower still:  
  
```  
BM_jacobi<boost::multiprecision::cpp_int>/512         44.6 ms         43.5 ms           14  
BM_jacobi<boost::multiprecision::cpp_int>/1024         108 ms          107 ms            7  
BM_jacobi<boost::multiprecision::cpp_int>/2048         260 ms          260 ms            3  
BM_jacobi<boost::multiprecision::cpp_int>/4096         543 ms          547 ms            1  
BM_jacobi<boost::multiprecision::cpp_int>/8192        1680 ms         1641 ms            1  
BM_jacobi<boost::multiprecision::cpp_int>/16384       4859 ms         4781 ms            1  
BM_jacobi<boost::multiprecision::cpp_int>/32768      17869 ms        17688 ms            1  
BM_jacobi<boost::multiprecision::cpp_int>_BigO       16.76 N^2       16.58 N^2  
BM_jacobi<boost::multiprecision::cpp_int>_RMS            8 %             8 %  
BM_jacobi<boost::multiprecision::mpz_int>/512         10.3 ms         10.2 ms           75  
BM_jacobi<boost::multiprecision::mpz_int>/1024        19.0 ms         18.1 ms           45  
BM_jacobi<boost::multiprecision::mpz_int>/2048        47.3 ms         46.9 ms           15  
BM_jacobi<boost::multiprecision::mpz_int>/4096        82.6 ms         78.1 ms            7  
BM_jacobi<boost::multiprecision::mpz_int>/8192         200 ms          198 ms            3  
BM_jacobi<boost::multiprecision::mpz_int>/16384        687 ms          688 ms            1  
BM_jacobi<boost::multiprecision::mpz_int>/32768       1452 ms         1422 ms            1  
BM_jacobi<boost::multiprecision::mpz_int>_BigO     2910.28 NlgN    2860.64 NlgN  
BM_jacobi<boost::multiprecision::mpz_int>_RMS           14 %            14 %  
BM_jacobi<boost::multiprecision::tom_int>/512          508 ms          500 ms            2  
BM_jacobi<boost::multiprecision::tom_int>/1024        1058 ms         1062 ms            1  
BM_jacobi<boost::multiprecision::tom_int>/2048        2065 ms         2031 ms            1  
BM_jacobi<boost::multiprecision::tom_int>/4096        3426 ms         3328 ms            1  
BM_jacobi<boost::multiprecision::tom_int>/8192        5599 ms         5594 ms            1  
BM_jacobi<boost::multiprecision::tom_int>/16384      14822 ms        14766 ms            1  
BM_jacobi<boost::multiprecision::tom_int>/32768      45262 ms        45000 ms            1  
BM_jacobi<boost::multiprecision::tom_int>_BigO       43.11 N^2       42.87 N^2  
BM_jacobi<boost::multiprecision::tom_int>_RMS           20 %            20 %  
```

---

## Comment 14

> Username: nemothenoone  
> Created_at: 2020-07-22 20:38:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#issuecomment-662684240  

@jzmaddock   
  
Okay. I finally got to comments.  
  
Agreed. This particular PR should be only about Jacobi symbol computations. But, this PR is just one more in a sequence of what I'm bringing, so all of the PR are designed to be applied sequentially. We should start with CMake (approve or reject that), then consider modular adaptor, then go to Jacobi symbol etc.  
  
In case CMake-related PR gets rejected, I would refactor this one and will make it independent.  
  
Coming to CMake-related PR. I agree, that testing with CMake is something which is not really required for the library. So I made it optional. The difference between testing and absence of testing is single CMakeLists.txt file. So the configuration can be used for primary target management convenience only. In case you are concerned about who is going to support CMake-based testing - this could be me. Or you can just cut it out the exact moment I'ill fail to do that.  
  
Coming to hypothetical "official" testing macros. Well, that particular PR was being made using the most probable target notation and testing definitions there will be - those ones, which has come from the old Ryppl project. I think @pdimov would agree with me that it was the most successful attempt to CMake-ify Boost. (Actually, Peter, I think there could be some collaboration point about the second attempt to CMake-ify Boost. I believe I got a solution better, lighter and easier to use and support than just introducing CMakeLists.txt to every module and keeping them updated.)  
  
@pabristow Agreed. Documentation should be introduced. I'ill come up with that.

---

## Comment 15

> Username: nemothenoone  
> Created_at: 2020-07-22 20:44:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#issuecomment-662686502  

> I haven't tried to investigate why, but running tommath through your jacobi code is slower still:  
>   
> ```  
> BM_jacobi<boost::multiprecision::cpp_int>/512         44.6 ms         43.5 ms           14  
> BM_jacobi<boost::multiprecision::cpp_int>/1024         108 ms          107 ms            7  
> BM_jacobi<boost::multiprecision::cpp_int>/2048         260 ms          260 ms            3  
> BM_jacobi<boost::multiprecision::cpp_int>/4096         543 ms          547 ms            1  
> BM_jacobi<boost::multiprecision::cpp_int>/8192        1680 ms         1641 ms            1  
> BM_jacobi<boost::multiprecision::cpp_int>/16384       4859 ms         4781 ms            1  
> BM_jacobi<boost::multiprecision::cpp_int>/32768      17869 ms        17688 ms            1  
> BM_jacobi<boost::multiprecision::cpp_int>_BigO       16.76 N^2       16.58 N^2  
> BM_jacobi<boost::multiprecision::cpp_int>_RMS            8 %             8 %  
> BM_jacobi<boost::multiprecision::mpz_int>/512         10.3 ms         10.2 ms           75  
> BM_jacobi<boost::multiprecision::mpz_int>/1024        19.0 ms         18.1 ms           45  
> BM_jacobi<boost::multiprecision::mpz_int>/2048        47.3 ms         46.9 ms           15  
> BM_jacobi<boost::multiprecision::mpz_int>/4096        82.6 ms         78.1 ms            7  
> BM_jacobi<boost::multiprecision::mpz_int>/8192         200 ms          198 ms            3  
> BM_jacobi<boost::multiprecision::mpz_int>/16384        687 ms          688 ms            1  
> BM_jacobi<boost::multiprecision::mpz_int>/32768       1452 ms         1422 ms            1  
> BM_jacobi<boost::multiprecision::mpz_int>_BigO     2910.28 NlgN    2860.64 NlgN  
> BM_jacobi<boost::multiprecision::mpz_int>_RMS           14 %            14 %  
> BM_jacobi<boost::multiprecision::tom_int>/512          508 ms          500 ms            2  
> BM_jacobi<boost::multiprecision::tom_int>/1024        1058 ms         1062 ms            1  
> BM_jacobi<boost::multiprecision::tom_int>/2048        2065 ms         2031 ms            1  
> BM_jacobi<boost::multiprecision::tom_int>/4096        3426 ms         3328 ms            1  
> BM_jacobi<boost::multiprecision::tom_int>/8192        5599 ms         5594 ms            1  
> BM_jacobi<boost::multiprecision::tom_int>/16384      14822 ms        14766 ms            1  
> BM_jacobi<boost::multiprecision::tom_int>/32768      45262 ms        45000 ms            1  
> BM_jacobi<boost::multiprecision::tom_int>_BigO       43.11 N^2       42.87 N^2  
> BM_jacobi<boost::multiprecision::tom_int>_RMS           20 %            20 %  
> ```  
  
Well, I've just introduced tommath-specific Jacobi symbol computation method usage, so the performance should improve.

---

## Comment 16

> Username: nemothenoone  
> Created_at: 2020-07-22 20:47:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#issuecomment-662688250  

> * Couldn't gmp_int use mpz_jacobi ?  
  
Of course. Done.  
  
> * For personal preference I'd like to see some more tests, the big matrix of values at https://en.wikipedia.org/wiki/Jacobi_symbol would be a good start.  Or use a "known good" (Wolfram Alpha?) to churn out a table of test values.  
  
Agreed. The matrix-based testing is coming.  
  
> * As noted above, like it or not, Boost.Build is still our testing tool: the test will need to be added to the Jamfile (this also helps us check that CI passes OK).  
  
My bad. Forgot to do that. Fixed. (And in modular adaptor PR as well).  
  
> * I believe Jacobi symbol computation is based on the Euclid GCD algorithm?  Does the Lehmer GCD code help here (recently added via #250)?  It's way way faster than plain Euclid.  
  
Nah, these are related algorithms, but they are not using each other. So we're stuck with this one.  
  
> * We also need documentation - if you really really don't want to mess with the doc quickbook sources, let me have some plain text or rst markup and I'll translate.  
  
Agreed. The documentation should be here. I'ill do that myself in quickbook.

---

## Comment 17

> Username: jzmaddock  
> Created_at: 2020-07-23 14:48:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#issuecomment-663050884  

>>I believe Jacobi symbol computation is based on the Euclid GCD algorithm? Does the Lehmer GCD code help here (recently added via #250)? It's way way faster than plain Euclid.  
  
>Nah, these are related algorithms, but they are not using each other. So we're stuck with this one.  
  
See https://arxiv.org/abs/1907.07795

---

## Comment 18

> Username: nemothenoone  
> Created_at: 2020-07-26 19:50:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/260#issuecomment-664032438  

> See https://arxiv.org/abs/1907.07795  
  
Oh. That makes things even better. I'ill reimplement the algorithm according to this paper.

---
