# #400 Prime Number Functions [Open]

> Username: mborland  
> Created at: 2020-07-10 04:01:07 UTC  
> Updated at: 2022-10-29 17:13:15 UTC  
> Url: https://github.com/boostorg/math/pull/400  

An initial addition of two prime number functions:  
  
`prime_sieve` is a linear prime sieve algorithm. Currently benchmarks O(n).  
  
`prime_range` returns all the prime numbers in the range [lower_bound, upper_bound]. For the first 1000 primes it can use the already built in lookup tables; outside of that it will call `prime_sieve`. This is the function intended for end users to call.  
  
Future additions would include a Miller-Rabin primality test.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2020-07-10 08:23:19 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-656552509  

FYI there's a Miller Rabin test in Multiprecision already.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2020-07-10 13:05:41 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-656666524  

@mborland : OMG thank you; we've needed a prime sieve for so long.

---

## Review 3 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-10 13:06:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-446384872  

📁 reporting/performance/prime_functions_performance.cpp

```diff
   6 |+ // or copy at http://www.boost.org/LICENSE_1_0.txt)
   7 |+ 
   8 |+ #include "../../include/boost/math/special_functions/prime_functions.hpp"
```

> Username: NAThompson  
> Created_at: 2020-07-10 13:06:34 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r452830886  

I'd use the standard include syntax; it should be on everyone's include path.


---

## Review 4 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-10 13:07:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-446385286  

📁 include/boost/math/special_functions/prime_functions.hpp

```diff
  24 |+ {
  25 |+     static_assert(std::is_integral<Z>::value, "No primes for floating point types");
  26 |+     std::vector<Z> least_divisors(upper_bound + 1, 0);
```

> Username: NAThompson  
> Created_at: 2020-07-10 13:07:15 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r452831201  

Is there a fast way to check if the sieve will overflows the type `Z`?

> Username: mborland  
> Created_at: 2020-07-10 19:00:18 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r453026156  

@NAThompson I added an assertion for overflow to the new commit. It is not 0 cost, but it was the cheapest test I could think of.


---

## Review 5 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-10 13:16:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-446391599  

📁 test/test_prime_functions.cpp

```diff
 108 |+     test_prime_range<int32_t>();
 109 |+     test_prime_range<int64_t>();
 110 |+     test_prime_range<uint32_t>();
```

> Username: NAThompson  
> Created_at: 2020-07-10 13:16:21 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r452836060  

We also need to make sure it works with boost::multiprecision integers; I imagine that we might want to make a huge sieve.


---

## Review 6 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-10 13:25:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-446398644  

📁 reporting/performance/prime_functions_performance.cpp

```diff
  18 |+         std::vector<Z> primes;
  19 |+         benchmark::DoNotOptimize(boost::math::prime_sieve(static_cast<Z>(2), upper, std::back_inserter(primes)));
  20 |+     }
```

> Username: NAThompson  
> Created_at: 2020-07-10 13:25:46 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r452841599  

You forgot the  
  
```  
state.SetComplexityN(state.range(0));  
```  
  
and so the complexity regressions are all wrong.

> Username: mborland  
> Created_at: 2020-07-10 16:57:55 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r452962380  

@NAThompson I was hoping you would find what I had missed. I was getting a stupid result for big O, but the relationship between CPU time and sample size was as expected.


---

## Comment 7

> Username: NAThompson  
> Created_at: 2020-07-10 13:28:28 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-656676930  

@mborland : I would definitely change the name from `prime_functions.hpp` to `prime_sieve.hpp`; the first is not very memorable.

---

## Review 8 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-10 13:29:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-446401218  

📁 include/boost/math/special_functions/prime_functions.hpp

```diff
   9 |+ #define BOOST_MATH_SPECIAL_FUNCTIONS_PRIME_FUNCTIONS_HPP
  10 |+ 
  11 |+ #include <boost/math/special_functions/prime.hpp>
```

> Username: NAThompson  
> Created_at: 2020-07-10 13:29:07 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r452843556  

Why is this header needed?

> Username: mborland  
> Created_at: 2020-07-10 16:59:15 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r452963062  

@NAThompson For small values (<= 104729) `prime_range` does not call the sieve, but uses the lookup table found in that header file.

> Username: NAThompson  
> Created_at: 2020-07-10 17:02:52 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r452964880  

@mborland : In the _Joy of Factoring_, the author states that sieves can be computed faster than primes can be read out of program memory.  
  
In addition, you deprive yourself of a great unit test, namely checking that table.

> Username: NAThompson  
> Created_at: 2020-07-10 17:03:37 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r452965234  

Here's a link: https://bookstore.ams.org/stml-68

> Username: mborland  
> Created_at: 2020-07-10 17:24:45 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r452975294  

@NAThompson That is interesting. I'll have to give it a read.


---

## Review 9 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-10 19:26:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-446650853  

📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
   6 |+ // or copy at http://www.boost.org/LICENSE_1_0.txt)
   7 |+ 
   8 |+ #ifndef BOOST_MATH_SPECIAL_FUNCTIONS_PRIME_FUNCTIONS_HPP
```

> Username: NAThompson  
> Created_at: 2020-07-10 19:26:41 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r453037841  

Make sure to fix the include guard after the name change.


---

## Comment 10

> Username: NAThompson  
> Created_at: 2020-07-11 02:29:44 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-656970582  

@mborland : IIRC, the Joy of Factoring also creates bitsets which set prime at bit i and has it zeroed at composites. Is this a useful API?

---

## Comment 11

> Username: mborland  
> Created_at: 2020-07-11 02:39:37 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-656971892  

@NAThompson That sounds like [wheel factorization](https://en.wikipedia.org/wiki/Wheel_factorization). That method is added to the sieve of Eratosthenes, but would not be applicable here.

---

## Comment 12

> Username: NAThompson  
> Created_at: 2020-07-11 13:50:58 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-657065749  

Would it be more ergonomic to have `prime_range` use a `back_inserter`, but `prime_sieve` behave more like `std::fill`?  
  
```cpp  
std::vector<int64_t> primes(1000); // gimme 1000 primes  
prime_sieve(primes.begin(), primes.end());  
```  
  
(I need to stay in my lane here; @jeremy-murphy is much better at ergonomics.)

---

## Comment 13

> Username: NAThompson  
> Created_at: 2020-07-11 14:17:06 UTC  
> Updated_at: 2020-07-11 14:44:32 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-657069993  

@mborland : I have begun a performance comparison with [Kim Walish's](https://github.com/kimwalisch/primesieve) prime sieve. Here is the code:  
  
```cpp  
#include <vector>  
#include <boost/math/special_functions/prime_sieve.hpp>  
#include <benchmark/benchmark.h>  
#include <primesieve.hpp>  
  
template <class Z>  
void prime_sieve(benchmark::State& state)  
{  
    Z upper = static_cast<Z>(state.range(0));  
    for(auto _ : state)  
    {  
        std::vector<Z> primes;  
        benchmark::DoNotOptimize(boost::math::prime_sieve(static_cast<Z>(2), upper, std::back_inserter(primes)));  
    }  
    state.SetComplexityN(state.range(0));  
}  
BENCHMARK_TEMPLATE(prime_sieve, int64_t)->RangeMultiplier(2)->Range(1 << 1, 1 << 22)->Complexity();  
  
template <class Z>  
void kimwalish_primes(benchmark::State& state)  
{  
  
    Z upper = static_cast<Z>(state.range(0));  
    for (auto _ : state)  
    {  
        std::vector<Z> primes;  
        primesieve::generate_primes(upper, &primes);  
        benchmark::DoNotOptimize(primes.back());  
    }  
    state.SetComplexityN(state.range(0));  
}  
BENCHMARK_TEMPLATE(kimwalish_primes, int64_t)->RangeMultiplier(2)->Range(1 << 1, 1 << 22)->Complexity();  
  
BENCHMARK_MAIN();  
```  
  
and the results:  
  
  
```  
Run on (16 X 2300 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x8)  
  L1 Instruction 32K (x8)  
  L2 Unified 262K (x8)  
  L3 Unified 16777K (x1)  
Load Average: 2.49, 1.81, 1.78  
----------------------------------------------------------------------------  
Benchmark                                  Time             CPU   Iterations  
----------------------------------------------------------------------------  
prime_sieve<int64_t>/2                   348 ns          348 ns      1939182  
prime_sieve<int64_t>/4                   463 ns          462 ns      1467524  
prime_sieve<int64_t>/8                   603 ns          602 ns      1174654  
prime_sieve<int64_t>/16                  717 ns          717 ns       956781  
prime_sieve<int64_t>/32                  907 ns          907 ns       732693  
prime_sieve<int64_t>/64                 1146 ns         1146 ns       604673  
prime_sieve<int64_t>/128                1410 ns         1410 ns       494574  
prime_sieve<int64_t>/256                2190 ns         2190 ns       319042  
prime_sieve<int64_t>/512                3838 ns         3838 ns       186498  
prime_sieve<int64_t>/1024               6769 ns         6769 ns       103041  
prime_sieve<int64_t>/2048              12757 ns        12756 ns        54359  
prime_sieve<int64_t>/4096              25295 ns        25291 ns        26912  
prime_sieve<int64_t>/8192              50898 ns        50880 ns        13661  
prime_sieve<int64_t>/16384            101938 ns       101936 ns         6707  
prime_sieve<int64_t>/32768            201957 ns       201936 ns         3455  
prime_sieve<int64_t>/65536            396735 ns       396709 ns         1754  
prime_sieve<int64_t>/131072           779986 ns       779933 ns          864  
prime_sieve<int64_t>/262144          1534550 ns      1534233 ns          446  
prime_sieve<int64_t>/524288          3020453 ns      3019390 ns          228  
prime_sieve<int64_t>/1048576         6089458 ns      6089187 ns          107  
prime_sieve<int64_t>/2097152        14131817 ns     14130295 ns           44  
prime_sieve<int64_t>/4194304        30953282 ns     30952476 ns           21  
prime_sieve<int64_t>_BigO               0.33 NlgN       0.33 NlgN  
prime_sieve<int64_t>_RMS                   9 %             9 %  
kimwalish_primes<int64_t>/2              276 ns          276 ns      2263102  
kimwalish_primes<int64_t>/4              280 ns          280 ns      2617321  
kimwalish_primes<int64_t>/8              279 ns          279 ns      2461625  
kimwalish_primes<int64_t>/16             289 ns          289 ns      2526273  
kimwalish_primes<int64_t>/32             296 ns          296 ns      2348788  
kimwalish_primes<int64_t>/64             287 ns          287 ns      2397589  
kimwalish_primes<int64_t>/128            357 ns          357 ns      2088954  
kimwalish_primes<int64_t>/256            368 ns          368 ns      1894662  
kimwalish_primes<int64_t>/512            459 ns          459 ns      1601636  
kimwalish_primes<int64_t>/1024          2177 ns         2176 ns       319871  
kimwalish_primes<int64_t>/2048          2535 ns         2534 ns       274488  
kimwalish_primes<int64_t>/4096          3377 ns         3377 ns       206241  
kimwalish_primes<int64_t>/8192          4527 ns         4525 ns       155072  
kimwalish_primes<int64_t>/16384         7015 ns         7014 ns        98151  
kimwalish_primes<int64_t>/32768        10982 ns        10981 ns        63122  
kimwalish_primes<int64_t>/65536        19631 ns        19627 ns        35826  
kimwalish_primes<int64_t>/131072       35359 ns        35356 ns        19769  
kimwalish_primes<int64_t>/262144       66530 ns        66523 ns        10449  
kimwalish_primes<int64_t>/524288      126772 ns       126769 ns         4532  
kimwalish_primes<int64_t>/1048576     246035 ns       245968 ns         2837  
kimwalish_primes<int64_t>/2097152     477212 ns       477177 ns         1450  
kimwalish_primes<int64_t>/4194304    1109480 ns      1109200 ns          621  
kimwalish_primes<int64_t>_BigO          0.01 NlgN       0.01 NlgN  
kimwalish_primes<int64_t>_RMS             11 %            11 %  
```  
  
So it looks like there is 1-2 orders of magnitude of performance improvement left in the boost implementation; presumably we need to find it. . .

---

## Comment 14

> Username: mborland  
> Created_at: 2020-07-11 14:35:36 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-657072908  

@NAThompson It would be fairly easy to create light wrappers to the current `prime_sieve` implementation like in the uni-variate statistics library.   
  
I will have to do some digging to see where more performance can be squeezed out.

---

## Comment 15

> Username: NAThompson  
> Created_at: 2020-07-11 14:46:10 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-657074534  

> It would be fairly easy to create light wrappers to the current prime_sieve implementation like in the uni-variate statistics library.  
  
Nah, if it's not obviously a better way to do it, I'm not interested in it.

---

## Comment 16

> Username: NAThompson  
> Created_at: 2020-07-11 15:51:15 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-657084622  

@mborland : Also, make sure to tag your commit messages with `[CI SKIP]` until we are about to merge.

---

## Review 17 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-11 16:59:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-446815362  

📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
  25 |+ {
  26 |+     static_assert(std::is_integral<Z>::value, "No primes for floating point types");
  27 |+     BOOST_ASSERT_MSG(upper_bound + 1 < std::numeric_limits<Z>::max(), "Type Overflow");
```

> Username: NAThompson  
> Created_at: 2020-07-11 16:59:58 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r453213778  

Sorry, this was a dumb suggestion on my part; I didn't recognize how this was working.


---

## Review 18 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-11 17:00:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-446815413  

📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
  27 |+     BOOST_ASSERT_MSG(upper_bound + 1 < std::numeric_limits<Z>::max(), "Type Overflow");
  28 |+     std::vector<Z> least_divisors(upper_bound + 1, 0);
  29 |+     std::deque<Z> primes;
```

> Username: NAThompson  
> Created_at: 2020-07-11 17:00:44 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r453213870  

You might want to use the prime number theorem to reserve the correct amount of memory: π(x) = x/ln(x).

> Username: mborland  
> Created_at: 2020-07-11 17:10:03 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r453214661  

@NAThompson There is no `std::deque::reserve()` as it does not exist in contiguous memory like a `std::vector`. `std::deque` is faster here for primes than a `std::vector` because insertion time into a `std::deque` is O(1) as opposed to O(N) for a `std::vector`.

> Username: NAThompson  
> Created_at: 2020-07-11 17:28:45 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r453216214  

But you know almost exactly how big the vector will be because of the prime number theorem. Then you get O(1) insertion. See "Non-asymptotic bounds on the prime-counting function" section [here](https://en.wikipedia.org/wiki/Prime_number_theorem).

> Username: mborland  
> Created_at: 2020-07-11 19:26:42 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r453226546  

@NAThompson Is there an implementation of ln somewhere in the code base that supports `boost::multiprecision::cpp_int`? A lot of errors are thrown by `boost::math::tools::promote_args` using `boost::math::log1p()`. Also do you know multiprecision is constructed at fixed size, or is it variable?

> Username: jzmaddock  
> Created_at: 2020-07-12 07:30:10 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r453280280  

You will need to explicitly cast a cpp_int to type double to pass through `log`/`log1p`.  
  
For built-in integer types these are promoted to double automatically (originally for compatibility with C99), but calling a floating point function with a multiprecision integer is always an error: IMO rightly so as this is normally a mistake ;)


---

## Comment 19

> Username: NAThompson  
> Created_at: 2020-07-11 19:37:02 UTC  
> Updated_at: 2020-07-11 20:22:16 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-657117759  

I just went through _The Joy of Factoring_ to refresh my memory on how these sieves work. I implemented algorithm 8.2 of that book, which the author claims runs in O(J*log(log(J)) time. He also references P.A. Pritchard who has given an algorithm which runs in O(J/log(log(J)) time; see _A sublinear additive sieve for finding prime numbers_, Comm. ACM 24, 1981.  
  
In any case, this is how my naive implementation of algorithm 8.2 looks:  
  
```  
// Copyright 2020 Matt Borland  
//  
// Use, modification and distribution are subject to the  
// Boost Software License, Version 1.0.  
// (See accompanying file LICENSE_1_0.txt  
// or copy at http://www.boost.org/LICENSE_1_0.txt)  
  
#include <vector>  
#include <boost/math/special_functions/prime_sieve.hpp>  
#include <benchmark/benchmark.h>  
#include <primesieve.hpp>  
  
template <class Z>  
void prime_sieve(benchmark::State& state)  
{  
    Z upper = static_cast<Z>(state.range(0));  
    for(auto _ : state)  
    {  
        std::vector<Z> primes;  
        benchmark::DoNotOptimize(boost::math::prime_sieve(static_cast<Z>(2), upper, std::back_inserter(primes)));  
    }  
    state.SetComplexityN(state.range(0));  
}  
BENCHMARK_TEMPLATE(prime_sieve, int64_t)->RangeMultiplier(2)->Range(1 << 1, 1 << 22)->Complexity();  
  
template <class Z>  
void kimwalish_primes(benchmark::State& state)  
{  
  
    Z upper = static_cast<Z>(state.range(0));  
    for (auto _ : state)  
    {  
        std::vector<Z> primes;  
        primesieve::generate_primes(upper, &primes);  
        benchmark::DoNotOptimize(primes.back());  
    }  
    state.SetComplexityN(state.range(0));  
}  
BENCHMARK_TEMPLATE(kimwalish_primes, int64_t)->RangeMultiplier(2)->Range(1 << 1, 1 << 22)->Complexity();  
  
  
template<typename Z>  
std::vector<bool> joy_of_factoring_bitset(const Z J)  
{  
    using std::sqrt;  
    using std::log;  
    // A vector of bools behaves like a bitset, but with runtime size:  
    std::vector<bool> P(J, true);  
    P[0] = false;  
    P[1] = false;  
    Z p = 2;  
    Z root_j = sqrt(J);  
    while (p <= root_j) {  
        Z i = p + p;  
        while (i <= J) {  
            P[i] = false;  
            i = i + p;  
        }  
        i = p + 1;  
        while (i <= root_j && P[i] == false) {  
            i += 1;     
        }  
        p = i;  
    }  
    return P;     
}  
  
template<typename Z>  
std::vector<Z> joy_of_factoring_primes(const Z J)  
{  
    auto P = joy_of_factoring_bitset(J);  
    std::vector<Z> primes;  
    if (J >= 355991) {  
        double inv_log = 1/log(J);  
        size_t N = J*inv_log*(1+inv_log + 2.51*inv_log*inv_log);  
        primes.reserve(N);  
    }  
    else if (J>=55) {  
        double n = static_cast<double>(J)/(log(J) - 4.0);  
        primes.reserve(static_cast<size_t>(n));  
    }  
    primes.emplace_back(2);  
    for (size_t i = 3; i < J; i += 2) {  
        if (P[i]) {  
            primes.emplace_back(i);     
        }  
    }  
    return primes;  
}  
  
template <class Z>  
void joy_of_factoring_bm(benchmark::State& state)  
{  
      
    Z upper = static_cast<Z>(state.range(0));  
    for (auto _ : state)  
    {  
        benchmark::DoNotOptimize(joy_of_factoring_primes(upper));  
    }  
    state.SetComplexityN(state.range(0));  
}  
BENCHMARK_TEMPLATE(joy_of_factoring_bm, size_t)->RangeMultiplier(2)->Range(1 << 1, 1 << 22)->Complexity();  
  
BENCHMARK_MAIN();  
```  
  
And the results:  
  
```  
prime_sieve<int64_t>/2                     352 ns          351 ns      1951056  
prime_sieve<int64_t>/4                     470 ns          470 ns      1481710  
prime_sieve<int64_t>/8                     597 ns          597 ns      1129615  
prime_sieve<int64_t>/16                    738 ns          738 ns       953198  
prime_sieve<int64_t>/32                    936 ns          936 ns       762660  
prime_sieve<int64_t>/64                   1145 ns         1145 ns       616909  
prime_sieve<int64_t>/128                  1407 ns         1407 ns       495898  
prime_sieve<int64_t>/256                  2247 ns         2246 ns       314915  
prime_sieve<int64_t>/512                  3715 ns         3713 ns       189649  
prime_sieve<int64_t>/1024                 6664 ns         6663 ns       103458  
prime_sieve<int64_t>/2048                12745 ns        12741 ns        54189  
prime_sieve<int64_t>/4096                25364 ns        25360 ns        27329  
prime_sieve<int64_t>/8192                51157 ns        51151 ns        13472  
prime_sieve<int64_t>/16384              102583 ns       102567 ns         6739  
prime_sieve<int64_t>/32768              203596 ns       203550 ns         3428  
prime_sieve<int64_t>/65536              399037 ns       399001 ns         1753  
prime_sieve<int64_t>/131072             781683 ns       781580 ns          876  
prime_sieve<int64_t>/262144            1538995 ns      1538545 ns          448  
prime_sieve<int64_t>/524288            3095005 ns      3094665 ns          230  
prime_sieve<int64_t>/1048576           6326961 ns      6326208 ns          101  
prime_sieve<int64_t>/2097152          14024588 ns     14024205 ns           44  
prime_sieve<int64_t>/4194304          31189328 ns     31180333 ns           21  
prime_sieve<int64_t>_BigO                 0.33 NlgN       0.33 NlgN  
prime_sieve<int64_t>_RMS                     9 %             9 %  
kimwalish_primes<int64_t>/2                279 ns          279 ns      2509536  
kimwalish_primes<int64_t>/4                289 ns          289 ns      2494788  
kimwalish_primes<int64_t>/8                282 ns          282 ns      2466786  
kimwalish_primes<int64_t>/16               313 ns          313 ns      2333162  
kimwalish_primes<int64_t>/32               293 ns          293 ns      2271474  
kimwalish_primes<int64_t>/64               304 ns          304 ns      2286663  
kimwalish_primes<int64_t>/128              372 ns          372 ns      2002437  
kimwalish_primes<int64_t>/256              383 ns          383 ns      1830046  
kimwalish_primes<int64_t>/512              444 ns          443 ns      1595158  
kimwalish_primes<int64_t>/1024            2178 ns         2177 ns       318499  
kimwalish_primes<int64_t>/2048            2557 ns         2556 ns       271694  
kimwalish_primes<int64_t>/4096            3363 ns         3363 ns       206716  
kimwalish_primes<int64_t>/8192            4591 ns         4591 ns       152819  
kimwalish_primes<int64_t>/16384           6976 ns         6974 ns        97478  
kimwalish_primes<int64_t>/32768          11104 ns        11103 ns        62514  
kimwalish_primes<int64_t>/65536          19537 ns        19534 ns        35619  
kimwalish_primes<int64_t>/131072         35760 ns        35756 ns        19317  
kimwalish_primes<int64_t>/262144         66588 ns        66586 ns        10404  
kimwalish_primes<int64_t>/524288        128041 ns       128034 ns         5385  
kimwalish_primes<int64_t>/1048576       245994 ns       245973 ns         2815  
kimwalish_primes<int64_t>/2097152       481273 ns       481164 ns         1445  
kimwalish_primes<int64_t>/4194304      1110850 ns      1110702 ns          618  
kimwalish_primes<int64_t>_BigO            0.01 NlgN       0.01 NlgN  
kimwalish_primes<int64_t>_RMS               11 %            11 %  
joy_of_factoring_bm<size_t>/2             66.1 ns         66.1 ns     10413103  
joy_of_factoring_bm<size_t>/4              252 ns          252 ns      2807457  
joy_of_factoring_bm<size_t>/8              385 ns          385 ns      1901306  
joy_of_factoring_bm<size_t>/16             508 ns          507 ns      1370265  
joy_of_factoring_bm<size_t>/32             618 ns          618 ns      1111023  
joy_of_factoring_bm<size_t>/64             238 ns          238 ns      2947071  
joy_of_factoring_bm<size_t>/128            334 ns          334 ns      2148722  
joy_of_factoring_bm<size_t>/256            532 ns          532 ns      1259151  
joy_of_factoring_bm<size_t>/512           1027 ns         1027 ns       662553  
joy_of_factoring_bm<size_t>/1024          2490 ns         2490 ns       281698  
joy_of_factoring_bm<size_t>/2048          5211 ns         5211 ns       130022  
joy_of_factoring_bm<size_t>/4096         10454 ns        10454 ns        65775  
joy_of_factoring_bm<size_t>/8192         20807 ns        20805 ns        33289  
joy_of_factoring_bm<size_t>/16384        41352 ns        41351 ns        16687  
joy_of_factoring_bm<size_t>/32768        82184 ns        82180 ns         8354  
joy_of_factoring_bm<size_t>/65536       161913 ns       161879 ns         4242  
joy_of_factoring_bm<size_t>/131072      320842 ns       320755 ns         2170  
joy_of_factoring_bm<size_t>/262144      640485 ns       640433 ns         1073  
joy_of_factoring_bm<size_t>/524288     1289366 ns      1289235 ns          536  
joy_of_factoring_bm<size_t>/1048576    2589004 ns      2588625 ns          269  
joy_of_factoring_bm<size_t>/2097152    5304918 ns      5302623 ns          130  
joy_of_factoring_bm<size_t>/4194304   11621459 ns     11620593 ns           59  
joy_of_factoring_bm<size_t>_BigO          0.12 NlgN       0.12 NlgN  
joy_of_factoring_bm<size_t>_RMS              5 %             5 %  
```  
  
Algorithm 8.2 is actually competitive with Kim Walish for smaller N but starts to lag for larger N.

---

## Comment 20

> Username: mborland  
> Created_at: 2020-07-11 19:49:54 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-657119395  

@NAThompson I am seeing a similar result using a segmented sieve. Currently faster than kimwalish, and `joy_of_factoring_bm` from your post until 524288. After that kimwalish pulls ahead.

---

## Comment 21

> Username: NAThompson  
> Created_at: 2020-07-11 20:20:17 UTC  
> Updated_at: 2020-07-11 20:25:17 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-657123628  

Just ran this benchmark under `perf`:  
  
![kimwalish](https://user-images.githubusercontent.com/5459618/87232923-db4a2d80-c390-11ea-9a54-ba7d85827d88.png)  
  
So Kim Walish's prime generator does indeed compute logarithms and fill up a table of primes; quite a bit of time in `malloc`; quite a bit of time in `fill`.  
  
However if you look at the _Joy of Factoring_ algorithm, it spends way more time in the `fill`:  
  
![joy_of_factoring](https://user-images.githubusercontent.com/5459618/87233079-5bbd5e00-c392-11ea-8623-3e8822a44083.png)  
  
So there are some opportunities there; you'll see that I'm only checking odd numbers after 2, there's probably some way to extend that to making the fill faster.

---

## Comment 22

> Username: mborland  
> Created_at: 2020-07-11 20:50:20 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-657127582  

@NAThompson I have found some performance improvements using C-style arrays instead of `std::vector::reserve()` which makes sense given that data.

---

## Comment 23

> Username: mborland  
> Created_at: 2020-07-13 17:08:53 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-657680882  

@NAThompson This latest iteration's benchmark delivered much better results:  
  
prime_sieve<int64_t>/real_time_BigO               2.87 N          0.22 N      
prime_sieve<int64_t>/real_time_RMS                  11 %            27 %

---

## Comment 24

> Username: NAThompson  
> Created_at: 2020-07-13 18:09:37 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-657710632  

@mborland : Really cool. BTW, if you're going to do threading, write a parallel execution policy; we need the ability to not use threads. [Here's](https://github.com/boostorg/math/pull/248/files) an example I started (but never finished since it was just not enough compute/memory reference).

---

## Comment 25

> Username: mborland  
> Created_at: 2020-07-14 02:33:37 UTC  
> Updated_at: 2020-07-14 02:34:01 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-657930335  

@NAThompson This should fit the bill. Performance ticked down a bit to 3.07N/0.23N because I realized I was relying on the prime table to be statically linked. If you dynamically linked the previous implementation the performance was ~0.2NlgN.

---

## Review 26 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-14 13:45:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-448120244  

📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
 155 |+             small_primes.reserve(1000);
 156 |+ 
 157 |+             // Split into two vectors and merge after joined to avoid data races
```

> Username: NAThompson  
> Created_at: 2020-07-14 13:45:18 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r454367100  

Can you split this into as many vectors as threads?

> Username: mborland  
> Created_at: 2020-07-14 16:44:34 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r454494943  

I can look into that today. I do not see why not.


---

## Comment 27

> Username: NAThompson  
> Created_at: 2020-07-14 13:48:44 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-658190300  

>  Performance ticked down a bit to 3.07N/0.23N because I realized I was relying on the prime table to be statically linked.   
  
I do recommend that the prime table not be used at all; my own preference would be to deprecate this header once your sieve is in.

---

## Comment 28

> Username: mborland  
> Created_at: 2020-07-14 16:41:16 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-658287204  

@NAThompson That makes sense. I can add the notice.

---

## Comment 29

> Username: mborland  
> Created_at: 2020-07-14 16:46:54 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-658290040  

@NAThompson What are your thoughts on defining a `seq_prime_sieve` function instead of wrapping the parallel `prime_sieve` in a block that requires an execution policy. My concern is that execution policies are a C++17 facility, and everything is currently compliant with C++11 except that.

---

## Comment 30

> Username: NAThompson  
> Created_at: 2020-07-14 16:52:37 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-658292945  

@mborland : All the new features I'm adding require C++17; also the `univariate_statistics.hpp` file requires C++-17 and uses `if constexpr` to great effect, IMO.  
  
My view is that gcc, clang and MSVC have been C++-17 compliant for some time now, so if C++-17 provides idioms that makes a function more ergonomic then we should use it. In addition, I thought that your sieve with parallel execution policies was shaping up to be one of the coolest features in all of the library. So I'm biased on that point. . .

---

## Comment 31

> Username: mborland  
> Created_at: 2020-07-14 16:58:00 UTC  
> Updated_at: 2020-07-14 16:58:09 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-658295733  

@NAThompson Works for me. I saw the notice that C++03 is just now being deprecated so I figured I would ask. I believe that GCC 11 is going to have C++17 set as the new default.

---

## Comment 32

> Username: jzmaddock  
> Created_at: 2020-07-14 17:03:57 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-658298657  

>I do recommend that the prime table not be used at all; my own preference would be to deprecate this header once your sieve is in.  
  
Note that the internal use case for that table really does require fast table lookup of primes.

---

## Comment 33

> Username: NAThompson  
> Created_at: 2020-07-14 17:05:54 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-658299621  

> Note that the internal use case for that table really does require fast table lookup of primes.  
  
Ok, no deprecation message, but there is hope that this will be faster than reading out of program memory.

---

## Comment 34

> Username: mborland  
> Created_at: 2020-07-14 18:34:29 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-658343395  

@NAThompson A section to use as many threads as processors available has been added. With the range of benchmark maxed out at 2^30 it bench marks at 6.86 N/0.69N

---

## Comment 35

> Username: mborland  
> Created_at: 2020-07-15 17:58:59 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-658914721  

I added an additional unit test for the multi-threading section and added this set of tests to the Jamfile. Are the 2 thread tests ok to send to CI or should I comment those out as well?

---

## Comment 36

> Username: NAThompson  
> Created_at: 2020-07-15 18:57:56 UTC  
> Updated_at: 2020-07-15 19:14:19 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-658944401  

@mborland : What is the use case for `boost::math::prime_range(100, 1000, std::back_inserter(primes));`? I understand wanting all the primes `< x`, but I've never seen anyone need primes in a range.  
  
> Are the 2 thread tests ok to send to CI or should I comment those out as well?  
  
Yeah send them in. Also, could you run it under `-fsanitize=thread` and make sure it's clean?

---

## Review 37 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-15 19:15:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-449256431  

📁 doc/sf/number_series.qbk

```diff
 293 |+ inclusive.
 294 |+ 
 295 |+ If you have a C++17 compatible compiler you are able to pass an execution policy to both both functions. Any
```

> Username: NAThompson  
> Created_at: 2020-07-15 19:15:04 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r455283054  

It appears that you must have a C++17 compatible compiler.


---

## Review 38 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-15 19:15:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-449256695  

📁 doc/sf/number_series.qbk

```diff
 297 |+ 
 298 |+ For upper_bound <= 2^24 two threads will be used. For any value larger than 2^24 `std::thread::hardware_concurrency()` will be called,
 299 |+ and all available concurrency will be used. Additionally, the memory requirements are `O(sqrt(N)`.
```

> Username: NAThompson  
> Created_at: 2020-07-15 19:15:30 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r455283288  

Typo in the `O(sqrt(N)`.


---

## Comment 39

> Username: mborland  
> Created_at: 2020-07-15 19:22:22 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-658958315  

@NAThompson I can't personally tell you a use case for `prime_range`, but the functionality is available in other libraries like [sympy](https://docs.sympy.org/latest/modules/ntheory.html#sympy.ntheory.generate.primerange).  
  
Locally `-fsanitize=thread` and `fsanitize=address` are both clean.

---

## Review 40 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-15 19:39:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-449272775  

📁 doc/sf/number_series.qbk

```diff
 295 |+ If you have a C++17 compatible compiler you are able to pass an execution policy to both functions. Any
 296 |+ policy besides `std::execution::seq` will enable internal multi-threading. If your compiler is not C++17 compatible the
 297 |+ sequential overloads will be used.
```

> Username: NAThompson  
> Created_at: 2020-07-15 19:39:29 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r455296273  

@mborland : I just checked in C++-11 mode with gcc-10, this does not compile. So the correct statement is "This requires C++17".

> Username: mborland  
> Created_at: 2020-07-15 19:57:08 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r455305860  

@NAThompson I did not realize that `__has_include()` is a macro introduced in C++17. I am editing the include guards to check for language standard instead. That will allow backwards compatibility to C++11.


---

## Review 41 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-15 19:40:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-449273750  

📁 doc/sf/number_series.qbk

```diff
 323 | [endsect] [/section:primes]
 324 | 
 325 | [endsect] [/Number Series]
```

> Username: NAThompson  
> Created_at: 2020-07-15 19:40:54 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r455297045  

This file is not indexed anywhere; it needs to be added to `math.qbk`.

> Username: mborland  
> Created_at: 2020-07-15 20:12:15 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r455313673  

@NAThompson This file is referenced on line 610 of `math.qbk`


---

## Review 42 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-15 20:25:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-449303748  

📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
  17 | #include <thread>
  18 |+ 
  19 |+ #if __cplusplus >= 201703
```

> Username: NAThompson  
> Created_at: 2020-07-15 20:25:47 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r455320916  

I admit to having preferred the `__has_include` since that would protect against compilers with partial C++17 support. Maybe both?

> Username: mborland  
> Created_at: 2020-07-15 20:42:10 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r455329515  

@NAThompson It looks like every compiler that does not support `<execution>` also do not support [`__has_include`](https://en.cppreference.com/w/cpp/compiler_support)

> Username: jzmaddock  
> Created_at: 2020-07-17 12:44:51 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r456418395  

I don't think that's still _quite_ right: the outlier is MSVC which still does not define __cpluplus to a sensible value!  
  
ordinarily the right way to do this is:  
  
```  
#ifdef __has_include  
#if __has_include(<execution>)  
#include <execution>  
#define BOOST_MATH_USE_STD_EXECUTION_POLICIES // if required?  
#endif  
#endif  
```  
  
But.... sadly that still fails if the compilation mode is not C++17 and <execution> happens to be in the include path :(  So I think we need to add a Boost.Config macro (feel free to nag me about that!).

> Username: mborland  
> Created_at: 2020-07-17 18:17:20 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r456601414  

@jzmaddock What about:  
  
```  
#ifdef _MSVC_LANG  
#if _MSVC_LANG >= 201703 // _MSVC_LANG == __cplusplus: https://devblogs.microsoft.com/cppblog/msvc-now-correctly-reports-__cplusplus/  
#include <execution>  
#endif  
#else  
#if __cplusplus >= 201703  
#include <execution>  
#endif  
#endif  
```  
That compiles fine with `-std=c++11` and should be portable


---

## Comment 43

> Username: NAThompson  
> Created_at: 2020-07-15 20:55:57 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-659005147  

@mborland : Just tried to build the docs; got the following error:  
  
```  
sf/number_series.qbk:325: error: Mismatched [endsect] near column 9.  
```

---

## Review 44 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-15 20:57:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-449324974  

📁 doc/sf/number_series.qbk

```diff
 312 |+ 
 313 |+ 
 314 |+ /Nota bene:/ For values larger than 2^24 the returned primes will not be sorted
```

> Username: NAThompson  
> Created_at: 2020-07-15 20:57:50 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r455337816  

I don't understand; why not sort them? If I understand correctly, this is just a merging of sorted lists, which is O(N) time.  
  
Or: Why not leave all of them unsorted?

> Username: mborland  
> Created_at: 2020-07-15 22:19:13 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r455397691  

Bad logic merging the lists during parallel execution. It is fixed now.


---

## Comment 45

> Username: NAThompson  
> Created_at: 2020-07-15 22:58:18 UTC  
> Updated_at: 2020-07-15 23:08:42 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-659056950  

@mborland : Very cool; here's the comparison to Kim Walish's sieve:  
  
```cpp  
#include <vector>  
#include <boost/math/special_functions/prime_sieve.hpp>  
#include <benchmark/benchmark.h>  
#include <primesieve.hpp>  
  
template <class Z>  
void prime_sieve(benchmark::State& state)  
{  
    Z upper = static_cast<Z>(state.range(0));  
    for(auto _ : state)  
    {  
        std::vector<Z> primes;  
        benchmark::DoNotOptimize(boost::math::prime_sieve(std::execution::par, upper, std::back_inserter(primes)));  
    }  
    state.SetComplexityN(state.range(0));  
}  
BENCHMARK_TEMPLATE(prime_sieve, int64_t)->RangeMultiplier(2)->Range(1 << 1, 1 << 22)->Complexity(benchmark::oN)->UseRealTime();  
  
template <class Z>  
void kimwalish_primes(benchmark::State& state)  
{  
  
    Z upper = static_cast<Z>(state.range(0));  
    for (auto _ : state)  
    {  
        std::vector<Z> primes;  
        primesieve::generate_primes(upper, &primes);  
        benchmark::DoNotOptimize(primes.back());  
    }  
    state.SetComplexityN(state.range(0));  
}  
BENCHMARK_TEMPLATE(kimwalish_primes, int64_t)->RangeMultiplier(2)->Range(1 << 1, 1 << 22)->Complexity(benchmark::oN)->UseRealTime();  
  
BENCHMARK_MAIN();  
```  
  
Here I force it to regress against O(N) scaling since that makes comparison easier:  
  
  
```  
prime_sieve<int64_t>/2/real_time                   417 ns          416 ns      1688428  
prime_sieve<int64_t>/4/real_time                   590 ns          589 ns      1132537  
prime_sieve<int64_t>/8/real_time                   932 ns          930 ns       730881  
prime_sieve<int64_t>/16/real_time                 1235 ns         1229 ns       573691  
prime_sieve<int64_t>/32/real_time                 1477 ns         1473 ns       465590  
prime_sieve<int64_t>/64/real_time                 1672 ns         1667 ns       412482  
prime_sieve<int64_t>/128/real_time                2017 ns         2009 ns       356051  
prime_sieve<int64_t>/256/real_time                2795 ns         2786 ns       249987  
prime_sieve<int64_t>/512/real_time                4226 ns         4209 ns       166142  
prime_sieve<int64_t>/1024/real_time               6851 ns         6835 ns        99070  
prime_sieve<int64_t>/2048/real_time              13647 ns        13622 ns        49657  
prime_sieve<int64_t>/4096/real_time              24109 ns        24061 ns        28328  
prime_sieve<int64_t>/8192/real_time              88496 ns        38358 ns         7544  
prime_sieve<int64_t>/16384/real_time            104841 ns        40361 ns         5822  
prime_sieve<int64_t>/32768/real_time            181437 ns        65559 ns         4329  
prime_sieve<int64_t>/65536/real_time            281237 ns        68548 ns         2480  
prime_sieve<int64_t>/131072/real_time           572515 ns       101001 ns         1238  
prime_sieve<int64_t>/262144/real_time          1155111 ns       154189 ns          614  
prime_sieve<int64_t>/524288/real_time          2800881 ns       294266 ns          278  
prime_sieve<int64_t>/1048576/real_time         5264969 ns       445906 ns          127  
prime_sieve<int64_t>/2097152/real_time        14900995 ns      1180893 ns           56  
prime_sieve<int64_t>/4194304/real_time        45743068 ns      2255611 ns           18  
prime_sieve<int64_t>/real_time_BigO               9.83 N          0.54 N  
prime_sieve<int64_t>/real_time_RMS                  61 %            16 %  
kimwalish_primes<int64_t>/2/real_time              580 ns          465 ns      1461148  
kimwalish_primes<int64_t>/4/real_time              507 ns          473 ns      1430308  
kimwalish_primes<int64_t>/8/real_time              691 ns          550 ns      1165038  
kimwalish_primes<int64_t>/16/real_time             681 ns          523 ns       935754  
kimwalish_primes<int64_t>/32/real_time             481 ns          454 ns      1098076  
kimwalish_primes<int64_t>/64/real_time             506 ns          490 ns      1000000  
kimwalish_primes<int64_t>/128/real_time            585 ns          571 ns      1169549  
kimwalish_primes<int64_t>/256/real_time            625 ns          613 ns      1066912  
kimwalish_primes<int64_t>/512/real_time            753 ns          730 ns       895759  
kimwalish_primes<int64_t>/1024/real_time          3281 ns         3228 ns       169253  
kimwalish_primes<int64_t>/2048/real_time          3736 ns         3728 ns       184918  
kimwalish_primes<int64_t>/4096/real_time          5086 ns         5074 ns       130160  
kimwalish_primes<int64_t>/8192/real_time          7088 ns         7050 ns        97634  
kimwalish_primes<int64_t>/16384/real_time        10945 ns        10921 ns        62913  
kimwalish_primes<int64_t>/32768/real_time        17168 ns        17057 ns        40847  
kimwalish_primes<int64_t>/65536/real_time        30259 ns        30192 ns        22623  
kimwalish_primes<int64_t>/131072/real_time       55462 ns        55328 ns        12102  
kimwalish_primes<int64_t>/262144/real_time      103074 ns       102894 ns         6650  
kimwalish_primes<int64_t>/524288/real_time      195996 ns       195575 ns         3428  
kimwalish_primes<int64_t>/1048576/real_time     380300 ns       379350 ns         1849  
kimwalish_primes<int64_t>/2097152/real_time     863277 ns       861173 ns          776  
kimwalish_primes<int64_t>/4194304/real_time    1719699 ns      1714099 ns          394  
kimwalish_primes<int64_t>/real_time_BigO          0.41 N          0.41 N  
kimwalish_primes<int64_t>/real_time_RMS              7 %             7 %  
```

---

## Comment 46

> Username: mborland  
> Created_at: 2020-07-15 23:17:42 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-659063289  

@NAThompson Do you mind running the range to `Range(1 << 1, 1 << 30)`? The multi-threaded section does not kick in until 16777216. On my 4-core machine I get BigO 7.48N / 0.72N

---

## Comment 47

> Username: NAThompson  
> Created_at: 2020-07-15 23:26:51 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-659066322  

Here's the data with the upper limits to `1 << 30`:  
  
```  
prime_sieve<int64_t>/2/real_time                      472 ns          451 ns      1414302  
prime_sieve<int64_t>/4/real_time                      661 ns          645 ns       994604  
prime_sieve<int64_t>/8/real_time                     1172 ns         1091 ns       642846  
prime_sieve<int64_t>/16/real_time                    2281 ns         1649 ns       510896  
prime_sieve<int64_t>/32/real_time                    1858 ns         1685 ns       326717  
prime_sieve<int64_t>/64/real_time                    2227 ns         1939 ns       344951  
prime_sieve<int64_t>/128/real_time                   3905 ns         2546 ns       263574  
prime_sieve<int64_t>/256/real_time                   5837 ns         3517 ns       100000  
prime_sieve<int64_t>/512/real_time                  10012 ns         5441 ns       118820  
prime_sieve<int64_t>/1024/real_time                 32633 ns        10591 ns        28876  
prime_sieve<int64_t>/2048/real_time                 87184 ns        21127 ns        10000  
prime_sieve<int64_t>/4096/real_time                 57855 ns        32931 ns        10000  
prime_sieve<int64_t>/8192/real_time                203505 ns        68658 ns         3676  
prime_sieve<int64_t>/16384/real_time               357408 ns        85046 ns         3132  
prime_sieve<int64_t>/32768/real_time               568251 ns       118150 ns         1943  
prime_sieve<int64_t>/65536/real_time               647119 ns       125206 ns          850  
prime_sieve<int64_t>/131072/real_time              873327 ns       158861 ns          839  
prime_sieve<int64_t>/262144/real_time             1896190 ns       233048 ns          289  
prime_sieve<int64_t>/524288/real_time             3440163 ns       365258 ns          155  
prime_sieve<int64_t>/1048576/real_time            5934125 ns       512692 ns          120  
prime_sieve<int64_t>/2097152/real_time           17128238 ns      1357882 ns           51  
prime_sieve<int64_t>/4194304/real_time           40289611 ns      2101125 ns           16  
prime_sieve<int64_t>/8388608/real_time          176450968 ns      5048200 ns            5  
prime_sieve<int64_t>/16777216/real_time         277882934 ns     10597000 ns            2  
prime_sieve<int64_t>/33554432/real_time         546636820 ns     29321000 ns            1  
prime_sieve<int64_t>/67108864/real_time        1228493929 ns     81935000 ns            1  
prime_sieve<int64_t>/134217728/real_time       2693028927 ns    176454000 ns            1  
prime_sieve<int64_t>/268435456/real_time       6167316914 ns    485259000 ns            1  
prime_sieve<int64_t>/536870912/real_time       10034755945 ns    651477000 ns            1  
prime_sieve<int64_t>/1073741824/real_time      21161373138 ns   1304266000 ns            1  
prime_sieve<int64_t>/real_time_BigO                 19.67 N          1.24 N  
prime_sieve<int64_t>/real_time_RMS                     14 %            31 %  
kimwalish_primes<int64_t>/2/real_time                 472 ns          459 ns      1377873  
kimwalish_primes<int64_t>/4/real_time                 472 ns          455 ns      1326232  
kimwalish_primes<int64_t>/8/real_time                 599 ns          459 ns      1258681  
kimwalish_primes<int64_t>/16/real_time                472 ns          464 ns      1068379  
kimwalish_primes<int64_t>/32/real_time                545 ns          505 ns      1361598  
kimwalish_primes<int64_t>/64/real_time                559 ns          531 ns      1256548  
kimwalish_primes<int64_t>/128/real_time               612 ns          603 ns      1080831  
kimwalish_primes<int64_t>/256/real_time               656 ns          622 ns      1104761  
kimwalish_primes<int64_t>/512/real_time               826 ns          761 ns       889666  
kimwalish_primes<int64_t>/1024/real_time             7313 ns         7243 ns        94173  
kimwalish_primes<int64_t>/2048/real_time             8217 ns         8080 ns        86309  
kimwalish_primes<int64_t>/4096/real_time             9420 ns         9350 ns        57991  
kimwalish_primes<int64_t>/8192/real_time            14043 ns        12633 ns        60442  
kimwalish_primes<int64_t>/16384/real_time           15789 ns        15559 ns        43194  
kimwalish_primes<int64_t>/32768/real_time           24174 ns        23648 ns        28812  
kimwalish_primes<int64_t>/65536/real_time           37681 ns        36867 ns        19021  
kimwalish_primes<int64_t>/131072/real_time          69875 ns        69478 ns         9372  
kimwalish_primes<int64_t>/262144/real_time         144312 ns       135258 ns         5513  
kimwalish_primes<int64_t>/524288/real_time         199171 ns       197979 ns         3401  
kimwalish_primes<int64_t>/1048576/real_time        406033 ns       391570 ns         1841  
kimwalish_primes<int64_t>/2097152/real_time        840364 ns       814348 ns          897  
kimwalish_primes<int64_t>/4194304/real_time       1931641 ns      1765441 ns          354  
kimwalish_primes<int64_t>/8388608/real_time       4507502 ns      4285706 ns          163  
kimwalish_primes<int64_t>/16777216/real_time      7541693 ns      7258225 ns           71  
kimwalish_primes<int64_t>/33554432/real_time     16549234 ns     15633788 ns           52  
kimwalish_primes<int64_t>/67108864/real_time     28684931 ns     27891000 ns           25  
kimwalish_primes<int64_t>/134217728/real_time    59007388 ns     55270462 ns           13  
kimwalish_primes<int64_t>/268435456/real_time   104790449 ns    103567333 ns            6  
kimwalish_primes<int64_t>/536870912/real_time   288390040 ns    287275000 ns            2  
kimwalish_primes<int64_t>/1073741824/real_time  593466043 ns    590670000 ns            1  
kimwalish_primes<int64_t>/real_time_BigO             0.54 N          0.54 N  
kimwalish_primes<int64_t>/real_time_RMS                22 %            23 %  
```

---

## Comment 48

> Username: mborland  
> Created_at: 2020-07-15 23:37:51 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-659069710  

@NAThompson Thanks. They have pretty impressive performance.

---

## Comment 49

> Username: NAThompson  
> Created_at: 2020-07-15 23:40:06 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-659070330  

@mborland : Have you tried any of the sublinear sieves like the [Sieve of Atkin](https://en.wikipedia.org/wiki/Sieve_of_Atkin) or any of Paul Pritchard's works?

---

## Comment 50

> Username: mborland  
> Created_at: 2020-07-15 23:51:17 UTC  
> Updated_at: 2020-07-15 23:51:33 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-659073483  

@NAThompson I have not seen Paul Pritchard's works yet, but I will take a look tonight. The Sieve of Atkin has a memory complexity of `O(N)` just like the current iteration of `linear_sieve`. The limiting factor is the performance of the segmented sieve (`mask_sieve`) which is slower than `linear_sieve` but has memory complexity of `O(sqrt(N))`.

---

## Comment 51

> Username: pabristow  
> Created_at: 2020-07-16 10:46:36 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-659331360  

You might like to use the Unicode symbol for which \math\doc\html4_symbols.qbk contains a template used thus [bigo]  
  
[template bigo[]'''&#x1D476;'''] [/  O blackletter capital O = big O symbol]

---

## Review 52 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-16 13:43:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-449860806  

📁 doc/sf/number_series.qbk

```diff
 297 |+ sequential overloads will be used.
 298 |+ 
 299 |+ For upper_bound <= 2^24 two threads will be used. For any value larger than 2^24 `std::thread::hardware_concurrency()` will be called,
```

> Username: NAThompson  
> Created_at: 2020-07-16 13:43:56 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r455796107  

You can use:  
  
2[super 24]  
  
to typeset these nicely.


---

## Review 53 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-16 13:44:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-449861662  

📁 doc/sf/number_series.qbk

```diff
 291 |+ There are two sets of functions available `prime_sieve` and `prime_range`. `prime_sieve` will return all primes in the
 292 |+ range 2 to `upper_bound` inclusive. `prime_range` will return all the primes in the range `lower_bound` to `upper_bound`
 293 |+ inclusive.
```

> Username: NAThompson  
> Created_at: 2020-07-16 13:44:52 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r455796741  

Generally we don't do inclusive in C++; what is the motivation to include `upper_bound`?

> Username: mborland  
> Created_at: 2020-07-16 14:09:05 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r455814986  

It was a design choice. Wolfram-alpha uses the range `[lower_bound, upper_bound]` while sympy uses `[lower_bound, upper_bound)`. It made sense to me to include the upper bound, but it should rarely make a difference.

> Username: jzmaddock  
> Created_at: 2020-07-17 12:37:30 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r456414573  

I think for consistency with the C++ std lib, I would go exclusive and use `[lower, upper)` as the bounds.


---

## Comment 54

> Username: mborland  
> Created_at: 2020-07-16 18:23:27 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-659588758  

@NAThompson After doing some reading I realized each call to `mask_sieve` was too big of a range to fit into cache. Now each thread takes 1/#threads of the total range, but then splits its range and makes several calls to `mask_sieve` that all fit into L1. Still not as fast as kimwalish but within a factor of 2 complexity in real time now.  
  
```  
------------------------------------------------------------------------------------------------  
Benchmark                                                      Time             CPU   Iterations  
------------------------------------------------------------------------------------------------  
prime_sieve<int64_t>/2/real_time                            58.8 ns         58.7 ns     11352262  
prime_sieve<int64_t>/4/real_time                            80.8 ns         80.7 ns      8713214  
prime_sieve<int64_t>/8/real_time                             127 ns          127 ns      5509311  
prime_sieve<int64_t>/16/real_time                            171 ns          171 ns      4098726  
prime_sieve<int64_t>/32/real_time                            248 ns          247 ns      2831628  
prime_sieve<int64_t>/64/real_time                            381 ns          380 ns      1844908  
prime_sieve<int64_t>/128/real_time                           646 ns          635 ns      1106924  
prime_sieve<int64_t>/256/real_time                          1251 ns         1248 ns       550957  
prime_sieve<int64_t>/512/real_time                          2540 ns         2537 ns       275430  
prime_sieve<int64_t>/1024/real_time                         5009 ns         4937 ns       141552  
prime_sieve<int64_t>/2048/real_time                        10122 ns        10107 ns        68760  
prime_sieve<int64_t>/4096/real_time                        21346 ns        20864 ns        32147  
prime_sieve<int64_t>/8192/real_time                        65443 ns        17662 ns        13685  
prime_sieve<int64_t>/16384/real_time                       79790 ns        18859 ns         9340  
prime_sieve<int64_t>/32768/real_time                      118341 ns        24716 ns         4430  
prime_sieve<int64_t>/65536/real_time                      227553 ns        63118 ns         2754  
prime_sieve<int64_t>/131072/real_time                     414106 ns       100359 ns         1425  
prime_sieve<int64_t>/262144/real_time                     809448 ns        97256 ns          887  
prime_sieve<int64_t>/524288/real_time                    1625920 ns       141167 ns          459  
prime_sieve<int64_t>/1048576/real_time                   3265761 ns       237121 ns          225  
prime_sieve<int64_t>/2097152/real_time                   6375365 ns       437109 ns          109  
prime_sieve<int64_t>/4194304/real_time                  13469974 ns       842544 ns           47  
prime_sieve<int64_t>/8388608/real_time                  42985232 ns      4424598 ns           16  
prime_sieve<int64_t>/16777216/real_time                113844645 ns      9156952 ns            6  
prime_sieve<int64_t>/33554432/real_time                 96517744 ns     21085706 ns            6  
prime_sieve<int64_t>/67108864/real_time                225407292 ns     33046887 ns            3  
prime_sieve<int64_t>/134217728/real_time               582350327 ns     92024480 ns            1  
prime_sieve<int64_t>/268435456/real_time              1156112449 ns    186584498 ns            1  
prime_sieve<int64_t>/536870912/real_time              2708158733 ns    376115084 ns            1  
prime_sieve<int64_t>/1073741824/real_time             6240828469 ns    748866767 ns            1  
prime_sieve<int64_t>/real_time_BigO                         5.57 N          0.70 N      
prime_sieve<int64_t>/real_time_RMS                            28 %             5 %      
  
kimwalish_primes<int64_t>/2/real_time                        222 ns          220 ns      3168063  
kimwalish_primes<int64_t>/4/real_time                        223 ns          221 ns      3170683  
kimwalish_primes<int64_t>/8/real_time                        226 ns          223 ns      3116809  
kimwalish_primes<int64_t>/16/real_time                       241 ns          236 ns      2951329  
kimwalish_primes<int64_t>/32/real_time                       248 ns          242 ns      2864052  
kimwalish_primes<int64_t>/64/real_time                       252 ns          251 ns      2621189  
kimwalish_primes<int64_t>/128/real_time                      266 ns          264 ns      2635081  
kimwalish_primes<int64_t>/256/real_time                      299 ns          298 ns      2335814  
kimwalish_primes<int64_t>/512/real_time                      379 ns          376 ns      1856834  
kimwalish_primes<int64_t>/1024/real_time                    1227 ns         1220 ns       568762  
kimwalish_primes<int64_t>/2048/real_time                    1653 ns         1644 ns       422254  
kimwalish_primes<int64_t>/4096/real_time                    2587 ns         2573 ns       270537  
kimwalish_primes<int64_t>/8192/real_time                    4063 ns         4040 ns       172389  
kimwalish_primes<int64_t>/16384/real_time                   6945 ns         6865 ns       101223  
kimwalish_primes<int64_t>/32768/real_time                  11772 ns        11689 ns        55680  
kimwalish_primes<int64_t>/65536/real_time                  21573 ns        21377 ns        32472  
kimwalish_primes<int64_t>/131072/real_time                 40186 ns        40010 ns        17176  
kimwalish_primes<int64_t>/262144/real_time                 76579 ns        75969 ns         9232  
kimwalish_primes<int64_t>/524288/real_time                147765 ns       146281 ns         4801  
kimwalish_primes<int64_t>/1048576/real_time               290699 ns       283196 ns         2476  
kimwalish_primes<int64_t>/2097152/real_time               567532 ns       555400 ns         1244  
kimwalish_primes<int64_t>/4194304/real_time              1085404 ns      1078746 ns          643  
kimwalish_primes<int64_t>/8388608/real_time              2148548 ns      2126485 ns          326  
kimwalish_primes<int64_t>/16777216/real_time             4259006 ns      4217367 ns          164  
kimwalish_primes<int64_t>/33554432/real_time             8416400 ns      8367273 ns           82  
kimwalish_primes<int64_t>/67108864/real_time            16975271 ns     16911195 ns           41  
kimwalish_primes<int64_t>/134217728/real_time           52566743 ns     52227093 ns           13  
kimwalish_primes<int64_t>/268435456/real_time          104504271 ns    103881750 ns            7  
kimwalish_primes<int64_t>/536870912/real_time          207417639 ns    206010421 ns            3  
kimwalish_primes<int64_t>/1073741824/real_time         414685617 ns    412502569 ns            2  
kimwalish_primes<int64_t>/real_time_BigO                    0.39 N          0.38 N      
kimwalish_primes<int64_t>/real_time_RMS                        7 %             7 %  
```

---

## Comment 55

> Username: mborland  
> Created_at: 2020-07-16 23:04:07 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-659723353  

Performance of most recent commit:  
  
```  
Run on (4 X 4300 MHz CPU s)  
CPU Caches:  
  L1 Data 32 KiB (x4)  
  L1 Instruction 32 KiB (x4)  
  L2 Unified 256 KiB (x4)  
  L3 Unified 6144 KiB (x1)  
Load Average: 3.44, 2.93, 2.75  
------------------------------------------------------------------------------------------------  
Benchmark                                                      Time             CPU   Iterations  
------------------------------------------------------------------------------------------------  
prime_sieve<int64_t>/2/real_time                            58.7 ns         58.4 ns     11898734  
prime_sieve<int64_t>/4/real_time                            80.6 ns         80.3 ns      8893825  
prime_sieve<int64_t>/8/real_time                             125 ns          125 ns      5708166  
prime_sieve<int64_t>/16/real_time                            163 ns          163 ns      4344503  
prime_sieve<int64_t>/32/real_time                            227 ns          226 ns      3019601  
prime_sieve<int64_t>/64/real_time                            332 ns          331 ns      2075233  
prime_sieve<int64_t>/128/real_time                           519 ns          517 ns      1344431  
prime_sieve<int64_t>/256/real_time                           982 ns          978 ns       708609  
prime_sieve<int64_t>/512/real_time                          2071 ns         2062 ns       338176  
prime_sieve<int64_t>/1024/real_time                         3913 ns         3895 ns       179006  
prime_sieve<int64_t>/2048/real_time                         7852 ns         7818 ns        89024  
prime_sieve<int64_t>/4096/real_time                        16242 ns        16169 ns        43363  
prime_sieve<int64_t>/8192/real_time                        50301 ns        18207 ns        14020  
prime_sieve<int64_t>/16384/real_time                       72244 ns        20050 ns         9008  
prime_sieve<int64_t>/32768/real_time                      121688 ns        30506 ns         6019  
prime_sieve<int64_t>/65536/real_time                      230695 ns        69722 ns         3064  
prime_sieve<int64_t>/131072/real_time                     407221 ns       105684 ns         1601  
prime_sieve<int64_t>/262144/real_time                     728795 ns       118175 ns          946  
prime_sieve<int64_t>/524288/real_time                    1412216 ns       175315 ns          490  
prime_sieve<int64_t>/1048576/real_time                   2805698 ns       263641 ns          249  
prime_sieve<int64_t>/2097152/real_time                   5760929 ns       449037 ns          120  
prime_sieve<int64_t>/4194304/real_time                  12234691 ns       883192 ns           57  
prime_sieve<int64_t>/8388608/real_time                  30650701 ns      4724122 ns           22  
prime_sieve<int64_t>/16777216/real_time                 67283165 ns      9756846 ns           10  
prime_sieve<int64_t>/33554432/real_time                 69353385 ns     22969767 ns           10  
prime_sieve<int64_t>/67108864/real_time                140249359 ns     34373742 ns            4  
prime_sieve<int64_t>/134217728/real_time               376052033 ns     96507400 ns            2  
prime_sieve<int64_t>/268435456/real_time               872877137 ns    195534536 ns            1  
prime_sieve<int64_t>/536870912/real_time              2128263723 ns    396076257 ns            1  
prime_sieve<int64_t>/1073741824/real_time             4816053283 ns    799589397 ns            1  
prime_sieve<int64_t>/real_time_BigO                         4.30 N          0.74 N      
prime_sieve<int64_t>/real_time_RMS                            30 %             6 %     
```

---

## Comment 56

> Username: jzmaddock  
> Created_at: 2020-07-17 12:48:31 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-660088124  

Hi Matt, I think this is showing a lot of promise, however the API I would use most often - well actually the only one I have use case for - is the equivalent of Kim Walisch's `primesieve::iterator`.  Can we add something equivalent and compare performance?   I think that would allow us to understand where we're at with this, as well as having a more useful interface.

---

## Review 57 [Commented]

> Username: jzmaddock  
> Created_at: 2020-07-17 17:58:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-450854146  

📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
  86 |+     Z limit {static_cast<Z>(std::floor(std::sqrt(static_cast<double>(upper_bound)))) + 1};
  87 |+ 
  88 |+     size_t primes_size;
```

> Username: jzmaddock  
> Created_at: 2020-07-17 17:58:14 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r456592287  

MSVC refuses to compile this as prime_sizes is used uninitialized.


---

## Comment 58

> Username: jzmaddock  
> Created_at: 2020-07-17 18:04:30 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-660260321  

prime_sieve_performance.cpp crashes inside std::vector for me, has/can someone run it through a sanitizer?

---

## Comment 59

> Username: mborland  
> Created_at: 2020-07-17 18:11:01 UTC  
> Updated_at: 2020-07-17 18:12:23 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-660263395  

@jzmaddock I will have to look at their code and see what the output looks like. This currently sends an output iterator so it shouldn't be terribly different.  
  
I have run this through both -fsanitize=address, and -fsanitize=thread before committing. What is it saying the exception is?  
The compiler I use is: gcc version 10.1.1 20200507 (Red Hat 10.1.1-1) (GCC)

---

## Review 60 [Commented]

> Username: jzmaddock  
> Created_at: 2020-07-17 18:39:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-450878712  

📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
  88 |+     size_t primes_size;
  89 |+     auto it{primes.begin()};
  90 |+     while(*it < limit && it != primes.end())
```

> Username: jzmaddock  
> Created_at: 2020-07-17 18:39:14 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r456611983  

This dereferences one past the end: swapping the checks over so we check for it != primes.end() first fixes that.


---

## Comment 61

> Username: jzmaddock  
> Created_at: 2020-07-17 18:40:26 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-660277030  

> What is it saying the exception is?  
  
It's accessing invalid memory - while inserting a zero-sized vector into another if that rings any bells.

---

## Comment 62

> Username: jzmaddock  
> Created_at: 2020-07-17 18:48:24 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-660280611  

Call stack for the crash:  
  
```  
 	test.exe!std::vector<__int64,std::allocator<__int64>>::_Orphan_range(__int64 * _First, __int64 * _Last) Line 1716	C++  
 	test.exe!std::vector<__int64,std::allocator<__int64>>::_Emplace_back_with_unused_capacity<__int64 &>(__int64 & <_Val_0>) Line 689	C++  
 	test.exe!std::vector<__int64,std::allocator<__int64>>::emplace_back<__int64 &>(__int64 & <_Val_0>) Line 705	C++  
>	test.exe!boost::math::detail::mask_sieve<__int64,std::vector<__int64,std::allocator<__int64>>,std::vector<__int64,std::allocator<__int64>>>(__int64 lower_bound, __int64 upper_bound, const std::vector<__int64,std::allocator<__int64>> & primes, std::vector<__int64,std::allocator<__int64>> & c) Line 126	C++  
 	test.exe!boost::math::detail::segmented_sieve<__int64,std::vector<__int64,std::allocator<__int64>>,std::vector<__int64,std::allocator<__int64>>>(__int64 lower_bound, __int64 upper_bound, const std::vector<__int64,std::allocator<__int64>> & primes, std::vector<__int64,std::allocator<__int64>> & c) Line 161	C++  
 	test.exe!boost::math::prime_sieve::__l26::<lambda>() Line 285	C++  
```  
  
Looks like something is either uninitialized or has been destructed, but I don't see what :(

---

## Review 63 [Commented]

> Username: jzmaddock  
> Created_at: 2020-07-17 18:53:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-450886802  

📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
  29 |+ {
  30 |+     size_t least_divisors_size{static_cast<size_t>(upper_bound + 1)};
  31 |+     Z *least_divisors{new Z[least_divisors_size]{0}};
```

> Username: jzmaddock  
> Created_at: 2020-07-17 18:53:24 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r456618508  

This should be a `std::unique_ptr<Z[]>`, raw new/delete should be (almost) banned ;)  
  
There's another "naked" call to new later on that should also change IMO.

> Username: mborland  
> Created_at: 2020-07-17 19:12:06 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r456627109  

@jzmaddock I can move that one into the 21st century. The other one calls `memset()` which is not compatible with smart pointers. Also not particularly good C++ style.

> Username: jzmaddock  
> Created_at: 2020-07-17 20:33:49 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r456661441  

Just call unique_ptr::get() to obtain the raw pointer and pass to memset.


---

## Comment 64

> Username: mborland  
> Created_at: 2020-07-17 19:25:20 UTC  
> Updated_at: 2020-07-17 19:25:31 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-660296816  

@jzmaddock The vectors are now all initialized to empty. That looks to me like the issue from your trace. Passes the sanitizer for both address and thread.

---

## Review 65 [Commented]

> Username: jzmaddock  
> Created_at: 2020-07-17 19:28:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-450906429  

📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
 275 |+             for(size_t i{1}; i < processor_count - 1; ++i)
 276 |+             {
 277 |+                 std::vector<Z> temp {};
```

> Username: jzmaddock  
> Created_at: 2020-07-17 19:28:05 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r456633774  

I don't think this is doing what you think it is, and in any case is the cause of the segfaults I'm seeing, as uninitialized vectors are being passed to semented_sieve.  
  
First you need   
  
```  
std::vector<std::vector<Z>> prime_vectors(processor_count);  
```  
  
above (count them!), then in the loop you should remove `temp` and just call:  
  
```  
prime_vectors[i - 1].reserve(primes_in_range);  
```  
  
No emplace_back required.  
  
Then update the index used in the lambda to i - 1 as well.  Or else just start from zero!!  
  
Then the final N'th thread created outside the loop needs the same fix.


---

## Comment 66

> Username: jzmaddock  
> Created_at: 2020-07-17 20:02:59 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-660312748  

>The vectors are now all initialized to empty. That looks to me like the issue from your trace. Passes the sanitizer for both address and thread.  
  
No still crashes, same issue, see my comments above.

---

## Comment 67

> Username: mborland  
> Created_at: 2020-07-18 02:15:03 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-660408280  

@jzmaddock Looking at the implementation of `primesieve::iterator` I would recommend that be submitted as separate file and PR. It would be a template class that uses `prime_sieve` as implemented here with some additional functionality added. This weekend I am going to see if I can squeeze some more performance out here before building the iterator class.

---

## Comment 68

> Username: mborland  
> Created_at: 2020-07-18 22:18:59 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-660550541  

`std::async` has been implemented into `segmented_sieve` with some success. New benchmarks for `Range(1 << 1, 1 << 30)`  are:  
  
```  
prime_sieve<int64_t>/real_time_BigO             1.74 N          0.66 N      
prime_sieve<int64_t>/real_time_RMS                 6 %             5 %      
```

---

## Comment 69

> Username: jeremy-murphy  
> Created_at: 2020-07-20 23:44:18 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-661450151  

@NAThompson , sorry, I didn't notice the mention until now when I was going to comment on this PR anyway.  :)  
There is an efficient implementation of the linear prime sieve in _From Mathematics to Generic Programming_, which is worth looking at even though the code is expository. I'll have to come back later to check whether it is actually suitable here.

---

## Comment 70

> Username: mborland  
> Created_at: 2020-07-21 00:22:53 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-661482056  

@jeremy-murphy I will have to take a look. The hotspot in the profile comes from the segmented sieve. I am working on replacing my current implementation of `mask_sieve` with [Pritchard's Linear Segmented Wheel Sieve](https://minds.wisconsin.edu/bitstream/handle/1793/59248/TR909.pdf?sequence=1)(p11 - Heading 8). It should be faster than `mask_sieve` if all of the preprocessing is done up front, and the wheels are then passed to the sieving threads.

---

## Review 71 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-07-21 06:00:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-452147862  

📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
  32 |+ // https://mathworld.wolfram.com/SieveofEratosthenes.html
  33 |+ // https://www.cs.utexas.edu/users/misra/scannedPdf.dir/linearSieve.pdf
  34 |+ template<class Z, class Container>
```

> Username: jeremy-murphy  
> Created_at: 2020-07-21 06:00:50 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r457855584  

I personally wouldn't use Z, since it is the name of an infinite _set_ of numbers; it doesn't really fit into a taxonomy of _concepts_. (And no computer integer type is infinite.) It fits into the taxonomy of _numbers_, in which there are various supersets of it such as R and Z[_i_], but only integer-like numbers (such as Z[_i_] but not R) will work here. Concepts can specify these properties, which is why it is important to use them as opposed to names of numbers.  
  
So, the appropriate concept here is probably _DiscreteArchimedeanRing_, but no-one knows what that means unless they have memorized _Elements of Programming_. So everyone just writes _Integer_.  :)   
Now you might say, but that's what Z means! It's a subtle distinction, sure. "Z" is a set, but "Integer" is a concept.  
  
And don't even get me started on R.

> Username: mborland  
> Created_at: 2020-07-27 03:35:07 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r460629336  

With C++20 concepts would you use `template<Integer Z, class Container>`? I can see for now replacing Z with Integer making sense.

> Username: jeremy-murphy  
> Created_at: 2020-07-27 04:15:11 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r460637863  

I haven't spent much time thinking about how I would name template parameters using the concepts syntax in C++20, sorry.  :)


---

## Review 72 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-07-21 06:52:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-452172426  

📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
  33 |+ // https://www.cs.utexas.edu/users/misra/scannedPdf.dir/linearSieve.pdf
  34 |+ template<class Z, class Container>
  35 |+ void linear_sieve(Z upper_bound, Container &c)
```

> Username: jeremy-murphy  
> Created_at: 2020-07-21 06:52:56 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r457875538  

Please use something more descriptive than `c` here. :) Surely the values in this container have some meaning, especially since it's the prime numbers result, right?


---

## Review 73 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-07-21 07:02:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-452177835  

📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
  36 |+ {
  37 |+     size_t least_divisors_size{static_cast<size_t>(upper_bound + 1)};
  38 |+     std::unique_ptr<Z[]> least_divisors{new Z[least_divisors_size]{0}};
```

> Username: jeremy-murphy  
> Created_at: 2020-07-21 07:02:33 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r457879763  

Can't this just be `vector<Z>`?

> Username: mborland  
> Created_at: 2020-07-27 03:31:41 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r460628688  

It could be, but using a C-style array instead of an `std::vector` yielded lower memory usage and complexity.

> Username: jeremy-murphy  
> Created_at: 2020-07-27 04:25:52 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r460639941  

Hmmm, that doesn't sound right, though, does it? Like, memory usage could be marginally less with the raw array, but complexity should be identical. What kind of difference are we talking here?

> Username: mborland  
> Created_at: 2020-07-27 04:34:39 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r460641705  

It is in micro-optimization territory. I just re-ran it and the difference is 3.72N with the C-style array vs 3.86N for the vector.

> Username: jeremy-murphy  
> Created_at: 2020-07-27 04:42:07 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r460643258  

Are those values consistent between different runs of the benchmark?

> Username: jeremy-murphy  
> Created_at: 2020-07-27 04:49:12 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r460644794  

Although I would generally argue not to leave any performance improvement on the table, I feel like `std::vector` _shouldn't_ be any worse here. At the risk of sounding demanding... have you tried another standard library implementation?

> Username: mborland  
> Created_at: 2020-07-27 04:49:41 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r460644948  

They are; benchmark's variation is usually pretty small. These were about +-0.03N.

> Username: mborland  
> Created_at: 2020-07-27 05:00:12 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r460647433  

Compiling with Intel C++ and their implementation of the standard library yields the same complexity for the c-style array and the `std::vector`. I guess I would err on the side of more users compiling with gcc/clang and libstdc++ than Intel C++.


---

## Review 74 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-07-21 07:19:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-452188012  

📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
 382 |+ 
 383 |+ template<class Z, class OutputIterator>
 384 |+ auto prime_range(Z lower_bound, Z upper_bound, OutputIterator output) -> decltype(output)
```

> Username: jeremy-murphy  
> Created_at: 2020-07-21 07:19:21 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r457887678  

I'm not sure it's worth including this functionality if it's not computationally more efficient.  
After all, this will do the same amount of work, but also allocate extra temporary memory just to omit the primes lower than the lower_bound.  The user can remove the primes less than the lower_bound without using extra temporary memory.

> Username: mborland  
> Created_at: 2020-07-27 03:44:24 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r460631287  

My thought process was it would make life easier in that use case. If the user were to remove the primes less than `lower_bound` themselves the memory usage should be the same as moving them the ones they wanted out of the full range [2, upper_bound) they would have received anyway.

> Username: jeremy-murphy  
> Created_at: 2020-07-27 05:14:46 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r460650740  

To be precise, the _peak_ memory allocation will be worse this way. Let's say,   
`x = upper_bound / std::log(static_cast<double>(upper_bound))`, and  
`y = (upper_bound - lower_bound) / std::log(static_cast<double>(upper_bound - lower_bound))`  
Then using this function, the user allocates size `y` for the final result, and this function allocates `x` for the temporary result of `prime_range(upper)`. So the peak memory usage is `x + y` (ignoring other temporary allocations).  
  
Without this function, the user allocates size `x` for the result, then calls `prime_range(upper)` directly, so the peak memory usage is `x` (again, ignoring other temporary allocations). The user then executes something like `move(begin(result) + lower_bound, end(result), begin(result)); result.resize(upper_bound - lower_bound); result.shrink_to_fit();` to remove the unwanted values and return unused capacity to the system. It's messier and more work for the user, but that's the cost of having lower peak memory overhead.  
  
Is it worth it? Who knows. On reflection, there is no problem with leaving this convenience wrapper in -- users concerned with peak memory allocation more than convenience will just do the second method.


---

## Review 75 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-07-27 04:31:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-455464964  

📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
  87 |+     const size_t n {static_cast<size_t>(upper_bound - lower_bound + 1)};
  88 |+     std::unique_ptr<bool[]> is_prime {new bool[n]};
  89 |+     memset(is_prime.get(), true, sizeof(*is_prime.get()) * (n));
```

> Username: jeremy-murphy  
> Created_at: 2020-07-27 04:31:38 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r460641065  

Using `vector` would simplify this code, too. These three lines would become:  
  
`std::vector<bool> is_prime(upper_bound - lower_bound + 1, true);`

> Username: jeremy-murphy  
> Created_at: 2020-07-27 04:33:43 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r460641509  

It would also get the benefits and quirks of `std::vector<bool>`.

> Username: mborland  
> Created_at: 2020-07-27 04:43:13 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r460643504  

I remember that Herb Sutter used to recommend against using `std::vector<bool>` because it was standard non-conforming. I found this current implementation to have slightly lower memory usage and complexity than his recommended `std::vector<char>`. The difference was again in micro-optimization territory.

> Username: jeremy-murphy  
> Created_at: 2020-07-27 04:53:11 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r460645744  

Yeah, right, fair point.


---

## Comment 76

> Username: jeremy-murphy  
> Created_at: 2020-07-27 05:40:56 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-664131094  

When I was thinking about that peak allocation business with `prime_range`, it occurred to me that these algorithms could be, and perhaps _should be_, designed to use memory that the user has already allocated, instead of doing the `new` and `move` combination used throughout. Because otherwise, it's effectively always allocating a temporary buffer of size _n_, and then moving all the data out to the user's buffer of size _n_ at the end. Why do we need the temporary buffer?  
Give the user a simple function with which to calculate the size of the output,   
```  
std::size_t prime_output_size(std::size_t n)  
{  
    return n / std::log(n);  
}  
```  
Now the user can allocate exactly the right sized buffer and pass it to the prime sieve functions. This also generally means that instead of taking a `Container` as a parameter, you take an `Iterator`, and it's simply a requirement of the algorithm that that Iterator refers to a block of memory of size whatever. (This also fits with the generic programming mantra that algorithms should not know anything about containers.)  
  
This won't remove all the temporary buffer allocations, but it will remove some.

---

## Review 77 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-07-27 05:47:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-455485786  

📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
 140 |+     SetS() = default;
 141 |+ 
 142 |+     constexpr Z next(const Z x) noexcept
```

> Username: jeremy-murphy  
> Created_at: 2020-07-27 05:47:42 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r460659311  

Don't forget to mark all the `const` functions as such.


---

## Comment 78

> Username: mborland  
> Created_at: 2020-07-27 14:40:02 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-664437599  

@NAThompson If you have a chance can you review `sub_linear_wheel_sieve`? It is an implementation of Pritchard's sub-linear sieve algorithm. The document is in the comment and all the variable names are as shown in that document. Right now it is benchmarking in the `O(NlogN)` range; way worse than expected.

---

## Comment 79

> Username: NAThompson  
> Created_at: 2020-07-27 14:50:47 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-664443605  

@mborland : Is it faster than the previous implementation? Also, could you run the benchmark under `perf record` and screenshot the hot assembly instructions and post them in the comments?  
  
Note that sometimes the memory hierarchy will make the complexity "empirically" `O(NlogN)` so as long as we're convinced that indeed the implementation is faster and doing the correct thing we should be fine. (Though we could constrain the data length for force it into RAM rather than cache . . . )

---

## Comment 80

> Username: mborland  
> Created_at: 2020-07-27 15:03:14 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-664450919  

@NAThompson Its not close. The current `linear_sieve` benchmarks at ~4N while `sub_linear_wheel_sieve` came in at ~5.5 NlogN. I have not profiled it yet because I assumed something was fundamentally wrong with my implementation. I can post the screenshot soon.

---

## Comment 81

> Username: mborland  
> Created_at: 2020-07-27 15:48:13 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-664476743  

![Profile](https://user-images.githubusercontent.com/3745830/88561951-b2af6e00-cff5-11ea-9844-b46bc859a960.png)  
  
@NAThompson the perf report as requested. Looks like removing elements from S is dominating performance. Remove is implemented as a binary search tree so it should be on average O(logN).

---

## Comment 82

> Username: NAThompson  
> Created_at: 2020-07-27 17:36:18 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-664536564  

> Remove is implemented as a binary search tree so it should be on average O(logN).  
  
So you have to call it O(N) times?

---

## Comment 83

> Username: mborland  
> Created_at: 2020-07-27 17:52:02 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-664545288  

@NAThompson S contains O(n / log(log(n)) elements, and remove is only called on non-prime elements of S.

---

## Comment 84

> Username: jeremy-murphy  
> Created_at: 2020-07-30 23:50:13 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-666782623  

Another general comment, since that `prime_sieve.hpp` file is getting kinda large, I would think about moving the core algorithms out into their own specific files, and then include those files into `prime_sieve.hpp`. I personally like how Boost.Geometry organize their algorithms.  
  
I also think the core algorithms deserve to be in the `boost::math` namespace, not in `detail`. Each one of them is a useful thing in its own right that some other algorithm might want to use. I would limit use of the `detail` namespace to things that could change name or meaning or complexity, etc; things that are in a state of flux. John Lakos also champions this approach, it's not just me. :)

---

## Review 85 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-07-31 00:12:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-458842832  

📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
 136 |+ {
 137 |+ private:
 138 |+     std::deque<Z> srec_;
```

> Username: jeremy-murphy  
> Created_at: 2020-07-31 00:12:01 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r463338416  

Have you benchmarked the benefit of using `std::deque` as opposed to `std::vector`?

> Username: mborland  
> Created_at: 2020-07-31 00:58:12 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r463350706  

I had not because the performance was so far off of what I expected. There is a lot of searching so it would not surprise me if the `std::vector` is faster.

> Username: jeremy-murphy  
> Created_at: 2020-07-31 01:59:55 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r463366098  

This guy did a great benchmark comparison years ago: https://baptiste-wicht.com/posts/2012/12/cpp-benchmark-vector-list-deque.html  
  
OK, wow, that was 8 years ago already.


---

## Comment 86

> Username: mborland  
> Created_at: 2020-07-31 01:03:04 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-666851682  

@jeremy-murphy Separating this file out makes sense. Would you include algorithms that are not the best if only for academic interest? Almost every paper on the subject begins discussion with classical algorithms like the sieve of eratosthenes. By the end of this I will have a fair number of non-optimal, but properly functioning algorithms.

---

## Comment 87

> Username: jeremy-murphy  
> Created_at: 2020-07-31 01:56:35 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-666868838  

> @jeremy-murphy Separating this file out makes sense. Would you include algorithms that are not the best if only for academic interest? Almost every paper on the subject begins discussion with classical algorithms like the sieve of eratosthenes. By the end of this I will have a fair number of non-optimal, but properly functioning algorithms.  
  
I would only include algorithms that are the best in their class, so no I wouldn't include toy or didactic algorithms unless they happen to be the best in some class.  
There are several classes to be the best in: time complexity, space complexity and 'fastest in practice' to name the most important ones that come to mind. Most users just want the fastest in practice, but some users have different requirements.   
  
So I think it's good to have a single point of entry that uses heuristics to choose the fastest algorithm in practice, but also good to let users choose a specific algorithm directly, and we should document where each one has an advantage over the others.

---

## Comment 88

> Username: mborland  
> Created_at: 2020-08-02 16:37:02 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-667696272  

Latest results:  
  
```  
-----------------------------------------------------------------------------------------  
Benchmark                                               Time             CPU   Iterations  
-----------------------------------------------------------------------------------------  
prime_sieve<int64_t>/2/real_time                     4.70 ns         4.70 ns    150505734  
prime_sieve<int64_t>/4/real_time                     19.9 ns         19.9 ns     35616087  
prime_sieve<int64_t>/8/real_time                     52.3 ns         52.2 ns     13216004  
prime_sieve<int64_t>/16/real_time                    70.1 ns         70.0 ns      9752453  
prime_sieve<int64_t>/32/real_time                    89.4 ns         89.3 ns      7722033  
prime_sieve<int64_t>/64/real_time                     116 ns          116 ns      6071210  
prime_sieve<int64_t>/128/real_time                    140 ns          140 ns      5022604  
prime_sieve<int64_t>/256/real_time                    187 ns          186 ns      3781960  
prime_sieve<int64_t>/512/real_time                    260 ns          259 ns      2722944  
prime_sieve<int64_t>/1024/real_time                   415 ns          414 ns      1709004  
prime_sieve<int64_t>/2048/real_time                   650 ns          650 ns      1073247  
prime_sieve<int64_t>/4096/real_time                  1038 ns         1037 ns       657852  
prime_sieve<int64_t>/8192/real_time                  1759 ns         1757 ns       389150  
prime_sieve<int64_t>/16384/real_time                 2863 ns         2860 ns       238403  
prime_sieve<int64_t>/32768/real_time                 5306 ns         5299 ns       129443  
prime_sieve<int64_t>/65536/real_time                30835 ns        20174 ns        23374  
prime_sieve<int64_t>/131072/real_time               90451 ns        21917 ns         8095  
prime_sieve<int64_t>/262144/real_time              530987 ns        70433 ns         1231  
prime_sieve<int64_t>/524288/real_time              550681 ns        40743 ns         1437  
prime_sieve<int64_t>/1048576/real_time            1774152 ns       168988 ns          473  
prime_sieve<int64_t>/2097152/real_time            3219870 ns       239832 ns          254  
prime_sieve<int64_t>/4194304/real_time            5977406 ns       414089 ns          121  
prime_sieve<int64_t>/8388608/real_time           10260546 ns       580496 ns           71  
prime_sieve<int64_t>/16777216/real_time          20672092 ns      1264057 ns           37  
prime_sieve<int64_t>/33554432/real_time          29944878 ns      2778050 ns           26  
prime_sieve<int64_t>/67108864/real_time          55096380 ns      5125197 ns           11  
prime_sieve<int64_t>/134217728/real_time        133807037 ns     28087185 ns            4  
prime_sieve<int64_t>/268435456/real_time        319550700 ns     86388827 ns            2  
prime_sieve<int64_t>/536870912/real_time        700423532 ns    170615353 ns            1  
prime_sieve<int64_t>/1073741824/real_time      1402043045 ns    323787231 ns            1  
prime_sieve<int64_t>/real_time_BigO                  1.29 N          0.30 N      
prime_sieve<int64_t>/real_time_RMS                     12 %            21 %     
kimwalish_primes<int64_t>/2/real_time                 211 ns          211 ns      3237379  
kimwalish_primes<int64_t>/4/real_time                 214 ns          214 ns      3191696  
kimwalish_primes<int64_t>/8/real_time                 217 ns          217 ns      3187980  
kimwalish_primes<int64_t>/16/real_time                237 ns          237 ns      2920574  
kimwalish_primes<int64_t>/32/real_time                245 ns          244 ns      2833667  
kimwalish_primes<int64_t>/64/real_time                254 ns          254 ns      2743253  
kimwalish_primes<int64_t>/128/real_time               273 ns          273 ns      2547653  
kimwalish_primes<int64_t>/256/real_time               318 ns          318 ns      2182346  
kimwalish_primes<int64_t>/512/real_time               377 ns          377 ns      1810732  
kimwalish_primes<int64_t>/1024/real_time             1200 ns         1199 ns       570857  
kimwalish_primes<int64_t>/2048/real_time             1625 ns         1623 ns       437359  
kimwalish_primes<int64_t>/4096/real_time             2530 ns         2527 ns       279461  
kimwalish_primes<int64_t>/8192/real_time             3995 ns         3990 ns       176256  
kimwalish_primes<int64_t>/16384/real_time            6715 ns         6707 ns       104606  
kimwalish_primes<int64_t>/32768/real_time           11474 ns        11459 ns        61386  
kimwalish_primes<int64_t>/65536/real_time           20877 ns        20848 ns        33848  
kimwalish_primes<int64_t>/131072/real_time          39268 ns        39218 ns        18128  
kimwalish_primes<int64_t>/262144/real_time          73916 ns        73805 ns         9407  
kimwalish_primes<int64_t>/524288/real_time         140761 ns       140575 ns         4955  
kimwalish_primes<int64_t>/1048576/real_time        272210 ns       271818 ns         2534  
kimwalish_primes<int64_t>/2097152/real_time        531798 ns       531105 ns         1281  
kimwalish_primes<int64_t>/4194304/real_time       1048795 ns      1047131 ns          669  
kimwalish_primes<int64_t>/8388608/real_time       2065337 ns      2062246 ns          343  
kimwalish_primes<int64_t>/16777216/real_time      4068960 ns      4063572 ns          172  
kimwalish_primes<int64_t>/33554432/real_time      8162407 ns      8149591 ns           86  
kimwalish_primes<int64_t>/67108864/real_time     16460520 ns     16434682 ns           43  
kimwalish_primes<int64_t>/134217728/real_time    50807118 ns     50672355 ns           14  
kimwalish_primes<int64_t>/268435456/real_time   100858553 ns    100671317 ns            7  
kimwalish_primes<int64_t>/536870912/real_time   199505521 ns    199193528 ns            3  
kimwalish_primes<int64_t>/1073741824/real_time  396751053 ns    396140903 ns            2  
kimwalish_primes<int64_t>/real_time_BigO             0.37 N          0.37 N      
kimwalish_primes<int64_t>/real_time_RMS                 7 %             7 %     
```  
  
My algorithms are clearly not as efficient but aggressive threading now gives me better CPU complexity.

---

## Comment 89

> Username: NAThompson  
> Created_at: 2020-08-02 16:55:15 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-667698311  

@mborland : Woot woot!

---

## Comment 90

> Username: NAThompson  
> Created_at: 2020-08-02 21:39:50 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-667728036  

@mborland : Do you think you can beat the Kim Walish prime sieve single-threaded and then make this the best implementation known by threading?

---

## Comment 91

> Username: mborland  
> Created_at: 2020-08-02 22:37:01 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-667733561  

@NAThompson That is a good question. I know that `mask_sieve` is my limiting factor (0.2 NlogN) but I have yet to find a different segmented sieve algorithm that can be implemented with better complexity.

---

## Comment 92

> Username: NAThompson  
> Created_at: 2020-08-04 16:22:57 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-668694021  

@jzmaddock : Should this go in `boost.integer` when it's finished?

---

## Comment 93

> Username: jzmaddock  
> Created_at: 2020-08-04 17:13:37 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-668719528  

>Should this go in boost.integer when it's finished?  
  
Honestly I don't know what's best: Boost.Integer isn't really actively maintained :(   But from a logical perspective I can see it would be easiest to find there.

---

## Comment 94

> Username: jeremy-murphy  
> Created_at: 2020-08-07 07:13:22 UTC  
> Updated_at: 2020-08-07 07:14:04 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-670368026  

@jzmaddock   
I personally don't agree with putting math algorithms into Boost.Integer just because they have specializations for computer `int` types. I think it's a maintenance problem waiting to happen.   
Does it mean that we'll move floating-point specializations into Boost.Float? (If it existed.) And if we did, that would mean three separate pull requests on three different libraries to make an API change or bug fix that affects all implementations?  
Math algorithms that work on `int` also work on integer-like numbers, such as Gaussian integers and polynomials over a field (_F_[_x_]).  
I think Boost.Integer is best defined with a very limited scope of operations unique to computer `int` types, as opposed to being open to all math algorithms that apply to integer-like numbers.  
Just my two cents, which as you know I am always happy to give.  ;)

---

## Review 95 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-08-10 00:23:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-463917962  

📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
 394 |+ {
 395 |+     prime_sieve(std::execution::par, upper_bound, primes);
 396 |+ }
```

> Username: jeremy-murphy  
> Created_at: 2020-08-10 00:23:59 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r467647857  

Somewhat sadly you might say, but the standard algorithms default to a sequenced policy of execution.  
I personally would err on the side of consistency and use the same default, but otherwise, at least Boost.Math should have a consistent default within the library. @jzmaddock ?


---

## Review 96 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-08-10 00:34:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-463919025  

📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
  61 |+                 least_divisors[i * resultant_primes[j]] = resultant_primes[j];
  62 |+             }
  63 |+         }
```

> Username: jeremy-murphy  
> Created_at: 2020-08-10 00:34:47 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r467649040  

Just a code simplification here, but those three conditions that result in a `break`, they could all go in the second clause of the `for` loop? I mean, `j < least_divisors && j < resultant_primes.size() && ... etc`.  
Another way to simplify this with more abstraction would be to make a zip iterator over the two data structures.


---

## Review 97 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-08-10 00:52:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-463920803  

📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
 243 |+     {
 244 |+         primes.reserve(upper_bound / std::log(static_cast<double>(upper_bound)));
 245 |+     }
```

> Username: jeremy-murphy  
> Created_at: 2020-08-10 00:52:21 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r467651047  

This really violates the philosophy of generic programming. These algorithms should not know about the container that the result is going into. The output should be an output iterator.  
Otherwise we will end up with algorithms that are not generic and someone will have to fix them later.  
It's also just making your work harder and the algorithm slower by managing the client's memory.  
I'm not sure who I should appeal to here, but please, this is not generic programming.


---

## Comment 98

> Username: mborland  
> Created_at: 2020-08-23 23:55:05 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-678842057  

Some preliminary results of the new sieve for large numbers:  
  
```  
prime_sieve<int64_t>/134217728/real_time        127063438 ns     18793762 ns            7  
prime_sieve<int64_t>/268435456/real_time        192700662 ns     37135553 ns            3  
prime_sieve<int64_t>/536870912/real_time        461129578 ns    112757174 ns            2  
prime_sieve<int64_t>/1073741824/real_time       992090610 ns    300949257 ns            1  
prime_sieve<int64_t>/real_time_BigO                  0.90 N          0.26 N     
```  
It is a good improvement in single thread complexity (last was 1.29N). I expect there are some more gains to be had because I have not profiled this new method yet.

---

## Comment 99

> Username: mborland  
> Created_at: 2020-08-24 04:09:45 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-678892851  

Latest Results:  
  
```  
-----------------------------------------------------------------------------------------  
Benchmark                                               Time             CPU   Iterations  
-----------------------------------------------------------------------------------------  
prime_sieve<int64_t>/2/real_time                     6.08 ns         6.07 ns    113767366  
prime_sieve<int64_t>/4/real_time                     51.0 ns         50.9 ns     13515048  
prime_sieve<int64_t>/8/real_time                     71.0 ns         71.0 ns      9777877  
prime_sieve<int64_t>/16/real_time                     106 ns          106 ns      6547406  
prime_sieve<int64_t>/32/real_time                     146 ns          146 ns      4820438  
prime_sieve<int64_t>/64/real_time                     217 ns          217 ns      3209322  
prime_sieve<int64_t>/128/real_time                    331 ns          330 ns      2122226  
prime_sieve<int64_t>/256/real_time                    588 ns          588 ns      1183560  
prime_sieve<int64_t>/512/real_time                   1087 ns         1087 ns       640559  
prime_sieve<int64_t>/1024/real_time                  2070 ns         2069 ns       337170  
prime_sieve<int64_t>/2048/real_time                  4452 ns         4450 ns       157479  
prime_sieve<int64_t>/4096/real_time                  9930 ns         9924 ns        69962  
prime_sieve<int64_t>/8192/real_time                 37572 ns        13552 ns        18783  
prime_sieve<int64_t>/16384/real_time                58303 ns        15416 ns        10785  
prime_sieve<int64_t>/32768/real_time               109392 ns        15245 ns         6294  
prime_sieve<int64_t>/65536/real_time               214308 ns        16721 ns         3255  
prime_sieve<int64_t>/131072/real_time              441972 ns        19288 ns         1598  
prime_sieve<int64_t>/262144/real_time              896304 ns        28966 ns          786  
prime_sieve<int64_t>/524288/real_time             1098929 ns        81140 ns          638  
prime_sieve<int64_t>/1048576/real_time            1601769 ns       126605 ns          440  
prime_sieve<int64_t>/2097152/real_time            2370154 ns       183676 ns          299  
prime_sieve<int64_t>/4194304/real_time            4452879 ns       351943 ns          158  
prime_sieve<int64_t>/8388608/real_time            7058822 ns       456815 ns          100  
prime_sieve<int64_t>/16777216/real_time          12679517 ns       930692 ns           55  
prime_sieve<int64_t>/33554432/real_time          25803331 ns      1762669 ns           25  
prime_sieve<int64_t>/67108864/real_time          53145041 ns      2603779 ns           14  
prime_sieve<int64_t>/134217728/real_time        103468162 ns      4873008 ns            7  
prime_sieve<int64_t>/268435456/real_time        235664828 ns      9392781 ns            4  
prime_sieve<int64_t>/536870912/real_time        378098840 ns     18413014 ns            2  
prime_sieve<int64_t>/1073741824/real_time       844867591 ns     37381758 ns            1  
prime_sieve<int64_t>/real_time_BigO                  0.78 N          0.03 N      
prime_sieve<int64_t>/real_time_RMS                     16 %             6 %      
kimwalish_primes<int64_t>/2/real_time                 200 ns          200 ns      3505182  
kimwalish_primes<int64_t>/4/real_time                 201 ns          201 ns      3483659  
kimwalish_primes<int64_t>/8/real_time                 206 ns          205 ns      3422449  
kimwalish_primes<int64_t>/16/real_time                219 ns          219 ns      3187866  
kimwalish_primes<int64_t>/32/real_time                226 ns          226 ns      3070826  
kimwalish_primes<int64_t>/64/real_time                237 ns          237 ns      2966324  
kimwalish_primes<int64_t>/128/real_time               250 ns          249 ns      2805139  
kimwalish_primes<int64_t>/256/real_time               291 ns          291 ns      2399146  
kimwalish_primes<int64_t>/512/real_time               346 ns          346 ns      2027377  
kimwalish_primes<int64_t>/1024/real_time             1089 ns         1088 ns       640964  
kimwalish_primes<int64_t>/2048/real_time             1461 ns         1460 ns       477841  
kimwalish_primes<int64_t>/4096/real_time             2245 ns         2243 ns       308465  
kimwalish_primes<int64_t>/8192/real_time             3540 ns         3537 ns       198477  
kimwalish_primes<int64_t>/16384/real_time            6041 ns         6037 ns       115883  
kimwalish_primes<int64_t>/32768/real_time           10272 ns        10266 ns        68039  
kimwalish_primes<int64_t>/65536/real_time           18826 ns        18814 ns        36440  
kimwalish_primes<int64_t>/131072/real_time          35211 ns        35186 ns        19853  
kimwalish_primes<int64_t>/262144/real_time          66172 ns        66116 ns        10455  
kimwalish_primes<int64_t>/524288/real_time         127314 ns       127195 ns         5494  
kimwalish_primes<int64_t>/1048576/real_time        249730 ns       249478 ns         2780  
kimwalish_primes<int64_t>/2097152/real_time        484138 ns       483663 ns         1443  
kimwalish_primes<int64_t>/4194304/real_time        945214 ns       944254 ns          737  
kimwalish_primes<int64_t>/8388608/real_time       1862814 ns      1860993 ns          378  
kimwalish_primes<int64_t>/16777216/real_time      3677418 ns      3673785 ns          189  
kimwalish_primes<int64_t>/33554432/real_time      7280352 ns      7268713 ns           95  
kimwalish_primes<int64_t>/67108864/real_time     14732963 ns     14717785 ns           47  
kimwalish_primes<int64_t>/134217728/real_time    31840443 ns     31804153 ns           22  
kimwalish_primes<int64_t>/268435456/real_time    64514102 ns     64438862 ns           11  
kimwalish_primes<int64_t>/536870912/real_time   129505881 ns    129354362 ns            5  
kimwalish_primes<int64_t>/1073741824/real_time  260234436 ns    259920477 ns            3  
kimwalish_primes<int64_t>/real_time_BigO             0.24 N          0.24 N      
kimwalish_primes<int64_t>/real_time_RMS                 2 %             2 %   
```  
The new algo still does not have me matching in single thread performance, but CPU complexity has been remarkably reduced.

---

## Comment 100

> Username: NAThompson  
> Created_at: 2020-08-25 11:48:18 UTC  
> Updated_at: 2020-08-25 11:48:33 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-679976123  

@mborland : I am very happy with these performance numbers, and regard it as "Boost quality", even if Kim Walish has a bit of a single-threaded edge on you!  
  
I still need to go over the ergonomics; but maybe @jeremy-murphy will help there.

---

## Review 101 [Commented]

> Username: NAThompson  
> Created_at: 2020-08-25 12:03:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-474411167  

📁 doc/sf/number_series.qbk

```diff
 293 |+ 
 294 |+ If you have a C++17 compatible compiler you are able to pass an execution policy to both functions. Any
 295 |+ policy besides `std::execution::seq` will enable internal multi-threading. If your compiler is not C++17 compatible the
```

> Username: NAThompson  
> Created_at: 2020-08-25 12:03:17 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r476394943  

Wait, is this correct? Shouldn't we throw an error if we get an esoteric execution policy? Which ones has compatible semantics with your implementation?  
  
(I'll ping @jzmaddock on this one because my knowledge on this is so poor.)

> Username: jzmaddock  
> Created_at: 2020-08-25 17:53:40 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r476633940  

I fear Nick is correct, for example C++20 draft says:  
  
> class execution::unsequenced_policy { unspecified };  
> 1 The class unsequenced_policy is an execution policy type used as a unique type to disambiguate parallel  
> algorithm overloading and indicate that a parallel algorithm’s execution may be vectorized, e.g., executed on  
> a single thread using instructions that operate on multiple data items.  
  
So that should be single threaded execution, since we don't actually support vectorisation.  
  
I would be inclined to say:  
  
* sequenced_policy, unsequenced_policy, single thread code.  
* parallel_policy or parallel_unsequenced_policy, multi threaded code.  
* Anything else.... well assuming anything else that's added follows the "may be" wording, I would say should be single threaded.  But they could equally be compiler errors on the grounds that we don't know what to do.

> Username: mborland  
> Created_at: 2020-08-25 21:51:03 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r476771558  

@jzmaddock That makes sense to me. GCC seems to ignore vectorisation if it can not accomplish it, but Intel C++ throws an error telling you if it is not SIMD compatible. I do not make any internal calls to execution policies anymore so the ones passed in are purely used in if statements to determine whether to run the sequential functions or not.


---

## Review 102 [Commented]

> Username: NAThompson  
> Created_at: 2020-08-25 12:09:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-474412159  

📁 doc/sf/number_series.qbk

```diff
 297 |+ 
 298 |+ For upper_bound <= 2[super 24] two threads will be used. For any value larger than 2[super 24] `std::thread::hardware_concurrency()` will be called,
 299 |+ and all available concurrency will be used. Additionally, the memory requirements are `bigo[](sqrt(N))`.
```

> Username: NAThompson  
> Created_at: 2020-08-25 12:04:47 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r476395763  

The complexity numbers are not appropriately grouped conceptually with the rest of the sentences in this.  
  
Could you add a complexity section? Maybe post some of your google benchmark numbers in it as well.

---

📁 doc/sf/number_series.qbk

```diff
 296 |+ sequential overloads will be used.
 297 |+ 
 298 |+ For upper_bound <= 2[super 24] two threads will be used. For any value larger than 2[super 24] `std::thread::hardware_concurrency()` will be called,
```

> Username: NAThompson  
> Created_at: 2020-08-25 12:05:08 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r476395994  

Could you put each sentence on its own line to make future diffs more legible?

---

📁 doc/sf/number_series.qbk

```diff
 302 |+ 
 303 |+ [h4 Examples]
 304 |+     // To calculate primes 2 - 1,000,000 in parallel
```

> Username: NAThompson  
> Created_at: 2020-08-25 12:05:43 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r476396317  

This doesn't include the upper bound right?


📁 include/boost/math/special_functions/interval_sieve.hpp

```diff
  34 |+ inline double get_double(const mpz_class &x) noexcept
  35 |+ {
  36 |+     return x.get_d()
```

> Username: NAThompson  
> Created_at: 2020-08-25 12:07:00 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r476396948  

hmm hmm dunno about this. Shouldn't there be a `static_cast` that does this without having to `#include` all this stuff?

> Username: mborland  
> Created_at: 2020-08-25 21:28:56 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r476752646  

GMP and MPIR do not support casting like multiprecision does. Do you think they are worth supporting? I have not fully tested their support throughout the rest of the code base. The reason they are there is this class was originally written entirely in C-style GMP by [Dr. Sorenson ](https://www.butler.edu/directory/user/jsorenso) who was kind enough to provide it to me along with other insights.

> Username: NAThompson  
> Created_at: 2020-08-25 23:46:25 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r476875182  

hmm; I think @jzmaddock will have a solution for this problem.

> Username: jzmaddock  
> Created_at: 2020-08-26 07:35:39 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r477094683  

Do we have tests for them?  
  
It doesn't look to me like the template will instantiate on mpz_t anyway?  
  
mpz_class is another matter, though it would be better not to have to #include stuff.  
  
How about (untested!):  
  
```  
class mpz_class;  // forward declare, no #include required.  
  
template <class I>  
typename std::enable_if<std::is_same<I, mpz_class>::value, double>::type get_double(const I & x)  
{  
   return x.get_d();  
}  
```  
?

> Username: NAThompson  
> Created_at: 2020-08-26 12:08:42 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r477249802  

I personally wouldn't support them directly, but would support them *through* multiprecision backends.

> Username: mborland  
> Created_at: 2020-08-26 14:35:48 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r477350954  

I think I will go with @NAThompson suggestion. I tried tests with GMP directly, and it does not come close to compiling. cpp_int is generally supported and tested with a few small bugs left. I have not tested gmp_int. I will have to read the docs for that one because I am not well versed in it.

> Username: NAThompson  
> Created_at: 2020-08-26 16:26:45 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r477430195  

@mborland : It took me a while to figure all the backends out, but I think you'll get the best performance numbers if you make sure the gmp backend works.

> Username: mborland  
> Created_at: 2020-08-27 03:15:55 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r478018729  

The gmp backend passes the tests now. cpp_int comes out a bit faster here.  
```  
Primes less than 2^30  
int64_t: 14265 ms  
cpp_int: 112723 ms  
mpz_int: 122094 ms  
```


📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
 297 |+ inline void prime_range(Integer lower_bound, Integer upper_bound, Container &primes)
 298 |+ {
 299 |+     prime_range(std::execution::par, lower_bound, upper_bound, primes);
```

> Username: NAThompson  
> Created_at: 2020-08-25 12:08:10 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r476397559  

Yeah I'd use the default of sequenced execution as @jeremy-murphy suggests.


📁 test/test_prime_sieve.cpp

```diff
 209 |+     //test_prime_range<int32_t>();
 210 |+     //test_prime_range<int64_t>();
 211 |+     //test_prime_range<uint32_t>();
```

> Username: NAThompson  
> Created_at: 2020-08-25 12:09:25 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r476398195  

Why are these tests all commented out?

> Username: mborland  
> Created_at: 2020-08-25 21:36:24 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r476759153  

`prime_range` needs to be update to use the new sieves. Since there have been such large fundamental changes I figured it was worth having outside review.


---

## Review 103 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-08-27 01:52:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-476331133  

📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
 244 |+     }
 245 |+ 
 246 |+     if(upper_bound <= 4096)
```

> Username: jeremy-murphy  
> Created_at: 2020-08-27 01:52:16 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r477902449  

4096 is a magic number here that's likely to change between CPU architectures.   
I would make it a parameter and either i) for the least effort, just make 4096 the default value, or ii) write a wrapper function that uses whatever is available (cache hierarchy, sizes, etc) to tune the value.

> Username: NAThompson  
> Created_at: 2020-08-27 02:01:10 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r477914800  

There are APIs to detect L1 cache size; for instance `perf` knows it.  
  
How is this done? I feel slightly ashamed I have no clue how to do it after this many years programming.

> Username: mborland  
> Created_at: 2020-08-27 02:29:16 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r477954276  

@NAThompson On linux you can call [sysconf](https://www.gnu.org/software/libc/manual/html_node/Constants-for-Sysconf.html). How to do it in a portable way? Good question.

> Username: jeremy-murphy  
> Created_at: 2020-08-27 23:10:21 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r478744448  

I'm not aware of a portable way to do it in C++ other than [these C++17 functions](https://en.cppreference.com/w/cpp/thread/hardware_destructive_interference_size) in the thread support library.  
I thought there were more, but I can't find them.

> Username: jeremy-murphy  
> Created_at: 2020-08-27 23:11:03 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r478744711  

Maybe Boost has or should have a cross-platform wrapper for that kind of thing?


---

## Review 104 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-08-27 02:08:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-476335831  

📁 reporting/performance/prime_sieve_performance.cpp

```diff
  96 |+ inline auto kimwalish_primes_helper(Integer upper, std::vector<Integer> primes) -> std::vector<Integer>
  97 |+ {
  98 |+     primesieve::generate_primes(upper, &primes);
```

> Username: jeremy-murphy  
> Created_at: 2020-08-27 02:08:00 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r477924057  

Where is this `generate_primes` function? Is it not part of this PR?

> Username: NAThompson  
> Created_at: 2020-08-27 02:09:38 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r477926316  

This is part of a separate library which is used to compare performance: https://github.com/kimwalisch/primesieve  
  
I asked @mborland to add this so we knew we had a good implementation.

> Username: jeremy-murphy  
> Created_at: 2020-08-27 04:43:10 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r478109372  

Aha.   
  
So is the BSD 2-Clause license not compatible with the Boost license?

> Username: NAThompson  
> Created_at: 2020-08-27 23:32:39 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r478751088  

@jeremy-murphy : This is only *using* the sieve in the performance section; there should be no licensing problems.

> Username: jeremy-murphy  
> Created_at: 2020-08-28 00:38:50 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r478768368  

Ah, what I meant is, can we not take KW's code directly because of the license? Is that why we're writing our own?

> Username: mborland  
> Created_at: 2020-08-28 02:38:09 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r478798402  

I was not aware of kimwalisch when this started. I would say the major distinguishing factor now is that he is limited to 2^64 as an upper bound where this supports arbitrary precision through the boost multi-precision back-ends.


---

## Review 105 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-08-27 04:50:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-476382830  

📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
  39 |+             least_divisors[i] = i;
  40 |+             resultant_primes.emplace_back(i);
  41 |+         }
```

> Username: jeremy-murphy  
> Created_at: 2020-08-27 04:50:26 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r478113800  

I would wager that this is a factor in the performance difference: Kim's implementation estimates the number of primes and reserves that number of elements upfront.  
I didn't look into exactly how it is estimated but presumably it is something like this: https://en.wikipedia.org/wiki/Prime-counting_function

> Username: NAThompson  
> Created_at: 2020-08-27 13:10:21 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r478406659  

Yes; I would agree-I thought we had this earlier . . .

> Username: mborland  
> Created_at: 2020-08-27 13:46:11 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r478431136  

I did. The snippets in question are below:  
  
```  
template<typename T>  
struct IsVector  
{  
    using type = T;  
    constexpr static bool value = false;  
};  
  
template<typename T>  
struct IsVector<std::vector<T>>  
{  
    using type = std::vector<T>;  
    constexpr static bool value = true;  
};  
  
template<typename T>  
constexpr bool is_vector_v = IsVector<T>::value;  
```  
and this was in the main `prime_sieve` (now callable from `prime_reserve`)  
  
```  
    if constexpr (detail::is_vector_v<decltype(primes)>)  
    {  
        primes.reserve(upper_bound / std::log(static_cast<double>(upper_bound)));  
    }  
```  
I thought making it a little less generic to gain performance was worthwhile.

> Username: jeremy-murphy  
> Created_at: 2020-08-27 23:51:13 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r478756336  

Call me a purist, but you'll make it faster by making it _more_ generic. Allocating memory for the output is not the responsibility of the algorithm.  
Now that I've seen KW's implementation, I guess you essentially copied the interface so that it was easier to compare performance?  
We don't have to copy the interface, ours can be _better_. And performance is not even the most important factor: allocating memory on the heap is simply a no-go in some contexts.  
To make it concrete, if the system that I work on at work needed to generate primes, we could not use KW's or this implementation (as it is), we would have to write our own. I wasn't always aware of such systems and limitations, it just comes with experience.

> Username: mborland  
> Created_at: 2020-08-28 02:25:21 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r478795213  

I initially was returning via output iterator instead of using a provided container. Profiling showed that more than 10% of my runtime was the call to `std::move` for the output iterator so I changed approaches to the current one.

> Username: NAThompson  
> Created_at: 2020-08-28 02:41:20 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r478799188  

Wow, `std::move` should be basically free.  
  
What were the compiler options?

> Username: mborland  
> Created_at: 2020-08-28 02:56:17 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r478802753  

I believe I used my benchmark options which are "-Ofast -march=native -g -MMD -lgmp -lbenchmark"

> Username: NAThompson  
> Created_at: 2020-08-28 12:05:02 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r479204460  

Could you go back to that commit and get the perf data? Maybe make a [flamegraph](https://github.com/brendangregg/FlameGraph) and post a screenshot of the assembly.

> Username: mborland  
> Created_at: 2020-08-28 18:04:29 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r479458348  

Let me edit the current interface to return via output iterator. That way I can be sure there were no confounding variables.

> Username: mborland  
> Created_at: 2020-08-30 02:28:18 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r479712096  

Here is the benchmark using the output iterator:  
  
```  
-----------------------------------------------------------------------------------------  
Benchmark                                               Time             CPU   Iterations  
-----------------------------------------------------------------------------------------  
prime_sieve<int64_t>/2/real_time                     27.4 ns         27.4 ns     25408214  
prime_sieve<int64_t>/4/real_time                     71.5 ns         71.5 ns      9734505  
prime_sieve<int64_t>/8/real_time                      111 ns          111 ns      6364109  
prime_sieve<int64_t>/16/real_time                     143 ns          143 ns      4894335  
prime_sieve<int64_t>/32/real_time                     211 ns          211 ns      3471114  
prime_sieve<int64_t>/64/real_time                     287 ns          287 ns      2376086  
prime_sieve<int64_t>/128/real_time                    418 ns          418 ns      1675569  
prime_sieve<int64_t>/256/real_time                    724 ns          724 ns       960573  
prime_sieve<int64_t>/512/real_time                   1239 ns         1238 ns       566861  
prime_sieve<int64_t>/1024/real_time                  2442 ns         2440 ns       282865  
prime_sieve<int64_t>/2048/real_time                  4990 ns         4987 ns       138413  
prime_sieve<int64_t>/4096/real_time                 10807 ns        10799 ns        64025  
prime_sieve<int64_t>/8192/real_time                 35965 ns        15096 ns        19436  
prime_sieve<int64_t>/16384/real_time                57982 ns        18296 ns        12024  
prime_sieve<int64_t>/32768/real_time               114760 ns        21607 ns         6069  
prime_sieve<int64_t>/65536/real_time               250246 ns        49289 ns         2809  
prime_sieve<int64_t>/131072/real_time              500623 ns        84600 ns         1364  
prime_sieve<int64_t>/262144/real_time              995925 ns       154794 ns          668  
prime_sieve<int64_t>/524288/real_time             1187309 ns       267116 ns          592  
prime_sieve<int64_t>/1048576/real_time            1865367 ns       435442 ns          376  
prime_sieve<int64_t>/2097152/real_time            2296991 ns       367161 ns          305  
prime_sieve<int64_t>/4194304/real_time            5657749 ns      1538171 ns          120  
prime_sieve<int64_t>/8388608/real_time            9159106 ns      2472841 ns           74  
prime_sieve<int64_t>/16777216/real_time          16958981 ns      4802018 ns           40  
prime_sieve<int64_t>/33554432/real_time          31315935 ns      5518799 ns           23  
prime_sieve<int64_t>/67108864/real_time          64255566 ns     12041533 ns           10  
prime_sieve<int64_t>/134217728/real_time        133253598 ns     24174532 ns            5  
prime_sieve<int64_t>/268435456/real_time        253561668 ns     48189439 ns            3  
prime_sieve<int64_t>/536870912/real_time        530443028 ns     95115274 ns            1  
prime_sieve<int64_t>/1073741824/real_time       993925796 ns    189247202 ns            1  
prime_sieve<int64_t>/real_time_BigO                  0.94 N          0.18 N      
prime_sieve<int64_t>/real_time_RMS                      8 %             4 %     
```  
For reference building in the container was :  
```  
prime_sieve<int64_t>/real_time_BigO                  0.78 N          0.03 N      
prime_sieve<int64_t>/real_time_RMS                     16 %             6 %    
```

> Username: mborland  
> Created_at: 2020-08-30 02:50:09 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r479713484  

And here is the relevant section from the call graph. The calls to `std::move` and `std::back_inserter` are not trivial. This was recorded running `test_par_prime_sieve_large`  
![move](https://user-images.githubusercontent.com/3745830/91650129-59e25380-ea41-11ea-9dce-617a52e86f7c.png)

> Username: NAThompson  
> Created_at: 2020-08-30 14:10:43 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r479774771  

Did you compile with `-fno-omit-frame-pointer`? I think these all might get inlined if you don't add that flag. (Obviously the `perf` data will get screwed up, but the google benchmark will be ok.)

> Username: mborland  
> Created_at: 2020-08-30 14:37:23 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r479777766  

I did not. `perf record --call-graph dwarf` works without frame pointers.

> Username: NAThompson  
> Created_at: 2020-08-30 15:29:17 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r479783302  

Ok, I think this is right on the boundary of a performance penalty that's acceptable if the ergonomics are improved.  
  
Personally I'm willing to approve merge it either way; but let's hear what other people have to say.

> Username: mborland  
> Created_at: 2020-08-30 18:15:35 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r479799850  

I am not convinced this interface change is worth ~5x more CPU runtime. Care to weigh in @pabristow @jzmaddock ?

> Username: jzmaddock  
> Created_at: 2020-09-01 07:54:18 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r480940451  

I think we have a number of issues here:  
  
* Using a container mandates dynamic memory allocation which may be a no-go in some contexts, and excludes things like std::array.  
* Using an output iterator is fundamentally unsafe in some contexts (ie where the output iterator is a pointer to a C array).  
* Using an output iterator on a container is non-trivial as it has to check and resize the container on each output :(  
  
Question: how far can we go with a range-based output?  As in "put the next N primes here".  The range could either be a pair of iterators, an iterator and the value N, or a boost::range.  If that's not faster than a container based interface (no memory allocation) then there's something wrong somewhere.  
  
But... I don't know how you would divide that up for multithreading?

> Username: jeremy-murphy  
> Created_at: 2020-09-02 01:59:48 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r481547603  

@mborland   
I can't see the code that is testing the output iterator, but yeah, a 5x difference is not acceptable. But it's also bizarre. Why is `move` being used at all? `int64_t` is a built-in data type, right, I thought it would just be copied? Is there the same huge difference for `int32_t`?  
  
_I_ would test it something like this. Create the vector to the size required outside the loop.  
If you're doing something different, I'd be curious to see it and know why.  
```  
template <typename Integer>  
Integer conservative_prime_count(Integer x)  
{  
    auto const c = 30 * log(113) / 113; // Magic numbers from Wikipedia.  
    return floor(c * x / log(x));  
}  
  
template <class Integer>  
void prime_sieve(benchmark::State& state)  
{  
    Integer upper = static_cast<Integer>(state.range(0));  
    std::vector<Integer> primes(conservative_prime_count(upper));  
  
    for(auto _ : state)  
    {  
        benchmark::DoNotOptimize(prime_sieve_helper(std::execution::par, upper, begin(primes)));  
    }  
    state.SetComplexityN(state.range(0));  
}  
```

> Username: mborland  
> Created_at: 2020-09-02 02:01:55 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r481548981  

@jzmaddock What about building a wrapper class that would support range-based output and other functions like last/next value, random value, etc? I think that is currently the best way to go about supporting a wider variety of data structures. As for removing dynamic memory allocation? I think it is in the realm of the possible, but would require massive retooling. You would become highly dependent on mutex locking which is not a trivial operation.

> Username: jeremy-murphy  
> Created_at: 2020-09-02 02:15:43 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r481557407  

@jzmaddock   
- Right. Doesn't matter how fast an algorithm is if it's not actually useful.  
- Not sure why this scenario is different (more or less safe) to any other scenario with an output iterator. Clients are responsible for creating and using output iterators safely. They can use a `back_insert_iterator` on `vector`.  
- Yes, but that's an issue for the client, not for the algorithm, right?  
  
(Btw, I'm going to use _x_ for prime numbers and `n` for data sizes.)  
  
It's funny, but the output iterator interface is almost a range interface. As you say, either an (iterator, iterator) or an (iterator, `n`) pair define a range. The interface to this algorithm is essentially (iterator, _x_), which means "generate prime numbers up to _x_", which translates to an output range of (iterator, prime_count(_x_)).   
So yeah, an alternative interface is (iterator, `n`), i.e., generate the first `n` prime numbers. Then it's up to the client to calculate `n` from _x_ if they know what _x_ they want to calculate up to.


---

## Comment 106

> Username: mborland  
> Created_at: 2020-09-01 01:29:33 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-684137177  

@NAThompson Do you know if -lgmp is automatically linked in the Jamfile, or how would I go about adding that flag? I think this is about ready for CI. I am paring back which of the tests CI runs because the full complement takes 33 mins to run on my machine.

---

## Review 107 [Commented]

> Username: jzmaddock  
> Created_at: 2020-09-01 07:43:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-479479018  

📁 doc/sf/number_series.qbk

```diff
 316 |+ Range of test upper_bound: 2[super 1] - 2[super 30]
 317 |+ 
 318 |+ <table xml:id="ex.htmltable">
```

> Username: jzmaddock  
> Created_at: 2020-09-01 07:43:02 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r480930176  

Not valid quickbook?


---

## Review 108 [Commented]

> Username: jzmaddock  
> Created_at: 2020-09-01 07:44:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-479479921  

📁 include/boost/math/special_functions/interval_sieve.hpp

```diff
  49 |+ class IntervalSieve
  50 |+ {
  51 |+ #ifdef __SIZEOF_INT128__   // Defined in GCC 4.6+, clang, intel. MSVC does not define.
```

> Username: jzmaddock  
> Created_at: 2020-09-01 07:44:23 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r480931919  

Better to use BOOST_HAS_INT128 as there are some corner cases where __SIZEOF_INT128__  does the wrong thing.


---

## Comment 109

> Username: NAThompson  
> Created_at: 2020-09-01 12:58:24 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-684833316  

@mborland:  
  
> Do you know if -lgmp is automatically linked in the Jamfile, or how would I go about adding that flag?  
  
Here's an example:  
  
```  
test$ cat Jamfile.v2| grep lgmp  
   [ run tanh_sinh_mpfr.cpp : : : <linkflags>-lgmp <linkflags>-lmpfr [ check-target-builds ../config//has_mpfr : : <build>no ] [ requires cxx11_hdr_initializer_list cxx11_auto_declarations cxx11_lambdas cxx11_unified_initialization_syntax cxx11_smart_ptr ] release <toolset>clang:<cxxflags>-Wno-literal-range ]  
```  
  
>  I think this is about ready for CI. I am paring back which of the tests CI runs because the full complement takes 33 mins to run on my machine.  
  
Good idea; what are you learning in 33 minutes that you can't learn in 30 seconds? (That's a real question, not rhetorical . . .)

---

## Comment 110

> Username: mborland  
> Created_at: 2020-09-03 18:25:37 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-686669379  

@NAThompson With the CI tests passed I think this PR should be merged as is; it currently does its one thing well. My thought is the best way go about offering the additional interfaces and functionality discussed would be building a wrapper class. The scope of that is sufficiently different that it should warrant its own PR, which I can begin with the long weekend coming up.

---

## Comment 111

> Username: jeremy-murphy  
> Created_at: 2020-09-04 01:25:33 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-686845894  

I appreciate the amount of work that has gone into this module, I really do, so I understand that it's frustrating when people like me criticize it or try to block it, but I have to stand up for what I think is important.  
I think we all have given in to a very seductive siren called premature optimization. The focus has been on performance benchmarks since early on. I know that's the fun part of implementing these algorithms. But we have an entire community of potential users to think of, which is the hard part, but I think it's more important.  
So I think we should get it to work for everyone (which basically means, make it generic and try to avoid allocating on the heap where possible), and then optimize performance later.  
  
A second, more specific, point is that I simply find it hard to believe that a generic interface is slower here. I suspect something is amiss in either the implementation of the generic interface, its benchmark, or possibly in the move semantics of the integer type being tested.  The rest of the world seems to get along fine with generic interfaces, so I just have trouble accepting that they are an issue here. Someone prove me wrong, please. I will try to find time this weekend to run the benchmarks myself and confirm or reject my hypotheses.  
  
These algorithms will be a great contribution to Boost.Math and I want to see this PR get merged. I just think we've got our priorities wrong in terms of acceptance criteria.

---

## Comment 112

> Username: mborland  
> Created_at: 2020-09-04 21:25:54 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-687394729  

@jeremy-murphy I have no issues with criticism. You guys clearly have more experience than I do, and it has certainly helped the quality of this PR. My concern was getting stalled at the goal line with no clear consensus over the interface. That is why I recommend a wrapper which would be able to offer more options and functionality than currently presented.  
  
Here is the gist of the changes I made to test returning via output iterator  
  
```  
template<class ExecutionPolicy, class Integer, class OutputIterator>  
auto prime_sieve(ExecutionPolicy&& policy, Integer upper_bound, OutputIterator output) -> decltype(output)  
{  
    std::vector<Integer> primes;  
    prime_reserve(upper_bound, primes);  
  
    // As is  
  
    return std::move(primes.begin(), primes.end(), output);  
}  
```  
The call from test or benchmark was then changed to:  
```  
boost::math::prime_sieve(std::execution::par, upper_bound, std::back_inserter(primes));  
```  
  
No changes were made to the makefile between benchmarking the above and the committed code.

---

## Comment 113

> Username: NAThompson  
> Created_at: 2020-09-05 02:39:27 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-687531780  

> My concern was getting stalled at the goal line with no clear consensus over the interface.   
  
Yeah, I want this in pretty soon. I'll do a final pass this weekend.

---

## Review 114 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-09-06 06:21:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-483114897  

📁 include/boost/math/special_functions/prime_sieve.hpp

```diff
 237 |+ constexpr void prime_reserve(Integer upper_bound, std::vector<Integer> &prime_container)
 238 |+ {
 239 |+     prime_container.reserve(static_cast<double>(upper_bound) / std::log(static_cast<double>(upper_bound)));
```

> Username: jeremy-murphy  
> Created_at: 2020-09-06 06:21:29 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r484027901  

I recommend that you just lift this equation (x / ln(x)) out, give it a name and a brief explanation of what it means.

> Username: jeremy-murphy  
> Created_at: 2020-09-07 04:55:18 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r484182467  

This one is, if I'm not mistaken, an underestimate of the true number of primes. (Not saying that _under_ estimate is bad, it's just a good name to differentiate from an overestimate.)


---

## Comment 115

> Username: jeremy-murphy  
> Created_at: 2020-09-06 06:46:02 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-687711263  

> @jeremy-murphy I have no issues with criticism. You guys clearly have more experience than I do, and it has certainly helped the quality of this PR. My concern was getting stalled at the goal line with no clear consensus over the interface. That is why I recommend a wrapper which would be able to offer more options and functionality than currently presented.  
  
I think this comes back to the premature optimization. Because performance was given primary importance, it now feels difficult to choose a good interface. It should be the other way around: design a good interface, then optimize.  
  
>   
> Here is the gist of the changes I made to test returning via output iterator  
>   
> ```  
> template<class ExecutionPolicy, class Integer, class OutputIterator>  
> auto prime_sieve(ExecutionPolicy&& policy, Integer upper_bound, OutputIterator output) -> decltype(output)  
> {  
>     std::vector<Integer> primes;  
>     prime_reserve(upper_bound, primes);  
>   
>     // As is  
>   
>     return std::move(primes.begin(), primes.end(), output);  
> }  
> ```  
>   
> The call from test or benchmark was then changed to:  
>   
> ```  
> boost::math::prime_sieve(std::execution::par, upper_bound, std::back_inserter(primes));  
> ```  
>   
> No changes were made to the makefile between benchmarking the above and the committed code.  
  
Yeah, so that's the problem. Don't allocate a temporary buffer in the algorithm, and don't use `back_inserter` in the performance benchmark. I showed what the benchmark code should look like in an earlier comment. The user allocates the memory before running the algorithm. That's why this function that over estimates the size needs to be available to the user. The algorithm just gets a pointer to the start of that memory and implicitly gets a guarantee that there is enough space for the result. Instead of the user giving a _container_ to the algorithm, the user gives an _iterator_ to the first element of some container (and the implicit guarantee).

---

## Comment 116

> Username: jeremy-murphy  
> Created_at: 2020-09-06 06:46:54 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-687711350  

> > My concern was getting stalled at the goal line with no clear consensus over the interface.  
>   
> Yeah, I want this in pretty soon. I'll do a final pass this weekend.  
  
Sure, but there is no deadline or rush for this, right?

---

## Comment 117

> Username: NAThompson  
> Created_at: 2020-09-06 16:18:28 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-687831879  

> Sure, but there is no deadline or rush for this, right?  
  
No, but to me it seems demoralizing to take this long.  
  
As for the cost of the move constructor interface, it might be valuable to just add it and hope that future compiler optimizations get it right.

---

## Comment 118

> Username: mborland  
> Created_at: 2020-09-06 18:51:03 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-687868753  

> As for the cost of the move constructor interface, it might be valuable to just add it and hope that future compiler optimizations get it right.  
  
I doubt this goes away. Move semantics and C++11 were finalized in GCC 4.8 and we are on 10.2 now. I think avoiding the move is inline with Scott Myers' advice of "Assume that move operations are not present, not cheap, and not used." (Effective Modern C++, item 29). If anything the argument for calling `std::move` got worse with C++17 with copy elision now being guaranteed. I just do not know how to avoid calling `std::move` for an output iterator.  
  
As for taking in a pointer/iterator to a pre-allocated block of memory and removing temporary buffers? The way that I avoid data races is using temporary buffers so that mutexes are not required. Can this be made to work without temporary buffers? Certainly, if a large amount is re-written to implement locking. Would it be as fast? I suspect that it would not be close.

---

## Comment 119

> Username: jeremy-murphy  
> Created_at: 2020-09-07 01:43:14 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-687971701  

> As for taking in a pointer/iterator to a pre-allocated block of memory and removing temporary buffers? The way that I avoid data races is using temporary buffers so that mutexes are not required. Can this be made to work without temporary buffers? Certainly, if a large amount is re-written to implement locking. Would it be as fast? I suspect that it would not be close.  
  
There is no need for locking and mutexes, please forget about them.  
Look at the parallel algorithms in the STL. Do any of them take a container for the output?  
You don't have to remove temporary buffers altogether, just stop passing containers into functions and using `emplace_back` inside those functions.

---

## Comment 120

> Username: jeremy-murphy  
> Created_at: 2020-09-07 04:50:17 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-688032553  

> > Sure, but there is no deadline or rush for this, right?  
>   
> No, but to me it seems demoralizing to take this long.  
  
Let's just take it as a lesson to get the interface right first next time. It's much easier to merge the right interface in and iterate on the performance afterwards than it is to iterate on the interface afterwards.   
  
  
> As for the cost of the move constructor interface, it might be valuable to just add it and hope that future compiler optimizations get it right.  
  
This whole move constructor thing could turn out to be a red herring. There are more fundamental implementation problems to be fixed first.

---

## Comment 121

> Username: jeremy-murphy  
> Created_at: 2020-09-07 23:12:26 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-688536321  

> > As for taking in a pointer/iterator to a pre-allocated block of memory and removing temporary buffers? The way that I avoid data races is using temporary buffers so that mutexes are not required. Can this be made to work without temporary buffers? Certainly, if a large amount is re-written to implement locking. Would it be as fast? I suspect that it would not be close.  
>   
> There is no need for locking and mutexes, please forget about them.  
> Look at the parallel algorithms in the STL. Do any of them take a container for the output?  
> You don't have to remove temporary buffers altogether, just stop passing containers into functions and using `emplace_back` inside those functions.  
  
And, start small. Get the interface to `linear_sieve` right first. Then move on to the next smallest thing. It might all just make sense from there on. If you get stuck, ask for help.

---

## Comment 122

> Username: jzmaddock  
> Created_at: 2020-09-08 07:54:45 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-688690649  

Guys my apologies for not being across this - I want to take a detailed look later this week, but some first thoughts:  
  
@jeremy-murphy : the reason I said that the output iterator interface is fundamentally unsafe in this context is that you don't know up front how many elements will be copied to it, and therefore it can not be safely used with a buffer on on the stack.  In contrast, every other std lib algorithm I can think of you either know, or can calculate, how many elements will be copied.  
That said, I can see the need:  consider a program that's brute force factorizing a number N, it asks for every prime below N/2, but _we don't need to store them_ instead the output iterator simply checks each one as a possible candidate and stores it somewhere (or does something with it) if it is indeed a factor.  This sort of "computation use" rather than "storage use" also leads to another design decision: _what order do the primes arrive in_?  This is for the multithreaded case, so we have sequential, threaded but sequential output, threaded and unordered output.  
I guess what myself and @jeremy-murphy are saying here, is that we may need to sacrifice a little performance in the "store everything in a container" benchmark, for faster performance in other use cases.  But let me get up to speed on the code before reworking anything - I'd like to try and work out for myself what the overhead of such changes might be.

---

## Comment 123

> Username: jeremy-murphy  
> Created_at: 2020-09-08 11:08:00 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-688796744  

@jzmaddock, the user _can_ calculate a safe overestimate of the number of elements that will be produced. The formula is in an earlier comment of mine, called "conservative_prime_function" or something. It's straight from Wikipedia.  
  
But yes, the example of an output iterator that computes rather than stores the prime numbers being emitted is a good use case.  
I don't _think_ we'll need to sacrifice anything in performance just to make it generic.   
To make it guarantee the sequence that the output is produced in, yeah, that's a different story; probably defeats the usefulness of parallelism.

---

## Comment 124

> Username: jzmaddock  
> Created_at: 2020-09-08 19:05:20 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-689077000  

I'm beginning to see the issue - since the sieve requires access to all the primes previously found we need to store them in a container, so we might as well use a container for output - using an OutputIterator would require an extra copy step which is superfluous.  
  
@jeremy-murphy can you see a way to make this work?  
  
Meanwhile the following patch fixes a *lot* of gcc warnings, and gets things compiling on msvc (which still has pages and pages of warnings I haven't looked at yet).  
  
```  
 .../math/special_functions/interval_sieve.hpp      | 23 ++++++++++++++++------  
 .../boost/math/special_functions/prime_sieve.hpp   | 14 +++++++++----  
 2 files changed, 27 insertions(+), 10 deletions(-)  
  
diff --git a/include/boost/math/special_functions/interval_sieve.hpp b/include/boost/math/special_functions/interval_sieve.hpp  
index 7c021a5c1..9d6edc7f5 100644  
--- a/include/boost/math/special_functions/interval_sieve.hpp  
+++ b/include/boost/math/special_functions/interval_sieve.hpp  
@@ -31,9 +31,9 @@ class IntervalSieve  
 {    
       
 #ifdef BOOST_HAS_INT128    // Defined in GCC 4.6+, clang, intel. MSVC does not define.   
-using int_128t = __int128; // One machine word smaller than the boost equivalent  
+using int_128t = unsigned __int128; // One machine word smaller than the boost equivalent  
 #else  
-using int_128t = boost::multiprecision::int128_t;  
+using int_128t = boost::multiprecision::uint128_t;  
 #endif  
   
 private:  
@@ -56,10 +56,21 @@ private:  
             23'616'331'489, 85'157'610'409, 196'265'095'009, 2'871'842'842'801, 26'250'887'023'729, 112'434'732'901'969,  
             178'936'222'537'081, 696'161'110'209'049, 2'854'909'648'103'881, 6'450'045'516'630'769, 11'641'399'247'947'921,  
             190'621'428'905'186'449, 196'640'148'121'928'601, 712'624'335'095'093'521, 1'773'855'791'877'850'321,  
-            2'327'687'064'124'474'441, 6'384'991'873'059'836'689, 8'019'204'661'305'419'761, 10'198'100'582'046'287'689,  
+            2'327'687'064'124'474'441, 6'384'991'873'059'836'689, 8'019'204'661'305'419'761, 10'198'100'582'046'287'689u,  
+  
+            (static_cast<int_128t>(0x3uLL) << 64) | 0xc956f827e0524359uLL,  
+            (static_cast<int_128t>(0xbuLL) << 64) | 0x539315b3b1268d59uLL,  
+            (static_cast<int_128t>(0x1cuLL) << 64) | 0xec87d86ca60b50a1uLL,  
+            (static_cast<int_128t>(0x32uLL) << 64) | 0xc6d3496f20db3d81uLL,  
+            (static_cast<int_128t>(0x74uLL) << 64) | 0x210967a12ba94be1uLL,  
+            (static_cast<int_128t>(0x2e3uLL) << 64) | 0xec11ddc09fd65c51uLL,  
+            (static_cast<int_128t>(0x753uLL) << 64) | 0x641c14b397c27bf1uLL,  
+            (static_cast<int_128t>(0x1511uLL) << 64) | 0x85fdf38d1fc9ce21uLL,  
+            (static_cast<int_128t>(0x3e8buLL) << 64) | 0xaba417e222ca5091uLL  
+            /*  
             69'848'288'320'900'186'969, 208'936'365'799'044'975'961, 533'552'663'339'828'203'681, 936'664'079'266'714'697'089,  
             2'142'202'860'370'269'916'129, 13'649'154'491'558'298'803'281, 34'594'858'801'670'127'778'801,   
-            99'492'945'930'479'213'334'049, 295'363'187'400'900'310'880'401  
+            99'492'945'930'479'213'334'049, 295'363'187'400'900'310'880'401*/  
         };  
     };  
   
@@ -126,7 +137,7 @@ void IntervalSieve<Integer, PrimeContainer, Container>::Settdlimit() noexcept  
     }  
     plimit_ = pss_.prime[i];  
   
-    double tdlimit_guess = 1 + std::fmod(dr, pss_.ps[i]);  
+    double tdlimit_guess = 1 + std::fmod(dr, (double)pss_.ps[i]);  
     if(tdlimit_guess * tdlimit_guess >= dr)  
     {  
         tdlimit_ = static_cast<std::size_t>(std::sqrt(dr));  
@@ -196,7 +207,7 @@ void IntervalSieve<Integer, PrimeContainer, Container>::WriteOutput(Container &r  
 template<class Integer, class PrimeContainer, class Container>  
 bool IntervalSieve<Integer, PrimeContainer, Container>::Psstest(const std::size_t pos) noexcept  
 {  
-    const Integer n {left_ + pos};  
+    const Integer n { static_cast<Integer>(left_ + pos) };  
     const Integer exponent {(n - 1) / 2};  
     const std::int_fast64_t nmod8 = static_cast<std::int_fast64_t>(n % 8);  
   
diff --git a/include/boost/math/special_functions/prime_sieve.hpp b/include/boost/math/special_functions/prime_sieve.hpp  
index 3e79dea44..a5ca2b7f1 100644  
--- a/include/boost/math/special_functions/prime_sieve.hpp  
+++ b/include/boost/math/special_functions/prime_sieve.hpp  
@@ -255,8 +255,11 @@ void prime_sieve(ExecutionPolicy&& policy, Integer upper_bound, Container &prime  
         boost::math::detail::linear_sieve(static_cast<Integer>(upper_bound), primes);  
     }  
   
-    else if constexpr (std::is_same_v<std::remove_reference_t<decltype(policy)>, decltype(std::execution::seq)> ||   
-                       std::is_same_v<std::remove_reference_t<decltype(policy)>, decltype(std::execution::unseq)>)  
+    else if constexpr (std::is_same_v<std::remove_reference_t<decltype(policy)>, decltype(std::execution::seq)>  
+#if __cpp_lib_execution > 201900  
+       || std::is_same_v<std::remove_reference_t<decltype(policy)>, decltype(std::execution::unseq)>  
+#endif  
+       )  
     {  
         boost::math::detail::linear_sieve(linear_sieve_limit<Integer>, primes);  
         boost::math::detail::sequential_segmented_sieve(linear_sieve_limit<Integer>, upper_bound, primes);  
@@ -303,8 +306,11 @@ void prime_range(ExecutionPolicy&& policy, Integer lower_bound, Integer upper_bo  
         boost::math::detail::linear_sieve(static_cast<Integer>(upper_bound), primes);  
     }  
   
-    else if constexpr (std::is_same_v<std::remove_reference_t<decltype(policy)>, decltype(std::execution::seq)> ||   
-                       std::is_same_v<std::remove_reference_t<decltype(policy)>, decltype(std::execution::unseq)>)  
+    else if constexpr (std::is_same_v<std::remove_reference_t<decltype(policy)>, decltype(std::execution::seq)>   
+#if __cpp_lib_execution > 201900  
+       || std::is_same_v<std::remove_reference_t<decltype(policy)>, decltype(std::execution::unseq)>  
+#endif  
+       )  
     {  
         if(limit <= linear_sieve_limit<Integer>)  
         {     
```  
  
Also note that the mpz_int tests all fail with msvc (using MPIR rather than "real" GMP), I'm not sure if that's an issue with this library, MPIR, or multiprecision:  
  
```  
D:\data\boost\boost\libs\math\test\test_prime_sieve.cpp(214): test 'primes.size() == 1'061' ('2057' == '1061') failed in function 'void __cdecl test_interval_sieve<class boost::multiprecision::number<struct boost::multiprecision::backends::gmp_int,1>>(void)'  
D:\data\boost\boost\libs\math\test\test_prime_sieve.cpp(218): test 'primes.size() == 8'363' ('20572' == '8363') failed in function 'void __cdecl test_interval_sieve<class boost::multiprecision::number<struct boost::multiprecision::backends::gmp_int,1>>(void)'  
D:\data\boost\boost\libs\math\test\test_prime_sieve.cpp(222): test 'primes.size() == 68'906' ('205714' == '68906') failed in function 'void __cdecl test_interval_sieve<class boost::multiprecision::number<struct boost::multiprecision::backends::gmp_int,1>>(void)'  
```  
  
Any ideas where to start looking?  
  
More comments coming soon...

---

## Comment 125

> Username: mborland  
> Created_at: 2020-09-08 21:16:41 UTC  
> Updated_at: 2020-09-08 22:58:41 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-689140942  

@jzmaddock What version of GCC and flags are you using to emit warnings? I get none with my configuration on GCC 10.2 but clearly you have even stricter build flags than I do. I will have to build MPIR and run some tests. I thought that MPIR was supposed to be a drop-in replacement for GMP, but I have never actually used it before.   
  
Edit: I built MPIR 3.0.0 from source and can not replicate the errors

---

## Comment 126

> Username: jeremy-murphy  
> Created_at: 2020-09-09 01:41:21 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-689245790  

I think a classic _sieve_ must use a fixed amount of temporary buffer proportional to the size (range) of prime numbers computed, I don't think there is any avoiding that. I haven't looked at how the more advanced sieves using a wheel work, but I think they also still require temporary storage proportional to the problem size.  
  
But importantly, that temporary buffer (at least of a classic sieve) can be a bitset, it doesn't have to store the actual integer values.  
Hmmm, the current implementation of `linear_sieve` appears to depend on two blocks of memory (`resultant_primes` and `least_divisors`) storing integers. I'm sure we can get this down to one block of memory storing just bits. (In fact there is a didactic implementation of exactly this in _From Mathematics to Generic Programming_, chapter 3 -- did I already mention it?)  
  
Anyway, another remark I can make is that presumably these sieves can start emitting the low prime numbers in sequence before they have finished computing the high ones? If an output iterator is doing computation one each emitted number, that sounds like something that could happen in parallel.

---

## Comment 127

> Username: jeremy-murphy  
> Created_at: 2020-09-11 07:57:23 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-690939607  

Sorry, I missed the fact that the linear sieve is the Gries-Misra algorithm, so although the result can be external to the algorithm, it still needs one temporary storage.

---

## Comment 128

> Username: jzmaddock  
> Created_at: 2020-09-15 16:52:49 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-692841963  

OK, it's taken a while, but I'm finally starting to get a handle on this, with a few small experiments.  
  
My aim was to look at Matt's basic `linear_sieve` algorithm and compare it to an output-iterator version, but as ever you rather quickly disappear down a performance rabbit-hole!  
  
My starting point was a schoolboy prime sieve, with the rather obvious optimisation of just storing the odd numbers:  
  
```  
template<class Integer>  
inline void do_linear_sieve_classical_bytes(Integer upper_bound, std::vector<Integer>& primes)  
{  
   std::vector<unsigned char> masks(upper_bound / 2 + 1, 1u);  
   primes.push_back(2u);  
   for (Integer index = 1; index < masks.size(); ++index)  
   {  
      if (masks[index])  
      {  
         primes.push_back(2 * index + 1);  
         for (Integer clear = index * 3 + 1; clear < masks.size(); clear += index * 2 + 1)  
            masks[clear] = 0;  
      }  
   }  
}  
```  
  
Which in turn can be trivially modified to use a `vector<bool>` for an 8x space saving, and/or to use an output iterator rather than a container.  
  
I also modified Matt's performance test program to remove the memory allocation from the benchmark, as otherwise that dominates for small sieves:  
  
```  
template <class Integer>  
void prime_sieve_seq(benchmark::State& state)  
{  
    Integer upper = static_cast<Integer>(state.range(0));  
    std::vector<Integer> primes;  
    boost::math::prime_reserve(upper, primes);  
    for(auto _ : state)  
    {  
        primes.clear();  
        boost::math::prime_sieve(upper, primes);  
    }  
    state.SetComplexityN(state.range(0));  
}  
```  
  
Here's the performance results for MSVC x64:  
  
  
Largest prime | MB prime_sieve | KW | Classical Bitwise | Classical Bytes | Classical Bytes OI  
-- | -- | -- | -- | -- | --  
4 | 0.8254397835 | 3.6129905277 | 0.9445196211 | 0.8119079838 | 0.8037889039  
8 | 0.6365217391 | 2.347826087 | 0.6547826087 | 0.5339130435 | 0.5460869565  
16 | 0.6006622517 | 1.9139072848 | 0.5682119205 | 0.4529801325 | 0.4390728477  
32 | 0.6612903226 | 1.6021505376 | 0.5752688172 | 0.4311827957 | 0.4123655914  
64 | 0.7027972028 | 1.0734265734 | 0.5104895105 | 0.3601398601 | 0.3367132867  
128 | 0.7168458781 | 0.5913978495 | 0.4301075269 | 0.270609319 | 0.2419354839  
256 | 0.609375 | 0.3465073529 | 0.375 | 0.234375 | 0.2022058824  
512 | 0.5822091062 | 0.1876053963 | 0.3086003373 | 0.1985666105 | 0.1787521079  
1024 | 0.6521237304 | 0.4395198523 | 0.3462603878 | 0.2126038781 | 0.189289012  
2048 | 0.6719807317 | 0.2760637688 | 0.4240165157 | 0.2207821998 | 0.1960087166  
4096 | 0.6984298555 | 0.2000227557 | 0.8571509842 | 0.2531573558 | 0.2053703493  
8192 | 0.8410647994 | 0.1556152476 | 0.505840099 | 0.3705369269 | 0.3536957112  
16384 | 0.7843589239 | 0.1176594608 | 0.5269867596 | 0.3916875158 | 0.3750175695  
32768 | 1.0158743162 | 0.1257111129 | 0.7047322574 | 0.5079417092 | 0.4977836032  
65536 | 1.0713084811 | 0.114616524 | 0.7807307071 | 0.5348850501 | 0.5232543187  
131072 | 1.0716680717 | 0.1059815871 | 0.7675615948 | 0.5356542405 | 0.5356542405  
262144 | 0.7496650183 | 0.0696020702 | 0.5113633124 | 0.3665591739 | 0.3706970633  
524288 | 0.7506699162 | 0.0671466448 | 0.5099468485 | 0.3835496096 | 0.3748325731  
1048576 | 0.7604895116 | 0.0662033134 | 0.5296267183 | 0.4634234049 | 0.4531251351  
2097152 | 0.7111111289 | 0.0662020471 | 0.4995121764 | 0.4995121764 | 0.4995121764  
4194304 | 0.6077519169 | 0.0523255659 | 0.4167441811 | 0.4254263376 | 0.4171511735  
8388608 | 0.5637254965 | 0.0484290071 | 0.4044117647 | 0.3892733553 | 0.3892733553  
16777216 | 0.5206611593 | 0.0404388743 | 0.390909095 | 0.4586776857 | 0.4586776857  
33554432 | 0.5340909069 | 0.0409090908 | 0.3993506481 | 0.8409090882 | 0.818181817  
  
In the above table I scored @mborland 's `linear_sieve` 1 in each row, so performance is relative to that.  
  
As you can see there is no difference between container-based and output iterator based algorithms (last 2 columns) which to be fare would be expected for simple linear sieves, and perhaps not so much for the more sophisticated algorithms.  What's interesting though is how competitive a simple linear sieve is compared to the others even when the sieve becomes large.  It's only when the max prime is really large (larger than shown in the table above) that the better O(N) algorithms take over.  
  
I also note that while Kim Walish's code has a much higher set up cost (memory allocations mostly), it really kicks butt at higher max primes - it is multi-threaded internally though.  
  
So..... where now?  I think I'd like to see how well a segmented algorithm can cope with an Output Iterator interface and then we can go from there..... I'm going to post on the mailing list as well and solicit design feedback.  
  
And finally, I'll leave you with one more table - this measures how long it takes to divide out all the prime factors below N from `std::numeric_limits<std::uint32_t>::max()`, here I gave @mborland 's `prime_factor` an arbitrary score of 1 each time.  Note how well the classical sieve does - particularly as it does not need to store the primes where as `prime_sieve` and Kim Walish's code do.  
  
  
Max factors | Classical sieve | KW  
-- | -- | --  
4 | 0.4477777778 | 2.0055555556  
8 | 0.2954397394 | 1.2540716612  
16 | 0.2831168831 | 1.1402597403  
32 | 0.2622377622 | 0.8129370629  
64 | 0.2404371585 | 0.7431693989  
128 | 0.2341463415 | 0.6809756098  
256 | 0.2825484765 | 0.6668975069  
512 | 0.29705027 | 0.6231823847  
1024 | 0.3146331058 | 0.944112628  
2048 | 0.3739737777 | 0.7521137116  
4096 | 0.4372207775 | 0.6139894646  
8192 | 0.538028169 | 0.5146210597  
16384 | 0.5999933321 | 0.4186100785  
32768 | 0.654759873 | 0.3797658463  
65536 | 0.7007710444 | 0.31855392  
131072 | 0.6812684203 | 0.2865900269  
262144 | 0.622500571 | 0.2549282083  
524288 | 0.5513441924 | 0.2446278057  
1048576 | 0.6980518723 | 0.2303570759  
2097152 | 0.7043478359 | 0.2506793357  
4194304 | 0.6572690387 | 0.2502527826  
8388608 | 0.6176470561 | 0.2386363654  
16777216 | 0.756250002 | 0.2291666673  
33554432 | 1.4244186047 | 0.2131782921

---

## Comment 129

> Username: jzmaddock  
> Created_at: 2020-09-15 16:54:03 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-692842675  

Here's the benchmark program, including the classical sieves:  
  
```  
// Copyright 2020 Matt Borland  
//  
// Use, modification and distribution are subject to the  
// Boost Software License, Version 1.0.  
// (See accompanying file LICENSE_1_0.txt  
// or copy at http://www.boost.org/LICENSE_1_0.txt)  
  
#include <boost/math/special_functions/prime_sieve.hpp>  
#include <boost/math/special_functions/interval_sieve.hpp>  
#include <boost/multiprecision/cpp_int.hpp>  
//#include <boost/multiprecision/gmp.hpp>  
#include <benchmark/benchmark.h>  
#include <primesieve.hpp>  
#include <vector>  
  
// Individual Algos  
template<class Integer>  
inline void do_linear_sieve_classical_bits(Integer upper_bound, std::vector<Integer>& primes)  
{  
   std::vector<bool> masks(upper_bound / 2 + 1, true);  
   primes.push_back(2u);  
   for (Integer index = 1; index < masks.size(); ++index)  
   {  
      if (masks[index])  
      {  
         primes.push_back(2 * index + 1);  
         for (Integer clear = index * 3 + 1; clear < masks.size(); clear += index * 2 + 1)  
            masks[clear] = false;  
      }  
   }  
}  
template<class Integer>  
inline void do_linear_sieve_classical_bytes(Integer upper_bound, std::vector<Integer>& primes)  
{  
   std::vector<unsigned char> masks(upper_bound / 2 + 1, 1u);  
   primes.push_back(2u);  
   for (Integer index = 1; index < masks.size(); ++index)  
   {  
      if (masks[index])  
      {  
         primes.push_back(2 * index + 1);  
         for (Integer clear = index * 3 + 1; clear < masks.size(); clear += index * 2 + 1)  
            masks[clear] = 0;  
      }  
   }  
}  
template<class Integer, class OutputIterator>  
inline OutputIterator do_linear_sieve_classical_bytes_oi(Integer upper_bound, OutputIterator primes)  
{  
   std::vector<unsigned char> masks(upper_bound / 2 + 1, 1u);  
   *primes++ = 2;  
   for (Integer index = 1; index < masks.size(); ++index)  
   {  
      if (masks[index])  
      {  
         *primes++ = 2 * index + 1;  
         for (Integer clear = index * 3 + 1; clear < masks.size(); clear += index * 2 + 1)  
            masks[clear] = 0;  
      }  
   }  
   return primes;  
}  
  
template<class Integer>  
void linear_sieve(benchmark::State& state)  
{  
    Integer upper = static_cast<Integer>(state.range(0));  
    std::vector<Integer> primes;  
    boost::math::prime_reserve(upper, primes);  
    for(auto _ : state)  
    {  
        primes.clear();  
        boost::math::detail::linear_sieve(upper, primes);  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
template<class Integer>  
void linear_sieve_classical_bits(benchmark::State& state)  
{  
   Integer upper = static_cast<Integer>(state.range(0));  
   std::vector<Integer> primes;  
   boost::math::prime_reserve(upper, primes);  
   for(auto _ : state)  
    {  
        primes.clear();  
        do_linear_sieve_classical_bits(upper, primes);  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
template<class Integer>  
void linear_sieve_classical_bytes(benchmark::State& state)  
{  
   Integer upper = static_cast<Integer>(state.range(0));  
   std::vector<Integer> primes;  
   boost::math::prime_reserve(upper, primes);  
   for(auto _ : state)  
    {  
        primes.clear();  
        do_linear_sieve_classical_bytes(upper, primes);  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
template<class Integer>  
void linear_sieve_classical_bytes_oi(benchmark::State& state)  
{  
   Integer upper = static_cast<Integer>(state.range(0));  
   std::vector<Integer> primes;  
   boost::math::prime_reserve(upper, primes);  
   for(auto _ : state)  
    {  
        primes.clear();  
        benchmark::DoNotOptimize(do_linear_sieve_classical_bytes_oi(upper, std::back_inserter(primes)));  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
template <class Integer>  
struct trivial_factorizer  
{  
   Integer value;  
   trivial_factorizer(Integer i) : value(i) {}  
   trivial_factorizer& operator*() { return *this; }  
   trivial_factorizer& operator = (Integer i)  
   {  
      while (value % i == 0)  
         value /= i;  
      return *this;  
   }  
   trivial_factorizer& operator++() { return *this; }  
   trivial_factorizer& operator++(int) { return *this; }  
};  
  
template<class Integer>  
void linear_sieve_classical_bytes_factorizer_oi(benchmark::State& state)  
{  
    Integer upper = static_cast<Integer>(state.range(0));  
    for(auto _ : state)  
    {  
        benchmark::DoNotOptimize(do_linear_sieve_classical_bytes_oi(upper, trivial_factorizer<Integer>(std::numeric_limits<std::uint32_t>::max())));  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
template<class Integer>  
inline auto mask_sieve_helper(Integer lower_bound, Integer upper_bound, std::vector<Integer> primes) -> std::vector<Integer>  
{  
    boost::math::detail::mask_sieve(lower_bound, upper_bound, primes);  
    return primes;  
}  
  
template<class Integer>  
void mask_sieve(benchmark::State& state)  
{  
    Integer lower = static_cast<Integer>(2);  
    Integer upper = static_cast<Integer>(state.range(0));  
    for(auto _ : state)  
    {  
        std::vector<Integer> primes;  
        benchmark::DoNotOptimize(mask_sieve_helper(lower, upper, primes));  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
template<class Integer>  
inline auto interval_sieve_helper(Integer lower_bound, Integer upper_bound, std::vector<Integer> primes) -> std::vector<Integer>  
{  
    std::vector<Integer> pre_sieved_primes {2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71};  
    boost::math::detail::IntervalSieve sieve(lower_bound, upper_bound, pre_sieved_primes, primes);  
    return primes;  
}  
  
template<class Integer>  
void interval_sieve(benchmark::State& state)  
{  
    Integer lower = static_cast<Integer>(2);  
    Integer upper = static_cast<Integer>(state.range(0));  
    for(auto _ : state)  
    {  
        std::vector<Integer> primes;  
        benchmark::DoNotOptimize(interval_sieve_helper(lower, upper, primes));  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
// Complete Implementations  
template<class ExecuitionPolicy, class Integer, class Container>  
inline auto prime_sieve_helper(ExecuitionPolicy policy, Integer upper, Container primes)  
{  
    boost::math::prime_sieve(policy, upper, primes);  
    return primes;  
}  
  
template <class Integer>  
void prime_sieve_seq(benchmark::State& state)  
{  
    Integer upper = static_cast<Integer>(state.range(0));  
    std::vector<Integer> primes;  
    boost::math::prime_reserve(upper, primes);  
    for(auto _ : state)  
    {  
        primes.clear();  
        boost::math::prime_sieve(upper, primes);  
    }  
    state.SetComplexityN(state.range(0));  
}  
template<class Integer>  
inline auto prime_sieve_factorizer_helper(Integer upper, Integer value)  
{  
    std::vector<Integer> primes;  
    boost::math::prime_sieve(upper, primes);  
    for (unsigned i = 0; i < primes.size(); ++i)  
       while (value % primes[i] == 0)  
          value /= primes[i];  
    return value;  
}  
  
template <class Integer>  
void prime_sieve_factorizer_seq(benchmark::State& state)  
{  
    Integer upper = static_cast<Integer>(state.range(0));  
    for(auto _ : state)  
    {  
        benchmark::DoNotOptimize(prime_sieve_factorizer_helper(upper, std::numeric_limits<std::uint32_t>::max()));  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
template <class Integer>  
void kimwalish_primes(benchmark::State& state)  
{  
    Integer upper = static_cast<Integer>(state.range(0));  
    std::vector<Integer> primes;  
    for (auto _ : state)  
    {  
       primes.clear();  
       primesieve::generate_primes(upper, &primes);  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
template <class Integer, class I2>  
inline Integer kimwalish_prime_factorizer_helper(Integer upper, I2 value)  
{  
   std::vector<Integer> primes;  
   primesieve::generate_primes(upper, &primes);  
   for (unsigned i = 0; i < primes.size(); ++i)  
      while (value % primes[i] == 0)  
         value /= primes[i];  
   return value;  
}  
  
template <class Integer>  
void kimwalish_prime_factorizer(benchmark::State& state)  
{  
    Integer upper = static_cast<Integer>(state.range(0));  
    for (auto _ : state)  
    {  
        benchmark::DoNotOptimize(kimwalish_prime_factorizer_helper(upper, std::numeric_limits<std::uint32_t>::max()));  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
constexpr int low_range = 4;  
constexpr int high_range = 1 << 25;  
  
// Invidiual Implementations  
// Linear  
//BENCHMARK_TEMPLATE(linear_sieve, int32_t)->RangeMultiplier(2)->Range(1 << 1, 1 << 16)->Complexity(benchmark::oN);  
//BENCHMARK_TEMPLATE(linear_sieve, int64_t)->RangeMultiplier(2)->Range(1 << 1, 1 << 16)->Complexity(benchmark::oN);  
  
BENCHMARK_TEMPLATE(linear_sieve, uint32_t)->RangeMultiplier(2)->Range(low_range, high_range)->Complexity(benchmark::oN);  
BENCHMARK_TEMPLATE(linear_sieve_classical_bits, uint32_t)->RangeMultiplier(2)->Range(low_range, high_range)->Complexity(benchmark::oN);  
BENCHMARK_TEMPLATE(linear_sieve_classical_bytes, uint32_t)->RangeMultiplier(2)->Range(low_range, high_range)->Complexity(benchmark::oN);  
BENCHMARK_TEMPLATE(linear_sieve_classical_bytes_oi, uint32_t)->RangeMultiplier(2)->Range(low_range, high_range)->Complexity(benchmark::oN);  
BENCHMARK_TEMPLATE(linear_sieve_classical_bytes_factorizer_oi, uint32_t)->RangeMultiplier(2)->Range(low_range, high_range)->Complexity(benchmark::oN);  
  
// Segmented  
//BENCHMARK_TEMPLATE(mask_sieve, int32_t)->RangeMultiplier(2)->Range(1 << 2, 2 << 22)->Complexity(benchmark::oNLogN);  
//BENCHMARK_TEMPLATE(mask_sieve, int64_t)->RangeMultiplier(2)->Range(1 << 14, 2 << 26)->Complexity(benchmark::oNLogN);  
//BENCHMARK_TEMPLATE(interval_sieve, int64_t)->RangeMultiplier(2)->Range(1 << 14, 2 << 26)->Complexity();  
//BENCHMARK_TEMPLATE(mask_sieve, uint32_t)->RangeMultiplier(2)->Range(1 << 2, 2 << 22)->Complexity(benchmark::oNLogN);  
  
// Complete Implemenations  
//BENCHMARK_TEMPLATE(prime_sieve, int32_t)->RangeMultiplier(2)->Range(1 << 1, 1 << 30)->Complexity(benchmark::oN)->UseRealTime();  
//BENCHMARK_TEMPLATE(prime_sieve, int64_t)->RangeMultiplier(2)->Range(1 << 1, 1 << 30)->Complexity(benchmark::oN)->UseRealTime();  
BENCHMARK_TEMPLATE(kimwalish_primes, int64_t)->RangeMultiplier(2)->Range(low_range, high_range)->Complexity(benchmark::oN);  
BENCHMARK_TEMPLATE(kimwalish_prime_factorizer, int64_t)->RangeMultiplier(2)->Range(low_range, high_range)->Complexity(benchmark::oN);  
BENCHMARK_TEMPLATE(prime_sieve_seq, uint32_t)->RangeMultiplier(2)->Range(low_range, high_range)->Complexity(benchmark::oN);  
BENCHMARK_TEMPLATE(prime_sieve_factorizer_seq, uint32_t)->RangeMultiplier(2)->Range(low_range, high_range)->Complexity(benchmark::oN);  
//BENCHMARK_TEMPLATE(prime_sieve, boost::multiprecision::cpp_int)->RangeMultiplier(2)->Range(1 << 1, 1 << 30)->Complexity(benchmark::oN)->UseRealTime();  
//BENCHMARK_TEMPLATE(prime_sieve, boost::multiprecision::mpz_int)->RangeMultiplier(2)->Range(1 << 1, 1 << 30)->Complexity(benchmark::oN)->UseRealTime();  
  
BENCHMARK_MAIN();  
```

---

## Comment 130

> Username: ckormanyos  
> Created_at: 2020-09-15 17:43:55 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-692871501  

This thread is fascinating and I am veryhappy about it.  
  
> But importantly, that temporary buffer (at least of a classic sieve) can be a bitset, it doesn't have to store the actual integer values.  
  
This is true. The use of a bitset or (maybe even better) a container of unsigned integers storing a bitstream *could* be a good idea to save internal storage in the sieves. This storage method will, however, come at a performance cost.  
  
This brings another idea to mind. In one of the examples of my other project, I calculate 100 primes in a sieve on an 8-bit microcontroller having merely 2 kilobytes of RAM in total. In this project, every bit counted. So you see here the [custom bitset for sieve storage](https://github.com/ckormanyos/real-time-cpp/blob/7aa50340b38599be095a148a2d887e52beab001d/examples/chapter03_02/src/math/primes/primes.h#L87)  
  
That line hints at something potentially interesting for this Boost work: Maybe consider to provide as template parameters either a container type and/or its allocator type for internal sieve storage of Boolean flags.

---

## Comment 131

> Username: mborland  
> Created_at: 2020-09-15 18:52:15 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-692907179  

@jzmaddock Those results are very interesting. Thank you. Now that there are fixed and variable length wheels (`prime_wheel.hpp`) I will have to try and see how the classical sieve does with an integrated wheel. If I remember correctly the computation of the wheel is the part of the reason for the slow start with KW. To avoid that `MOD210Wheel`, which is the most common wheel size, is entirely static constexpr.  
  
As for a segmented sieve using an output iterator that may be easy to adapt from `IntervalSieve`. `IntervalSeive` uses a `boost::dynamic_bitset` internally and then emplaces the resultant primes into the provided container (`WriteOutput`).   
  
@ckormanyos [This comparison of bit vector implementations](https://www.researchgate.net/publication/220803585_Performance_of_C_bit-vector_implementations) may be of interest to you. While a few years old the results were not far off and helped led me to choose `boost::dynamic_bitset`. A custom container could be worth researching.

---

## Comment 132

> Username: jzmaddock  
> Created_at: 2020-09-27 10:46:43 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-699618572  

Morning @mborland : thanks for the updated code - and for plugging away at this!  
  
I've also been experimenting, there's some strictly experimental code here: https://github.com/boostorg/math/pull/438 which has dual OutputIterator/Container interfaces.  Internally it's just a segmented Sieve of Eratosthenes, nothing sophisticated!  
  
I've just run a quick performance comparison against your latest code (and I apologise in advance if you're still working on it, but I'm away all next week so it's now or never!), here's what I see with msvc:  
  
  
  | KW | MB/OI | JM | JM/OI  
-- | -- | -- | -- | --  
4 | 4.2684766214 | 1.4841628959 | 1.3574660633 | 1.1040723982  
8 | 3.6897001304 | 1.5645371578 | 1.0234680574 | 1  
16 | 2.6607142857 | 1.5267857143 | 0.8223214286 | 0.7848214286  
32 | 2.0551724138 | 1.6137931034 | 0.8275862069 | 0.8482758621  
64 | 1.3063829787 | 1.4042553191 | 0.6382978723 | 0.6127659574  
128 | 0.8259803922 | 1.3406862745 | 0.5147058824 | 0.5220588235  
256 | 0.5226666667 | 1.3253333333 | 0.44 | 0.4693333333  
512 | 0.2936408978 | 1.0218204489 | 0.3703241895 | 0.3703241895  
1024 | 0.5456198848 | 1.0084874204 | 0.3297969082 | 0.3331312519  
2048 | 0.3360215054 | 0.7934587814 | 0.3249701314 | 0.3281063321  
4096 | 0.2500181015 | 0.828325248 | 0.3636231989 | 0.37332561  
8192 | 0.176772979 | 0.8585598957 | 0.4848495819 | 0.4886145603  
16384 | 0.1500071679 | 0.9240018637 | 0.5106264784 | 0.56250448  
32768 | 0.1229776056 | 0.8571034237 | 0.5147856284 | 0.546582966  
65536 | 0.1092082448 | 0.800070087 | 0.5222207558 | 0.5587296964  
131072 | 0.0990697188 | 0.7638390184 | 0.5240094475 | 0.5962726421  
262144 | 0.0969190832 | 0.7307688066 | 0.568615407 | 0.5127491156  
524288 | 0.0874159869 | 0.7660186505 | 0.5695667884 | 0.4766336582  
1048576 | 0.0765253902 | 0.742849556 | 0.4742754061 | 0.4968600205  
2097152 | 0.0751164115 | 0.8035714755 | 0.4498953245 | 0.5380434857  
4194304 | 0.0767045443 | 0.9131493467 | 0.4772727421 | 0.5454545157  
8388608 | 0.077000867 | 0.8770763948 | 0.4809302283 | 0.5615428117  
16777216 | 0.0714285748 | 1.0444444427 | 0.4685185204 | 0.537777774  
33554432 | 0.071544718 | 1.5853658537 | 0.4545454517 | 0.5420054166  
  
I scored your `prime_sieve` 1 in each row, everything else is relative to that.  
  
Kin Walch's code obviously shoots off into 1st place once it get's going, but it is multithreaded under the hood and the others are all single threaded.  Your OutputIterator version (MB/OI column) starts off showing good promise, but then starts to lag behind.  My OutputIterator code (JM/OI) takes a small hit when the algorithm switches to the segmented sieve, but in the long run the difference from the container based code seems to be small.  I confess though I've only tested up to 2^32.  
  
Some other general comments:  
  
* Your `prime_reserve` actually under-estimates things slightly so there may be 2 memory allocations instead of one.  You may also over-allocate on the second one, depending on the vector's growth strategy.  This gets it slightly better: https://github.com/boostorg/math/pull/438/files#diff-970063e9520d88b17a6a061e4ee5a2d4R298-R324.  
* With a bitmask sieve, and sieving only odd numbers, you can sieve 16 values per byte, so the segment size in my code is rather large - up to 128K per segment, each occupying 32K bytes (which fit's in my cache, no idea how we make this portable?).  
* I used a custom bitmask for sieving - it's noticeably better than `vector<bool>`, but not so much better (or at all?) than `dynamic_bitset`.  It is crazy simple though.  
* Wheel sieves, should be the speed demon's here, so your code is the way to go, but appears still to have some room for improvement compared to the classical sieve?  
* Sieve of Atkin and other "modern" sieves, while theoretically better, in practice seem to have a reputation for being slower in practice?  
* I haven't double checked with your latest code, but there was a fencepost-type error somewhere in your sieve as `prime_sieve(4100, my_container)` failed to find 4099.  
* Once you step outside of anything trivial, this code is remarkably hard to get right - even the classical SoE seems to acquire new bugs every time I change something, so you have my sympathies!  
* I had hoped to have an experimental multi-threaded framework up and running by now, but sadly not to be, hopefully when I get back in a weeks time I'll be able to continue experimenting.  
  
I'm sure there's lot's I've forgotten, but for now I must dash as the real world calls....

---

## Comment 133

> Username: jzmaddock  
> Created_at: 2020-09-27 11:44:12 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-699624502  

>With a bitmask sieve, and sieving only odd numbers, you can sieve 16 values per byte, so the segment size in my code is rather large - up to 128K per segment, each occupying 32K bytes (which fit's in my cache, no idea how we make this portable?).  
  
Ugh, I should have said, 32K sieve, with 16 values per byte = 512K segment size.  
  
@jeremy-murphy : there is still a good reason for container-based interfaces: _if_ a vector of primes is what you really want, then using the output vector as working storage saves a huge amount of memory.  In this context the performance tests are somewhat false, because you wouldn't (or at least should not) use a `back_inserter` to create a vector of primes - in fact that particular use case could probably be `static_assert`'ed against with a helpful error message.  On the other hand, if you don't need to store the primes (or at least not all of them), then a small overhead on the OutputIterator interface is largely irrelevant - especially if the slower benchmarks are mostly down to increased memory consumption.  I should probably find a way to test that.

---

## Comment 134

> Username: mborland  
> Created_at: 2020-09-27 15:50:02 UTC  
> Updated_at: 2020-09-29 16:46:24 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-699652037  

@jzmaddock I appreciate the analysis. I looked through your PR and like your linear sieve. I had been using one for only the first 4096 primes because linear sieves do not lend themselves well to threading. After that the segmented wheel sieve should take off like you said. To make the segmented sieve compatible with using an OI I am removing passing the container of pre-sieved primes in. I was trying to remove any duplicate calculations with that but in reality you really do not need much to seed it and primes up to 353 are already in the psuedo-squares table. I had been using a local branch for development but I can use this one for better visibility.  
  
- @jeremy-murphy also created a PR on my branch and provided a good prime approximation function. I can make it constepxr using libstdc++ but I am not sure if MSVC has log and floor implemented as constexpr because the standard does not require it. Edit: CI shows that in all cases it is constexpr  
```  
template<typename Integer>  
constexpr Integer prime_approximation(const Integer upper_bound)  
{  
    constexpr auto c = 30 * ::log(113) / 113; // Magic numbers from wikipedia  
    return static_cast<Integer>(::floor(c * upper_bound / ::log(static_cast<double>(upper_bound))));  
}  
```  
  
- I will give your bitmask implementation a shot and see how it compares with `boost::dynamic_bitset`. I have not directly measured the performance of each of the containers but it may be worthwhile to write a simple benchmark and submit an update to [this paper](https://www.researchgate.net/publication/220803585_Performance_of_C_bit-vector_implementations) since it is a decade old now.  
  
- In some of the old commits you will find pritchard's sieves which are theoretically among the best but in practice they did not perform like I would have hoped. They treat all operations equally but use modulo and remove values from the middle of containers extensively which I suspect is why the performance was not there.  
  
- I have the L1 cache size as a template constant because I was not sure either how to query it in a portable way. [This SO discussion](https://stackoverflow.com/questions/39680206/understanding-stdhardware-destructive-interference-size-and-stdhardware-cons) seems to have the answer, but I have not tried it. Intel's Ice Lake line has 48kb L1D so I probably should figure this out.

---

## Comment 135

> Username: mborland  
> Created_at: 2020-10-01 00:28:05 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-701715713  

@jzmaddock With this latest commit all of the framework is functioning. You should now be able to plug and play algorithms pretty easily. To handle data races each tread has a vector to store the primes in its range. The futures are returned, and potentially blocked in order to ensure that the final output is sorted.

---

## Comment 136

> Username: mborland  
> Created_at: 2020-10-02 01:42:31 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-702482717  

Here is a set of benchmarks comparing the original container based sieve with the newly complete output iterator based sieve:  
  
```  
Run on (12 X 4500 MHz CPU s)  
CPU Caches:  
  L1 Data 32 KiB (x6)  
  L1 Instruction 32 KiB (x6)  
  L2 Unified 256 KiB (x6)  
  L3 Unified 12288 KiB (x1)  
Load Average: 0.52, 0.69, 0.69  
---------------------------------------------------------------------------------------  
Benchmark                                             Time             CPU   Iterations  
---------------------------------------------------------------------------------------  
prime_sieve<int64_t>/2/real_time                   2.03 ns         2.02 ns    342490519  
prime_sieve<int64_t>/4/real_time                   41.2 ns         41.2 ns     16447980  
prime_sieve<int64_t>/8/real_time                   65.0 ns         64.9 ns     10630962  
prime_sieve<int64_t>/16/real_time                  96.4 ns         96.3 ns      7143866  
prime_sieve<int64_t>/32/real_time                   159 ns          158 ns      4388946  
prime_sieve<int64_t>/64/real_time                   238 ns          238 ns      2931989  
prime_sieve<int64_t>/128/real_time                  376 ns          376 ns      1883032  
prime_sieve<int64_t>/256/real_time                  668 ns          668 ns      1040072  
prime_sieve<int64_t>/512/real_time                 1305 ns         1304 ns       533253  
prime_sieve<int64_t>/1024/real_time                2905 ns         2903 ns       238961  
prime_sieve<int64_t>/2048/real_time                5828 ns         5823 ns       113918  
prime_sieve<int64_t>/4096/real_time               12128 ns        12119 ns        57577  
prime_sieve<int64_t>/8192/real_time               39413 ns        14480 ns        17716  
prime_sieve<int64_t>/16384/real_time              56881 ns        17358 ns        12276  
prime_sieve<int64_t>/32768/real_time             107125 ns        17891 ns         6552  
prime_sieve<int64_t>/65536/real_time             205661 ns        18813 ns         3388  
prime_sieve<int64_t>/131072/real_time            408244 ns        21392 ns         1729  
prime_sieve<int64_t>/262144/real_time            810349 ns        28117 ns          860  
prime_sieve<int64_t>/524288/real_time           1014861 ns        65912 ns          671  
prime_sieve<int64_t>/1048576/real_time          1700894 ns       127048 ns          405  
prime_sieve<int64_t>/2097152/real_time          2576707 ns       226808 ns          273  
prime_sieve<int64_t>/4194304/real_time          4741766 ns       382234 ns          139  
prime_sieve<int64_t>/8388608/real_time          8679737 ns       793298 ns           80  
prime_sieve<int64_t>/16777216/real_time        16565028 ns      1714545 ns           41  
prime_sieve<int64_t>/33554432/real_time        31602258 ns      2902396 ns           22  
prime_sieve<int64_t>/67108864/real_time        62848972 ns      3405550 ns           11  
prime_sieve<int64_t>/134217728/real_time      127677660 ns      6905033 ns            6  
prime_sieve<int64_t>/268435456/real_time      252555637 ns     12751858 ns            3  
prime_sieve<int64_t>/536870912/real_time      494142191 ns     22623342 ns            2  
prime_sieve<int64_t>/1073741824/real_time     990409989 ns     43851009 ns            1  
prime_sieve<int64_t>/real_time_BigO                0.92 N          0.04 N      
prime_sieve<int64_t>/real_time_RMS                    2 %            17 %      
  
prime_sieve_oi<int64_t>/2/real_time               0.449 ns        0.449 ns   1000000000  
prime_sieve_oi<int64_t>/4/real_time                12.7 ns         12.7 ns     55120459  
prime_sieve_oi<int64_t>/8/real_time                15.3 ns         15.3 ns     44447710  
prime_sieve_oi<int64_t>/16/real_time               18.0 ns         18.0 ns     38908419  
prime_sieve_oi<int64_t>/32/real_time               26.6 ns         26.5 ns     26347858  
prime_sieve_oi<int64_t>/64/real_time               42.2 ns         42.2 ns     16593211  
prime_sieve_oi<int64_t>/128/real_time              70.1 ns         70.1 ns      9797867  
prime_sieve_oi<int64_t>/256/real_time               136 ns          136 ns      5155269  
prime_sieve_oi<int64_t>/512/real_time               268 ns          268 ns      2624387  
prime_sieve_oi<int64_t>/1024/real_time              580 ns          579 ns      1151391  
prime_sieve_oi<int64_t>/2048/real_time             1202 ns         1201 ns       564843  
prime_sieve_oi<int64_t>/4096/real_time             2369 ns         2367 ns       290710  
prime_sieve_oi<int64_t>/8192/real_time           124666 ns       122854 ns         5464  
prime_sieve_oi<int64_t>/16384/real_time          129749 ns       128021 ns         5534  
prime_sieve_oi<int64_t>/32768/real_time          138206 ns       135886 ns         5041  
prime_sieve_oi<int64_t>/65536/real_time          139101 ns       136746 ns         5019  
prime_sieve_oi<int64_t>/131072/real_time         174366 ns       163837 ns         4039  
prime_sieve_oi<int64_t>/262144/real_time         226633 ns       179921 ns         3093  
prime_sieve_oi<int64_t>/524288/real_time         358367 ns       204135 ns         1951  
prime_sieve_oi<int64_t>/1048576/real_time        608103 ns       237185 ns         1093  
prime_sieve_oi<int64_t>/2097152/real_time       1113854 ns       289575 ns          580  
prime_sieve_oi<int64_t>/4194304/real_time       2131399 ns       383375 ns          327  
prime_sieve_oi<int64_t>/8388608/real_time       4282676 ns       651338 ns          164  
prime_sieve_oi<int64_t>/16777216/real_time      8776750 ns      1469589 ns           79  
prime_sieve_oi<int64_t>/33554432/real_time     17906567 ns      3163761 ns           39  
prime_sieve_oi<int64_t>/67108864/real_time     43112895 ns     15656060 ns           16  
prime_sieve_oi<int64_t>/134217728/real_time    86521186 ns     29236241 ns            8  
prime_sieve_oi<int64_t>/268435456/real_time   176181813 ns     55391648 ns            4  
prime_sieve_oi<int64_t>/536870912/real_time   357701007 ns    106635183 ns            2  
prime_sieve_oi<int64_t>/1073741824/real_time  721644354 ns    204384534 ns            1  
prime_sieve_oi<int64_t>/real_time_BigO             0.67 N          0.19 N      
prime_sieve_oi<int64_t>/real_time_RMS                 3 %            10 %  
```  
  
I expect that the reason why the multi-threaded performance drops even with the increase in single-thread performance is compiling the results. The container based method used `vector::insert` to compile buffers into the container of resultant primes. The OI approach moves every value individually (lines 84-95 of `prime_sieve_iter.hpp`). Any insight into how to do this more efficiently would be appreciated.

---

## Comment 137

> Username: jzmaddock  
> Created_at: 2020-10-02 17:34:49 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-702864439  

Thanks for the update - I really need to get to grips with wheel sieves - frankly I haven't got my head around them at present, but now that I'm home and have internet access again I'll see if I can dust off the old brain cells and get to grips with the things ;)  
  
What I did do while away, was experiment with multi-threading the basic linear sieve.  The (segmented) sieve is actually quite easy to execute in parallel, since most of the work is done in striking out the smaller primes which are all calculated in the first segment anyway.  You only need to wait for the preceding segment to be completed for the first few segments as you don't consider primes > `sqrt(segment_end)` anyway.  BUT, as you say, collating the results is a major bottleneck, and as a result the performance doesn't scale well with the number of cores.  
  
I also notice that starting too many threads may actually make performance worse - on my laptop for example, `std::thread::hardware_concurrency()` reports 8, but there are only really 4 true cores, which are each hyper-threaded.  So the best performance came from 4x parallelism, not 8.  
  
One other thing, I really don't understand google benchmark results in the multithreaded case at all - I've been getting some pretty screwy results, including sporadic 0's for the time taken.  As a result, I took to sanity checking performance by measuring real-time taken with <chrono>, and the results looked completely different.  With your latest code, and mine pulled into https://github.com/boostorg/math/pull/438  here's what I see in terms of real time (seconds) to calculate primes up to 1<<32:  
  
|test|time|  
|-----|------|  
|JM threaded|4.8|  
|JM seq|7.9|  
|MB threaded| 7.6|  
|MB seq|19.9|  
|KW seq|1.8|  
  
So you're getting a better threaded speedup than me - but take a look at Kim Walch's code, it's a good 10x faster than anything we have and uses a single thread (I checked!!).  There is a lot of of quite low level jumps (goto's) and bit fiddling going on, but even so, it's clear we're missing a lot here.

---

## Comment 138

> Username: mborland  
> Created_at: 2020-10-02 18:28:09 UTC  
> Updated_at: 2020-10-02 20:23:21 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-702890366  

[Here is the paper](https://minds.wisconsin.edu/bitstream/handle/1793/59248/TR909.pdf?sequence=1) the brought me up to speed on wheels. If you roll back to commit b233a80 you will find direct (and likely naive) implementations of the wheel, Pritchard's sub-linear algorithm, and Pritchard's linear segmented algorithm lifted directly out of that paper.  
  
Since I have clearly been thinking about caching recently I have considered the impact of hyper-threading. I still think my segments need to change based on the type too. In previous results you can see performance takes a giant hit when using arbitrary precision types.  
  
That's interesting that you are having issues with the threaded benchmarks. I have not seen that. My current OI implementation is pretty naively threaded in comparison to the original container version. You can see on the benchmarks above the performance in the container version is much more linear. KW definitely has some impressive optimizations and part of that would come if we constrain ourselves to int64_t like he does. One suggestion that I received from the author of the linked paper was using arithmetic progressions instead of segments [Comments mine]:  
  
Right now I'm doing a computation for Kevin McGown on primitive roots, and for that I'm finding all primes p, along with the factorization of p-1, for primes p up to 10^16.  This is on a small cluster using MPI, rather than threads on a single CPU.  
  
To get linear time, you need a wheel, and for this computation, since I don't care if the primes come in order or not, instead of sieving segments, I sieve arithmetic progressions of the form a+km, where m is the product of the primes up to 29 I believe, and then the a's are the integers relatively prime to m.  Well, in addition to sieving the progression a+km, I also sieve (a-1)+km and store complete factorizations, which I need for the primitive root computations. [When asked if this would translate to a sieve] Given that each arithmetic progression would already be sorted, a mergesort might be rather efficient.  Something like O( (log phi(m)) n/log n), which should be sublinear in n.  
  
IIRC, Kim's program uses a fixed wheel, probably with 2 * 3 * 5 [Mod 30, `prime_wheel` is Mod 210]. If the wheel doesn't grow with N, then you can't get below the O(N log log N) time.  Since log log N is so very small anyway, the fixed wheel might be a win in practice.  However, if you're able to sieve arithmetic progressions instead of segments, then you can use a much larger wheel.

---

## Comment 139

> Username: mborland  
> Created_at: 2020-10-05 03:08:11 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-703372145  

@jzmaddock I was going through your PR and saw the dual interface, so decided to see how a trivial wrapper around the original `prime_sieve` would compare to `prime_sieve_oi`. The gist of the results:  
```  
--------------------------------------------------------------------------------------------  
Benchmark                                                  Time             CPU   Iterations  
--------------------------------------------------------------------------------------------  
  
prime_sieve_wrapper<int64_t>/real_time_BigO             0.79 N          0.08 N      
prime_sieve_wrapper<int64_t>/real_time_RMS                 2 %            13 %   
  
prime_sieve_oi<int64_t>/real_time_BigO                  0.70 N          0.21 N      
prime_sieve_oi<int64_t>/real_time_RMS                      5 %            14 %   
  
kimwalish_primes<int64_t>/real_time_BigO                0.32 N          0.32 N      
kimwalish_primes<int64_t>/real_time_RMS                    6 %             6 %  
```  
  
Building a dual interface around the existing sieve seems like a better choice on my end. Being able to just move compiled results to the output iterator really helps threaded performance.

---

## Comment 140

> Username: mborland  
> Created_at: 2020-10-07 04:22:40 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-704682318  

The dual interface for `prime_sieve` is complete, and passes all existing unit tests. There is now the dual interface provided by `prime_sieve` (formerly `prime_sieve_wrapper` for OI performance data above), and the dedicated design for output iterators in `prime_sieve_oi`. I think either design meets the intent of being able to offer a generic interface. From here what is the way forward? Are there specific performance goals, or other acceptance criteria?

---

## Comment 141

> Username: jeremy-murphy  
> Created_at: 2020-10-09 06:12:30 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-705990089  

> @jeremy-murphy : there is still a good reason for container-based interfaces: _if_ a vector of primes is what you really want, then using the output vector as working storage saves a huge amount of memory. In this context the performance tests are somewhat false, because you wouldn't (or at least should not) use a `back_inserter` to create a vector of primes - in fact that particular use case could probably be `static_assert`'ed against with a helpful error message. On the other hand, if you don't need to store the primes (or at least not all of them), then a small overhead on the OutputIterator interface is largely irrelevant - especially if the slower benchmarks are mostly down to increased memory consumption. I should probably find a way to test that.  
  
Maybe I'm missing something, so can we focus on a specific function? Which one benefits from the container interface?  
I'll try to respond quickly.

---

## Comment 142

> Username: jeremy-murphy  
> Created_at: 2020-10-09 09:37:01 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-706079043  

Something occurred to me, unrelated to this container vs iterator topic, and maybe it's a dumb idea, but for an algorithm that produces a sequence of prime numbers, wouldn't it be more efficient in terms of output space to apply basic delta encoding, i.e. output the prime _gaps_ rather than the primes themselves? I.e., if the user just needs to test one prime after another for some property, then they don't need the whole sequence of primes in memory at once. And the point being, the gaps are much, much smaller in size than the primes, so for 128-bit integer prime numbers, we would be producing 16-bit or 32-bit prime gaps.

---

## Comment 143

> Username: mborland  
> Created_at: 2020-10-09 13:52:19 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-706193560  

@jeremy-murphy that is effectively how the wheel works; the mod 210 wheel can remove ~90% of composites using a fixed length array representing gaps. It can and should be used for efficient factorization if that is what you're getting at.

---

## Review 144 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-10-09 23:56:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-506041340  

📁 include/boost/math/special_functions/detail/interval_prime_sieve.hpp

```diff
 112 |+     if(tdest <= 1ul<<30)
 113 |+     {
 114 |+         tdlimit_ = static_cast<std::size_t>(tdest);
```

> Username: jeremy-murphy  
> Created_at: 2020-10-09 23:56:02 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r502716310  

As a complete aside, but are all these `static_cast`s really necessary? I know there are some tricky rules about conversions sometimes, but I just didn't imagine it was necessary to be _explicit_ so often. If the implicit cast is correct, it makes for much easier (less noisy) code to read.

> Username: mborland  
> Created_at: 2020-10-10 00:08:30 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r502718286  

Unfortunately they are. I do not claim to know what is going on under the hood of `multiprecision::gmp_int` but it does not seem to implicitly cast. Almost every explicit cast is to fix a compiler error for `gmp_int`.

> Username: pabristow  
> Created_at: 2020-10-10 09:00:25 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r502766949  

They are code clutter, but serve to document conversions that probably lose precision, saying "OK - I don't care."  
  
Implicit conversion is a big pit with spikes at the bottom waiting to catch the unwary and is a major cause of very-difficult-to-trace loss of precision using Boost.Multiprecision.  
  
(And I have fallen into it too many times... ouch!)

> Username: jeremy-murphy  
> Created_at: 2020-10-11 23:25:45 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r502980457  

Aha, yeah, OK, thanks.


---

## Review 145 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-10-10 00:08:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-506043242  

📁 include/boost/math/special_functions/detail/interval_prime_sieve.hpp

```diff
  79 |+     
  80 |+     std::vector<Integer> primes_;
  81 |+     std::int_fast64_t plimit_;
```

> Username: jeremy-murphy  
> Created_at: 2020-10-10 00:08:02 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r502718219  

I'm a little bit confused -- why is there both a bitset `b_` of the prime range, _and_ a vector of prime numbers, `primes`?  
Is there a duplication of information here?

> Username: mborland  
> Created_at: 2020-10-10 00:12:11 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r502718924  

The bitset `b_` is used to represent the numbers between `left_` and `right_`. `primes_` is a small number of primes that are used to seed the sieve (Lines 172-202).


---

## Review 146 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-10-10 00:28:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-506046120  

📁 include/boost/math/special_functions/detail/interval_prime_sieve.hpp

```diff
 214 |+     }
 215 |+     return resultant_primes_;
 216 |+ }
```

> Username: jeremy-murphy  
> Created_at: 2020-10-10 00:28:41 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r502721236  

Hmmm, I can see, you're trying to maintain a consistent interface, right? But I think it's pushing you into an awkward design corner.  
I would treat the output of `IntervalSieve` in the same way as Stepanov's `sift` function: the bitset `b_` _is_ the output. Don't process it any further: it has _all_ the information. The user can get prime number values from it with a trivial constant-time operation. Remember, the user provides the value of `left_` in the first place.  
  
I suspect, although I could be wrong, that the vector `primes_` could be removed completely if the linear sieve wrote directly to the bitset? Ah, I just realized that the container version in another file takes these low primes in pre-calculated? Not sure why there is a design difference there, but doesn't matter too much.  
  
I think you can cut through the whole output iterator vs container argument by just making the bitset be the output.  
I'm not sure if that holds for the more advanced algorithms, I'll look at them next.

> Username: mborland  
> Created_at: 2020-10-10 00:44:24 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r502723139  

You would still need the vector `primes_`. The intervals only represent a range that can be fit into L1 not the entire range from the user. In order to do this you need to recursively pass a reference to the computed primes which is exactly what you see in the container version. In the OI version you don't have the capability to do that so it maintains the vector `primes_` of only exactly what it needs to account for that shortfall. It also resets the bitset every range so the bitset has to get written to a buffer anyway; seems reasonable to me to give the actual underlying value instead of a boolean.

> Username: jeremy-murphy  
> Created_at: 2020-10-10 07:27:45 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r502758846  

So, with `primes_`, as I said in another comment, it's not strictly a container, it's just an iterator range, so don't conflate it with the output iterator vs output container issue. It's only when `emplace_back()` and `resize()`, etc appear that something really has to be a container. So, I hope we can remove that difference between the two `IntervalSieve`s.  
  
Well, if the bitset is the output, that removes the last difference between the two `IntervalSieve`s, right? Because all the output iterator/container stuff gets removed. So that's a win for code simplicity.  
  
The other reason is also about general design simplicity, and in particular I mean the S in SOLID:  single responsibility.  
What does `IntervalSieve` do currently? It sieves primes in a bitset AND then transforms that bitset into a sequence of primes. That's two responsibilities: g(f(x)) (or g ∘ f), where g is the transform. Yes, at the moment you 'know' that you want the prime number sequence from the sieve, but, I guess part of the point of separating the two operations is that it gives you the freedom to change your mind. You might discover that in one case you need g(y), but in another it's actually h(y). Anyway, that's enough on that. I think my first reason is more important.

> Username: mborland  
> Created_at: 2020-10-10 15:35:09 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r502802562  

The two `IntervalSieve`s need not be reconciled. There are currently two complete, and totally discrete implementations of the entire prime sieve with different interfaces: one with the dual interface and one strictly OI. You are now suggesting a different interface where the user receives boolean values. There is no function in the sieve that currently does that so once again all the methods and handling would need retooled. I am uninterested in making any more design or interface changes without clear consensus and direction. I do not know if this a question for the 4 reviewers (you, @NAThompson, @pabristow, and @jzmaddock), or if it needs to be taken to the mailing list to be resolved. This entire PR has been, and will continue to be stagnant until the interface question is resolved.


---

## Review 147 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-10-10 00:40:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-506047465  

📁 include/boost/math/special_functions/interval_sieve.hpp

```diff
  27 |+ }
  28 |+ 
  29 |+ template<class Integer, class PrimeContainer, class Container>
```

> Username: jeremy-murphy  
> Created_at: 2020-10-10 00:40:37 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r502722734  

So you understand the difference, what you have called `PrimeContainer` is _not_ actually a container. Or more precisely, the concrete type that instantiates here does not have to model all the interface of the _Container_ concept.  
`primes_` just has to model a `RandomAccessRange`, i.e. it has `begin()`, `end()`, `size()` and `T& operator[](std::size_t i)`. A container can model that, but a pair of iterators, or an iterator-size pair can too.  
So, to be precise, that template parameter is not a (Prime)Container, it's a (Prime)RandomAccessRange.


---

## Comment 148

> Username: jzmaddock  
> Created_at: 2020-10-19 18:19:05 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-712356801  

Apologies for delay in getting back to you....  
  
Acceptance criteria, not sure yet, good flexible design and performance that's not too embarrassing... I don't think we're there yet, though I confess I have nothing better to offer :(  
  
On performance, I think google bench is not measuring what you think here, I've taken to measuring wall clock time via <chrono> like this:  
  
```  
#include <climits>  
#include <boost/math/special_functions/prime_sieve_jm.hpp>  
#include <boost/math/special_functions/prime_sieve.hpp>  
#include <chrono>  
#include <primesieve.hpp>  
  
template <class Clock>  
struct stopwatch  
{  
   typedef typename Clock::duration duration;  
   stopwatch()  
   {  
      m_start = Clock::now();  
   }  
   double elapsed()  
   {  
      return std::chrono::duration_cast<std::chrono::duration<double>>(Clock::now() - m_start).count();  
   }  
   void reset()  
   {  
      m_start = Clock::now();  
   }  
  
private:  
   typename Clock::time_point m_start;  
};  
  
int main()  
{  
   std::vector<std::uint64_t> primes;  
  
   std::uint64_t limit = 4294967296;  
  
   stopwatch<std::chrono::high_resolution_clock> w;  
   primesieve::generate_primes(limit, &primes);  
     
   primes.clear();  
   w.reset();  
   jm::prime_sieve(limit, primes);  
   double t = w.elapsed();  
   std::cout << "jm::prime_sieve(seq):";  
   std::cout << "time =            " << t << std::endl;  
   std::cout << "size/last value = " << primes.size() << " " << primes.back() << std::endl;  
  
   primes.clear();  
   w.reset();  
   jm::prime_sieve(std::execution::par, limit, primes);  
   t = w.elapsed();  
   std::cout << "jm::prime_sieve(par):";  
   std::cout << "time =            " << t << std::endl;  
   std::cout << "size/last value = " << primes.size() << " " << primes.back() << std::endl;  
  
   primes.clear();  
   w.reset();  
   boost::math::prime_sieve(std::execution::seq, limit, &primes);  
   t = w.elapsed();  
   std::cout << "prime_sieve(seq):";  
   std::cout << "time =            " << t << std::endl;  
   std::cout << "size/last value = " << primes.size() << " " << primes.back() << std::endl;  
  
   primes.clear();  
   w.reset();  
   boost::math::prime_sieve(std::execution::par, limit, &primes);  
   t = w.elapsed();  
   std::cout << "prime_sieve(par):";  
   std::cout << "time =            " << t << std::endl;  
   std::cout << "size/last value = " << primes.size() << " " << primes.back() << std::endl;  
  
   primes.clear();  
   w.reset();  
   primesieve::generate_primes(limit, &primes);  
   t = w.elapsed();  
   std::cout << "KW(seq):";  
   std::cout << "time =            " << t << std::endl;  
   std::cout << "size/last value = " << primes.size() << " " << primes.back() << std::endl;  
  
}  
```  
  
And here's what I see for output with mingw:  
  
```  
jm::prime_sieve(seq):time =            9.74036  
size/last value = 203280221 4294967291  
jm::prime_sieve(par):time =            7.20429  
size/last value = 203280221 4294967291  
prime_sieve(seq):time =            21.2991  
size/last value = 203280221 4294967291  
prime_sieve(par):time =            16.7777  
size/last value = 203280221 4294967291  
KW(seq):time =            1.23015  
size/last value = 203280221 4294967291  
```  
  
Note the relatively low parallel speed up, and how quick Kim Walch's code is.  
  
BTW there was a bug in the previous version of my experimental sieve which made it 10x slower on gcc than msvc - bit access is a **very** hot code path.  
  
I have a few ideas for further improvements (which would be applicable to your wheel sieves as well), but haven't had a chance to try them yet.  
  
@jeremy-murphy :  
>I would treat the output of IntervalSieve in the same way as Stepanov's sift function: the bitset b_ is the output.  
  
I'm not so sure, extracting the primes from the sieve is actually one area where there's great opportunities for performance improvements based on the observation that most of the bits are zero.  More on that soon I hope.  I do note however, that the bitmask should occupy less memory than a list of primes which surprised me.

---

## Comment 149

> Username: jeremy-murphy  
> Created_at: 2020-10-19 23:57:17 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-712504698  

On the topic of performance of concurrent systems in general, this recent plenary talk from CppCon is _great_: https://www.youtube.com/watch?v=koTf7u0v41o&amp;feature=emb_logo&amp;ab_channel=CppCon  
  
TL;DR: Conventional performance benchmarks can be super misleading for optimizing code, instead try profiling with Coz: https://github.com/plasma-umass/coz

---

## Comment 150

> Username: mborland  
> Created_at: 2020-10-23 02:45:18 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-714872934  

@jzmaddock No worries about the delay. I am in the midst of moving so I will likely be on here sporadically the next few weeks.  
  
I will have to use your stopwatch. The benchmark should have been setup to give wall clock like results, but thats clearly not what is going on. I will keep an eye out for other improvements you have.  
  
@jeremy-murphy I will have to give that a watch; the library looks interesting.

---

## Comment 151

> Username: mborland  
> Created_at: 2020-11-22 10:42:21 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-731728291  

Latest results show some improvement:  
```  
-----------------------------------------------------------------------------------------  
Benchmark                                               Time             CPU   Iterations  
-----------------------------------------------------------------------------------------  
prime_sieve_oi<int64_t>/real_time_BigO               0.58 N          0.20 N      
prime_sieve_oi<int64_t>/real_time_RMS                   6 %            15 %  
kimwalish_primes<int64_t>/real_time_BigO             0.31 N          0.31 N      
kimwalish_primes<int64_t>/real_time_RMS                 6 %             6 %  
```  
  
Dummy checking with the stopwatch shows close agreement with `prime_sieve_oi` taking ~1.84x kim's runtime.

---

## Comment 152

> Username: jzmaddock  
> Created_at: 2020-11-22 12:49:11 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-731743074  

Thanks Matt, I'll try and take a look later.  
  
I haven't posted any code but I have been working on a suite of optimisations, here's my current performance results:  
  
size|old/new|KW/new  
-- | -- | --  
linear_new/2 | 6.2 | 38.8888888889  
linear_new/4 | 6.2845445241 | 36.9498464688  
linear_new/8 | 5.16 | 28.88  
linear_new/16 | 5.2291666667 | 25.5555555556  
linear_new/32 | 3.9174311927 | 17.5229357798  
linear_new/64 | 2.8301886792 | 9.5990566038  
linear_new/128 | 3.6380255941 | 7.5137111517  
linear_new/256 | 3.0944963655 | 4.963655244  
linear_new/512 | 1.4709141274 | 1.5180055402  
linear_new/1024 | 1.8817204301 | 5.4372759857  
linear_new/2048 | 2.4386536374 | 3.8393051031  
linear_new/4096 | 3.23992891 | 2.7079383886  
linear_new/8192 | 4.4912810195 | 2.1599597586  
linear_new/16384 | 4.928827445 | 1.6014678273  
linear_new/32768 | 4.8979353005 | 1.2843768286  
linear_new/65536 | 6.261284366 | 1.3382232253  
linear_new/131072 | 6.2434023818 | 1.5116303742  
linear_new/262144 | 6.2237265378 | 1.4675934991  
linear_new/524288 | 5.9181493922 | 1.3838556637  
linear_new/1048576 | 4.9681630545 | 0.9589277288  
linear_new/2097152 | 4.7427012942 | 1.544116903  
linear_new/4194304 | 4.3805556884 | 1.3731620479  
linear_new/8388608 | 3.4589373364 | 0.9954499755  
linear_new/16777216 | 3.0000001043 | 0.8062589498  
linear_new/33554432 | 2.8414376026 | 0.7276485807  
linear_new/67108864 | 2.65531912 | 0.6808510564  
linear_new/134217728 | 2.1666666667 | 0.4343434311  
linear_new/268435456 | 1.4 | 0.2041666672  
linear_new/536870912 | 1.3137254902 | 0.1535947715  
linear_new/1073741824 | 1.25 | 0.1355932203  
  
Bigger is better, and the "new" code is a simple non-segmented SoE that's been highly optimised - as you can see eventually caching effects take over as you would expect - but it's noticeably faster than KW's code up to about 2^22.  
  
Applying the optimisations to a wheel sieve is easy in principle, but hard in practice (mostly handling the edge cases around segment boundaries).  I do have a prototype wheel sieve that I was going to start optimising, but would it be better/quicker just to document the optimisations so you can apply to your existing code?

---

## Comment 153

> Username: mborland  
> Created_at: 2020-11-22 13:39:38 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-731750187  

As I'm sure you've noticed almost everything related to sieves is easy in principle, but hard in practice. If you send documentation of optimizations I can apply them to the existing code. I have been meaning to copy your bitset implementation to try with the wheel sieve as well.

---

## Comment 154

> Username: jzmaddock  
> Created_at: 2020-11-22 18:33:36 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-731807988  

Well.... it's probably a good idea to document them anyway, as some of them can transform the code in ways that makes it almost impossible for a newcomer to the code to understand... this may take several posts and some of this may be obvious so bare with me! :)  
  
Throughout I'm going to assume the sieve is an array of bytes (and that we have access to the underlying data structures), and that the wheel is a 2,3,5 wheel with W=30 and #W=8.  Also that the data structure is _packed_ so that each byte represents one turn of the wheel (ie W=30 values).  
  
1) Lookup table for very small primes ( < 2^8)  
  
It takes up very little space and is very fast.  It's also useful since to (segment) sieve up to 2^16 we only need the first 2^8 primes, so `prime_sieve` become recursive and asks for `sqrt(upper)` primes at each level eventually ending up at this small table.  
  
2) Sieve initialization.  
  
For a "compact" sieve - either odds only sieve, or wheel sieve containing only non-trivial values we start by setting every bit to 1.  However, the sieve formed by the first N primes forms a repeating pattern of length p_1 * p_2 *....p_n.  We can pre-compute the pattern and `memcpy` it over - note that once we've copied the pattern twice, the amount we can copy doubles with each iteration, so initialization is pretty-fast.  This one isn't  a huge win, but it does help and is easy to program.  For a wheel sieve we would use the pattern formed by the first few primes *not* in the wheel, so for example using 7, 11, 13 would give us a pattern just under 1K in size.  
  
3) Sieving Small Primes.  
  
Each prime P that we're sieving forms a repeating pattern every P blocks (where a block is the underlying data type used for the sieve).  If P is small compared to the block size then we strike it off multiple times in each block, so the process becomes:  
  
* Calculate on the fly (or better pre-compute and store in a table) the pattern of length P blocks for each small prime P.  Note that if calculating on the fly we calculate each pattern by applying the sieving algorithm for prime P to a short block which can be on the stack since there is an upper limit to P for which we apply this algorithm..  
* Loop over the pattern applying it bitwise-and over and over to the sieve.  
  
If we use 64-bit blocks then not only are there many small primes that occur many times in each block, but on Intel the compiler generates MMX code to handle multiple blocks at a time.  I haven't checked to see if -arch=whatever can produce even better code.  For small primes this is up to 20x faster than regular code.  There are two catches - there aren't as many small primes as you might think, and we require as large a block size as possible where as other optimisations require byte sized blocks.  But.  I think if we (pre-)calculate the patterns as byte-strings and then reinterpret_cast **both** pattern and sieve to uint64_t blocks then we should be OK as the actual value of each 64-bit block is irrelevant, and only the bit patterns matter.  Plus for a wheel sieve this is probably the right method for all primes less than 64W = 1920, which is actually pretty good.  
  
4) Sieving medium primes  
  
This is the first of the bigger gains, but is harder to program....  
  
Remember that in sieving each prime P, we get a repeating pattern of length P blocks, with at most B non-trivial values within that pattern (where B is the number of bits in the block).  Further, ignoring the "stride" of each prime for a moment, there are only B such patterns.  The method is now quite straightforward:   
  
* Switch on `bit_index[position % W]` - there are B=8 such cases to handle, where `bit_index` is a table that maps values to the bit position within each block.  
* Within each case we loop handling B=8 cases in each loop.  
  
My odd's only SoE code looks something like this:  
  
```  
               switch (index % 8)  
               {  
               case 0:  
               {  
                  while (p < p_limit)  
                  {  
                     p[stride * 1 + 0] &= bitmask_0;  
                     p[stride * 3 + 0] &= bitmask_1;  
                     p[stride * 5 + 0] &= bitmask_2;  
                     p[stride * 7 + 0] &= bitmask_3;  
                     p[stride * 9 + 0] &= bitmask_4;  
                     p[stride * 11 + 0] &= bitmask_5;  
                     p[stride * 13 + 0] &= bitmask_6;  
                     p[stride * 15 + 0] &= bitmask_7;  
                     p += current_prime;  
                  }  
                  if (p + stride >= p_end) break; p[stride * 1 + 0] &= bitmask_0;  
                  if (p + 3 * stride >= p_end) break; p[stride * 3 + 0] &= bitmask_1;  
                  if (p + 5 * stride >= p_end) break; p[stride * 5 + 0] &= bitmask_2;  
                  if (p + 7 * stride >= p_end) break; p[stride * 7 + 0] &= bitmask_3;  
                  if (p + 9 * stride >= p_end) break; p[stride * 9 + 0] &= bitmask_4;  
                  if (p + 11 * stride >= p_end) break; p[stride * 11 + 0] &= bitmask_5;  
                  if (p + 13 * stride >= p_end) break; p[stride * 13 + 0] &= bitmask_6;  
                  if (p + 15 * stride >= p_end) break; p[stride * 15 + 0] &= bitmask_7;  
                  break;  
               }  
               case 1:  
               {  
                  while (p < p_limit)  
                  {  
                     p[stride * 1 + 0] &= bitmask_1;  
                     p[stride * 3 + 0] &= bitmask_4;  
                     p[stride * 5 + 0] &= bitmask_7;  
                     p[stride * 7 + 1] &= bitmask_2;  
                     p[stride * 9 + 1] &= bitmask_5;  
                     p[stride * 11 + 2] &= bitmask_0;  
                     p[stride * 13 + 2] &= bitmask_3;  
                     p[stride * 15 + 2] &= bitmask_6;  
                     p += current_prime;  
                  }  
                  if (p + stride >= p_end) break; p[stride * 1 + 0] &= bitmask_1;  
                  if (p + 3 * stride >= p_end) break; p[stride * 3 + 0] &= bitmask_4;  
                  if (p + 5 * stride >= p_end) break; p[stride * 5 + 0] &= bitmask_7;  
                  if (p + 7 * stride + 1 >= p_end) break; p[stride * 7 + 1] &= bitmask_2;  
                  if (p + 9 * stride + 1 >= p_end) break; p[stride * 9 + 1] &= bitmask_5;  
                  if (p + 11 * stride + 2 >= p_end) break; p[stride * 11 + 2] &= bitmask_0;  
                  if (p + 13 * stride + 2 >= p_end) break; p[stride * 13 + 2] &= bitmask_3;  
                  if (p + 15 * stride + 2 >= p_end) break; p[stride * 15 + 2] &= bitmask_6;  
                  break;  
               }  
           // etc, more cases.  
```  
  
Each case consists of prolog (handles segment start posistion, not shown above since my SoE was non-segmented), main loop, and epilog (to handle the last few values before the end of segment).  
  
The key is that each line consists of:  
```  
pointer[stride * constant + constant] &= constant;  
```  
  
You can't really write this code by hand - you instrument a sieve and have it print out the code, once you have handled all 8 cases you can stop.  `stride` will be `current_number / W` and you want to run your instrumented sieve only with primes larger than the largest constant, otherwise the stride multipliers get calculated incorrectly!  
  
Now here's the thing, the assembly for the inner loop looks like this:  
  
```  
               p[stride * 1 + 0] &= bitmask_0;  
00007FF7A2D42A10  and         byte ptr [r9+rax*2],0FEh    
               p[stride * 3 + 0] &= bitmask_1;  
00007FF7A2D42A15  and         byte ptr [r9],0FDh    
               p[stride * 5 + 0] &= bitmask_2;  
00007FF7A2D42A19  and         byte ptr [r9+rdx*2],0FBh    
               p[stride * 7 + 0] &= bitmask_3;  
00007FF7A2D42A1E  and         byte ptr [r9+rdx*4],0F7h    
               p[stride * 9 + 0] &= bitmask_4;  
00007FF7A2D42A23  and         byte ptr [r9+rdi*2],0EFh    
               p[stride * 11 + 0] &= bitmask_5;  
00007FF7A2D42A28  and         byte ptr [r9+rdx*8],0DFh    
               p[stride * 13 + 0] &= bitmask_6;  
00007FF7A2D42A2D  and         byte ptr [r9+rbp*2],0BFh    
               p[stride * 15 + 0] &= bitmask_7;  
00007FF7A2D42A32  and         byte ptr [r9+r14*4],7Fh    
               p += current_prime;  
00007FF7A2D42A37  add         rcx,r8    
00007FF7A2D42A3A  add         r9,r8    
```  
  
So that's just 10 instructions per loop.  
  
A regular odds-only SoE uses about the same number of instructions to move forward 2P values, this optimisation moves us 16P values forward with each loop, and for a wheel sieve it's WP = 30P values per loop.  So it's a big win, and as it happens most primes fall into this category.  
  
Note: this method fails once WP > segment_size.  
  
5) Sieving large primes.  
  
Not only does the above method fail for large primes, but even before that point we spend all our time handling the end-points and not in the main loop.  For super large primes, they may not even occur in every segment.  A quick back-of-the-envelope calculation suggests this is only an issue when the upper end of the range is > 2^30.  
  
Using the "naive" method and striking out individual bits is one option.  I believe KW uses a bucket sort: for each large prime run as if on a non-segmented sieve and store in buckets lists of the values to strike out, then when sieving each segment enumerate the values in it's bucket.  
  
6)  Readout.  
  
This is another big one, scanning through the bitset is really quite slow, a much better way is to:  
  
* Convert the sieve into a sequence of 64-bit values (reinterpret_cast if our endianness allows, otherwise read off each 64-bit value in turn).  
* then use a bitscan intrinsic to extract the non-zero bit positions.  
  
My (msvc specific) code looks like this:  
  
```  
   std::uint8_t* p = masks.limbs();  
   for (unsigned offset = index / 8; offset < aligned_end / 8; offset += 8)  
   {  
      std::uint64_t val = *reinterpret_cast<std::uint64_t const*>(p + offset);  
      unsigned long index;  
      while (_BitScanForward64(&index, val))  
      {  
         *out++ = 2 * (index + offset * 8) + 1;  
         val &= (val - 1);  // clears least significant bit  
      }  
   }  
```  
  
There are gcc intrinsics for this as well, but also some portable code at: https://www.chessprogramming.org/BitScan#De_Bruijn_Multiplication which is supposed to be almost as fast.  
  
7) Prime counting.  
  
If we're counting primes, then the above method with a popcount intrinsic is the way to go.  Interestingly such a function is part of C++20's `<bit>` where as it has no bitscan left/right functions :( Otherwise there are portable methods: https://en.wikipedia.org/wiki/Hamming_weight#Efficient_implementation.  
  
In order to accommodate both efficient printing of values, and efficient counting, it may be wise to extract the reading phase into a separate (replaceable) function at least at the internal detail level.  Might be a good use for std::function to prevent code boat from a template parameter functor?  
  
8) Prime ranges.  
  
Back to the obvious ones - if we're looking for primes in the range [a,b] then we need only sieve segments in the range [max(sqrt(b), a), b].  A big win when a >> sqrt(b).  
  
9) Integer types.  
  
A small and fiddly optimisation.  
  
When grabbing the first `sqrt(upper)` primes, we need only use an integer type half the width of the argument types, and in any case a type no wider than size_t (since we wouldn't be able to store all the primes requiring a wider type).  This saves us half the storage we would otherwise require.  
  
Also when sieving each segment we can use a type no wider than `size_t`, and only convert to type `Integer` during reading/output.  This one only matters if we're using a multiprecision type to sieve/count above values holdable in a `size_t`.  
  
Whew.  
  
I've probably missed something and not explained all of it perfectly well, but hopefully it gives you the idea.  Ping me if you want more details for any of it.  
  
Also note that individually, each of these optimisations is applied to a stage of the algorithm that takes no more than 10-20% of the runtime.... so sadly you only start to see big gains once you've applied nearly all of them.  They do all each chop big globs of time off their respective stages though.

---

## Comment 155

> Username: jzmaddock  
> Created_at: 2020-11-22 18:38:13 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-731809273  

Forgot to say.... I'm assuming that the sieve is a SoE with wheel, and not SoA or SoP.  While the latter have better O(n) performance, in practice the extra log(log(n)) term is < 1.3 for n = 2^64.  So IMO simple well optimised code is always going to win out.  I'm not saying these optimisations can't be applied to these sieves, just that I don't know how right now ;)

---

## Comment 156

> Username: jzmaddock  
> Created_at: 2020-11-22 18:49:52 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-731812789  

Also.... this paper has a very good description of the various sieves including C++ pseudo-code: https://www.researchgate.net/publication/2274357_Trading_Time_for_Space_in_Prime_Number_Sieves

---

## Comment 157

> Username: mborland  
> Created_at: 2020-11-23 17:17:51 UTC  
> Updated_at: 2020-12-05 16:10:20 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-732302918  

That's a good chunk of information. Right now I have a 2,3,5,7 wheel (removes ~77% of composites) that can readily be reduced to a 2,3,5 (removes ~73%). The internal data structure is a simple `std::vector<bool>` which is packed but it will have to be replaced to represent one turn of the wheel per byte. I'll post a check-list with notes if you want to look at the implementation as I go through.  
  
### Optimizations Status  
- [x] Replace 2,3,5,7 wheel with 2,3,5  
- [x] Packed data structure  
- [x] Lookup Table - First 49 primes (std::int_fast16_t) and 49 pseudo-primes (uint128_t) are stored in static constexpr arrays   
- [x] Sieve initialization  
- [x] Sieving small primes  
- [ ] Sieving medium primes  
- [ ] Sieving large primes  
- [x] Readout  
- [x] Prime counting  
- [x] Prime ranges  
- [x] Integer types

---

## Comment 158

> Username: jzmaddock  
> Created_at: 2020-11-24 08:49:14 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-732749394  

Thanks for persisting with this Matt - I do realise that was a big "brain dump" of info I just gave you!  
  
For sieving small primes I said:  
  
>Plus for a wheel sieve this is probably the right method for all primes less than 64W = 1920, which is actually pretty good.  
  
That should be primes less than 8W=240 (mixed up my bits and bytes there!).  Might only be required for primes less than 30 as that's the point at which the following method hits each byte more than once - if it works at all that is, I haven't checked that.

---

## Review 159 [Commented]

> Username: jzmaddock  
> Created_at: 2020-11-24 13:23:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/400#pullrequestreview-537485656  

📁 include/boost/math/special_functions/detail/simple_bitset.hpp

```diff
  17 |+ {
  18 |+ template <typename I = std::uint64_t>
  19 |+ class simple_bitset
```

> Username: jzmaddock  
> Created_at: 2020-11-24 13:23:19 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r529540894  

I ended up simplifying this code a lot locally as while msvc does a decent job of optimising this code, GCC does not.  And strangely both compilers generate faster code for `simple_bitset<unsigned char>` with the simplified version.  Here we go:  
  
```  
      template <class I>  
      struct simple_bitset  
      {  
         std::unique_ptr<I[]> bits;  
         std::size_t m_size;  
         simple_bitset(std::size_t n) : bits(new I[n / (sizeof(I) * CHAR_BIT) + (n % (sizeof(I) * CHAR_BIT) ? 1 : 0)]), m_size(n)  
         {  
            std::memset(bits.get(), 0xff, n / CHAR_BIT + (n % CHAR_BIT ? 1 : 0));  
         }  
         // Initialize with specific pattern:  
         simple_bitset(std::size_t n, I const* pattern, std::size_t len) : bits(new I[n / (sizeof(I) * CHAR_BIT) + (n % (sizeof(I) * CHAR_BIT) ? 1 : 0)]), m_size(n)  
         {  
            const std::size_t block_count = n / (sizeof(I) * CHAR_BIT) + (n % (sizeof(I) * CHAR_BIT) ? 1 : 0);  
            if (block_count <= len)  
            {  
               std::memcpy(bits.get(), pattern, block_count * sizeof(I));  
            }  
            else  
            {  
               I* p = bits.get();  
               std::memcpy(p, pattern, len * sizeof(I));  
               I* base = p;  
               p += len;  
               while (len <= block_count - len)  
               {  
                  std::memcpy(p, base, len * sizeof(I));  
                  p += len;  
                  len *= 2;  
               }  
               if (block_count > len)  
                  std::memcpy(p, base, (block_count - len) * sizeof(I));  
            }  
         }  
         static constexpr std::size_t ln2(std::size_t n)  
         {  
            return n <= 1 ? 0 : 1 + ln2(n >> 1);  
         }  
         I* limbs()  
         {  
            return bits.get();  
         }  
         I test(std::size_t n)const  
         {  
            constexpr I mask = (sizeof(I) * CHAR_BIT) - 1;  
            constexpr std::size_t shift = ln2(sizeof(I) * CHAR_BIT);  
            return bits[n >> shift] & (I(1u) << (n & mask));  
         }  
         void clear(std::size_t n)  
         {  
            constexpr I mask = (sizeof(I) * CHAR_BIT) - 1;  
            constexpr std::size_t shift = ln2(sizeof(I) * CHAR_BIT);  
            bits[n >> shift] &= ~I(I(1u) << (n & mask));  
         }  
         void set(std::size_t n)  
         {  
            constexpr I mask = (sizeof(I) * CHAR_BIT) - 1;  
            constexpr std::size_t shift = ln2(sizeof(I) * CHAR_BIT);  
            //BOOST_ASSERT((n >> shift) < (m_size / (sizeof(I) * CHAR_BIT) + (m_size % (sizeof(I) * CHAR_BIT) ? 1 : 0)));  
            bits[n >> shift] |= (I(1u) << (n & mask));  
         }  
         std::size_t size()const { return m_size; }  
         void reset() { std::memset(bits.get(), 0xff, m_size / CHAR_BIT + (m_size % CHAR_BIT ? 1 : 0)); }  
         void clear_all() { std::memset(bits.get(), 0, m_size / CHAR_BIT + (m_size % CHAR_BIT ? 1 : 0)); }  
      };  
```

> Username: mborland  
> Created_at: 2020-11-24 19:07:58 UTC  
> Updated_at: 2020-12-13 11:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/400#discussion_r529814347  

The updated bitset has been added in commit d649f65. For initializing with a specific pattern what do you think about using the [gaps in the MOD 30 wheel](https://en.wikipedia.org/wiki/Wheel_factorization#/media/File:Wheel_factorization-n=30.svg) as the specific pattern to set? This would remove setting 22 of every 30 values and is a very short pattern. The only difficulty would be finding where to start based of each threads assigned lower and upper bound. The `IntervalSieve` class is reused so it would only be a concern on the first pass. `simple_bitset` should then satisfy the requirements for packed data structure and the initialization optimization.


---

## Comment 160

> Username: jzmaddock  
> Created_at: 2020-11-25 09:10:08 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-733571594  

> For initializing with a specific pattern what do you think about using the gaps in the MOD 30 wheel as the specific pattern to set?  
  
I *think* that for the other optimisations to work, and for the wheel to be in synch with the underlying bytes, we have to use a compacted data structure - one that contains only the 8 values in the wheel that form the prime candidates on each rotation (ie the spokes of the wheel).  
  
Soronson in this paper: https://www.researchgate.net/publication/2274357_Trading_Time_for_Space_in_Prime_Number_Sieves  has some very lucid descriptions of the various sieves and how to index a compacted sieve (see "reducing space with a wheel" in the above ref).  Note that this is normally much slower than a straight SoE as the inner loop become much more complex.  However, once the small and medium prime optimisations I listed above are applied they are all effectively the same complexity, and now just differ in how far they skip forward with each loop / wheel turn.  
  
Another way to look at this: my odd's only sieve is actually a wheel sieve with just one prime (2) in the wheel.  So you get 16 numbers and 8 candidate primes per byte.  A 2,3,5 wheel has 8 candidates per wheel turn (30), so we would normally start with all bits 1, and each byte represents the 8 possible primes in each block of 30 values of a full wheel turn.  Now lets suppose you take that data structure and sieve out 7, 11 and 13 as well.  Now you have a repeating bit pattern 7*11*13 bytes long you can use to initialize the sieve, you can then start sieving at 17 rather than at 7 as you normally would.  
  
I hope that makes sense, but maybe I misunderstood you?

---

## Comment 161

> Username: mborland  
> Created_at: 2020-11-25 18:04:56 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-733865341  

Each turn of the wheel being represented in one 8 bit byte since there are only 8 possible primes using the 2,3,5 wheel was what I was getting at. Let me see if I can get this working with the 2,3,5 wheel and then expanding to a larger wheel should be easy enough. In `prime_wheel.hpp` the class `Wheel` is used to compute and print the patterns into the terminal so I can copy and paste them into the pre-computed wheels (`MOD30Wheel` and `MOD210Wheel`) that are actually used.

---

## Comment 162

> Username: mborland  
> Created_at: 2020-11-29 10:59:50 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-735375208  

@jzmaddock I am making some good headway on your list of additions and optimizations. In commit eafbefc all multiples of the wheel basis in the bitset have been removed so each turn of the wheel now takes only 8 bits to represent. The implementation passes all unit tests, but the sieve is many times slower than it was, especially when using `boost::multiprecision` types. When you have a chance can you take a look? Thanks.

---

## Comment 163

> Username: jzmaddock  
> Created_at: 2020-12-02 19:16:19 UTC  
> Url: https://github.com/boostorg/math/pull/400#issuecomment-737439614  

Apologies for being late back here - I'll take a look as soon as I can, but I would expect the code to be significantly slower until the sieving optimisations are in place.  Sorenson notes this in his paper, the reason being that indexing into the sieve is now much more complex *until* the optimisations are applied after which all the sieves (compressed or not, wheel or not) are equal.  Hope this makes sense!

---
