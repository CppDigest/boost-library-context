# #376 Fibonacci numbers [Merged]

> Username: madhur4127  
> Created at: 2020-06-24 18:15:09 UTC  
> Updated at: 2021-03-16 13:11:11 UTC  
> Merged at: 2021-03-16 13:11:11 UTC  
> Closed at: 2021-03-16 13:11:11 UTC  
> Url: https://github.com/boostorg/math/pull/376  

Based on #371   
  
Currently, the code checks for the sufficient bits using `std::numeric_limits<T>::digits` and throws if the result overflows. This check uses `std::log` and is *fast*. 94th Fibonacci number overflows the precision of `uint64_t`.  
  
I would appreciate feedback on the overflow behavior. My desired approach would be to leave the overflow handling to the user and provide a fast bare-bones version for users to build abstractions. This approach also works in the case of custom modular arithmetic integer wrapper as the template parameter. This functionality can also be made available by providing a non-throwing overload.   
  
The time complexity is O(log2(N) * Multiply(B)) where F_n denotes the nth fibonacci number, B is the number of bits in F_n.

---

## Review 1 [Commented]

> Username: NAThompson  
> Created_at: 2020-06-24 21:09:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/376#pullrequestreview-437008795  

📁 include/boost/math/special_functions/fibonacci.hpp

```diff
   9 |+ #define BOOST_MATH_SPECIAL_FIBO_HPP
  10 |+ 
  11 |+ #include "boost/math/constants/constants.hpp"
```

> Username: NAThompson  
> Created_at: 2020-06-24 21:09:52 UTC  
> Updated_at: 2021-03-15 15:05:25 UTC  
> Url: https://github.com/boostorg/math/pull/376#discussion_r445173533  

Did you want this one in quotes?


---

## Review 2 [Commented]

> Username: NAThompson  
> Created_at: 2020-06-24 21:10:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/376#pullrequestreview-437009457  

📁 include/boost/math/special_functions/fibonacci.hpp

```diff
  25 |+ T fibonacci(unsigned long long n, const Policy &pol) {
  26 |+     using ull = unsigned long long;
  27 |+     BOOST_ASSERT(n >= 0);
```

> Username: NAThompson  
> Created_at: 2020-06-24 21:10:57 UTC  
> Updated_at: 2021-03-15 15:05:25 UTC  
> Url: https://github.com/boostorg/math/pull/376#discussion_r445174044  

If `n` is unsigned, isn't this assert superfluous?


---

## Review 3 [Commented]

> Username: NAThompson  
> Created_at: 2020-06-24 21:12:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/376#pullrequestreview-437010075  

📁 include/boost/math/special_functions/fibonacci.hpp

```diff
  27 |+     BOOST_ASSERT(n >= 0);
  28 |+     if (n <= 3) return (n + 1) >> 1;
  29 |+     if (std::ceil(n * std::log2(boost::math::constants::phi<long double>()) - std::log2(5) / 2.0) > std::numeric_limits<T>::digits)
```

> Username: NAThompson  
> Created_at: 2020-06-24 21:12:04 UTC  
> Updated_at: 2021-03-15 15:05:25 UTC  
> Url: https://github.com/boostorg/math/pull/376#discussion_r445174526  

I might do a bit of analysis to see if you could get away with `float` rather than `long double` here. I worry the check will take longer than the computation!

> Username: madhur4127  
> Created_at: 2020-06-28 18:22:13 UTC  
> Updated_at: 2021-03-15 15:05:25 UTC  
> Url: https://github.com/boostorg/math/pull/376#discussion_r446681896  

I took the constants out from the `if` check. In future, I'm hoping if `std::log2` becomes `constexpr` then it would be very efficient. To deal with the imprecision issues with floating-point, I guess a difference of `std::numeric_limits<T>::epsilon` must be relaxed.  
  
There are two extremes now, a very precise check or no check (in unchecked version).


---

## Review 4 [Commented]

> Username: NAThompson  
> Created_at: 2020-06-24 21:12:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/376#pullrequestreview-437010367  

📁 include/boost/math/special_functions/fibonacci.hpp

```diff
  23 |+ 
  24 |+ template <typename T, class Policy>
  25 |+ T fibonacci(unsigned long long n, const Policy &pol) {
```

> Username: NAThompson  
> Created_at: 2020-06-24 21:12:32 UTC  
> Updated_at: 2021-03-15 15:05:25 UTC  
> Url: https://github.com/boostorg/math/pull/376#discussion_r445174785  

`inline`?


---

## Review 5 [Commented]

> Username: NAThompson  
> Created_at: 2020-06-24 21:13:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/376#pullrequestreview-437010721  

📁 include/boost/math/special_functions/fibonacci.hpp

```diff
  23 |+ 
  24 |+ template <typename T, class Policy>
  25 |+ T fibonacci(unsigned long long n, const Policy &pol) {
```

> Username: NAThompson  
> Created_at: 2020-06-24 21:13:02 UTC  
> Updated_at: 2021-03-15 15:05:25 UTC  
> Url: https://github.com/boostorg/math/pull/376#discussion_r445175095  

Should there be a static assert that `T` is an integral type?

> Username: madhur4127  
> Created_at: 2020-06-28 16:52:21 UTC  
> Updated_at: 2021-03-15 15:05:25 UTC  
> Url: https://github.com/boostorg/math/pull/376#discussion_r446672831  

I think a static assert for `is_arithmetic` should be better instead as there's no harm in using floating-point types.  
  
I only require +,-,* operations on the type.   
  
C++20 style would be to use concepts and requires expression here, I guess.


---

## Comment 6

> Username: madhur4127  
> Created_at: 2020-06-28 16:16:02 UTC  
> Url: https://github.com/boostorg/math/pull/376#issuecomment-650787761  

Thanks, Nick for the review! Sorry for the delayed response!

---

## Comment 7

> Username: NAThompson  
> Created_at: 2020-06-29 13:15:37 UTC  
> Url: https://github.com/boostorg/math/pull/376#issuecomment-651113434  

@madhur4127 : Could you also put a benchmark in `reporting/performance` as well as some unit tests?

---

## Comment 8

> Username: madhur4127  
> Created_at: 2020-07-04 08:59:59 UTC  
> Updated_at: 2020-07-04 09:00:59 UTC  
> Url: https://github.com/boostorg/math/pull/376#issuecomment-653741429  

I am stuck on trying to identify floating point types and integral types in general.  
  
Standard says that specialising type_traits for is_arithmetic, is_floating_point is UB.  
  
This is because I think it would be fast to call O(1) closed expression in case of floating point type but it will have precision issues with integers.  
  
Or should I add a new function for floating point and let user call the necessary?

---

## Comment 9

> Username: pabristow  
> Created_at: 2020-07-04 09:51:54 UTC  
> Url: https://github.com/boostorg/math/pull/376#issuecomment-653745929  

Is boost::multiprecision::number_category any help to you?  
  
template<class FPType>  
typename std::enable_if<  
  // (std::is_floating_point<FPType>::value == true), // Only enables fundamental types.  
  ((boost::multiprecision::number_category<FPType>::value  
    == boost::multiprecision::number_kind_floating_point) == true),  
  void>::type  
your_foo() ...  
  
also boost::multiprecision::number_kind_integer and rational and complex.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2020-07-04 11:29:58 UTC  
> Url: https://github.com/boostorg/math/pull/376#issuecomment-653754274  

>Is boost::multiprecision::number_category any help to you?  
  
Yes but it adds a dependency to multiprecision.  
  
numeric_limits is your friend here IMO: integer types have is_integer set to true, floating point types have min_exponent != max_exponent.  For types where is_specialized is false, then it's more likely to be a floating point type (reasoning is that variable precision floating point types can't easily specialize numeric_limits, butvariable precision integer types can).  
  
HTH, John.

---

## Comment 11

> Username: madhur4127  
> Created_at: 2020-07-12 22:27:26 UTC  
> Updated_at: 2020-07-13 11:37:11 UTC  
> Url: https://github.com/boostorg/math/pull/376#issuecomment-657284504  

I added the test file and performance stats (as shown below) in reporting/performance.  
  
As the `n` increases cost of recursion is only minimal thus iterative has no performance advantage. I used GMP as the backend for both the functions. `recursive_slow` is not _supposed_ to be slow because I wrote it to outperform naive implementations.  
  
Both of them work in O(log n) time and O(1) space (without taking into account big integer's space and time complexity for less verbosity).  
  
```  
-----------------------------------------------------------------  
Benchmark                       Time             CPU   Iterations  
-----------------------------------------------------------------  
recursive_slow/8             3669 ns         3594 ns       190414  
recursive_slow/64            6213 ns         6199 ns       116423  
recursive_slow/512           8999 ns         8990 ns        78773  
recursive_slow/4096         14529 ns        13984 ns        51206  
recursive_slow/32768        74183 ns        73039 ns         8539  
recursive_slow/262144     1297806 ns      1291304 ns          569  
recursive_slow/2097152   23166565 ns     22751898 ns           30  
recursive_slow/4194304   47038831 ns     46546938 ns           16  
recursive_slow_BigO          0.51 NlgN       0.51 NlgN   
recursive_slow_RMS              5 %             5 %      
iterative_fast/8             1413 ns         1399 ns       493692  
iterative_fast/64            2408 ns         2394 ns       298417  
iterative_fast/512           4181 ns         4132 ns       170957  
iterative_fast/4096          7627 ns         7558 ns        93554  
iterative_fast/32768        65080 ns        64791 ns        11289  
iterative_fast/262144     1207873 ns      1200725 ns          557  
iterative_fast/2097152   19627331 ns     19510132 ns           36  
iterative_fast/4194304   42351871 ns     42240620 ns           17  
iterative_fast_BigO          0.46 NlgN       0.45 NlgN   
iterative_fast_RMS              5 %             5 %  
```

---

## Comment 12

> Username: madhur4127  
> Created_at: 2020-07-12 22:42:32 UTC  
> Updated_at: 2020-07-12 22:42:45 UTC  
> Url: https://github.com/boostorg/math/pull/376#issuecomment-657286047  

1. Because of imprecisions, the calculation for the exact overflow limit for a type is off by 1 (up to 2000) and increases as the `n` increases. But on a bright side, when the overflow exception is thrown then it hints that overflow would _surely_ occur. So overflow can be missed but not falsely reported.  
  
2. I couldn't convince myself about limiting the Fibonacci to types like integral only. Any type for which +, -, * is defined should be enough. This could easily be checked with C++20's requires clause, but till Boost.Math reaches that stage compiler errors would be the only help.

---

## Comment 13

> Username: NAThompson  
> Created_at: 2020-07-12 22:47:18 UTC  
> Url: https://github.com/boostorg/math/pull/376#issuecomment-657286637  

> I couldn't convince myself about limiting the Fibonacci to types like integral only.  
  
Yes, I now agree on this point; a use case might be computation of the [reciprocal Fibonacci constant](https://mathworld.wolfram.com/ReciprocalFibonacciConstant.html).

---

## Review 14 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-12 22:48:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/376#pullrequestreview-446915937  

📁 reporting/performance/test_fibonacci.cpp

```diff
  24 |+ }
  25 |+ constexpr int bm_start = 1 << 3, bm_end = 1 << 22;
  26 |+ BENCHMARK(recursive_slow)->Range(bm_start, bm_end);
```

> Username: NAThompson  
> Created_at: 2020-07-12 22:48:46 UTC  
> Updated_at: 2021-03-15 15:05:25 UTC  
> Url: https://github.com/boostorg/math/pull/376#discussion_r453372735  

Could you add `Complexity()` here?  
  
```cpp  
BENCHMARK(recursive_slow)->Range(bm_start, bm_end)->Complexity();  
```

> Username: madhur4127  
> Created_at: 2020-07-12 23:06:58 UTC  
> Updated_at: 2021-03-15 15:05:25 UTC  
> Url: https://github.com/boostorg/math/pull/376#discussion_r453374345  

`Complexity(benchmark::oLogN);` results in:  
  
```  
recursive_slow_BigO          -nan lgN        -nan lgN    
recursive_slow_RMS           -nan %          -nan %      
...  
iterative_fast_BigO          -nan lgN        -nan lgN    
iterative_fast_RMS           -nan %          -nan %    
```

> Username: NAThompson  
> Created_at: 2020-07-12 23:09:04 UTC  
> Updated_at: 2021-03-15 15:05:25 UTC  
> Url: https://github.com/boostorg/math/pull/376#discussion_r453374517  

Oh, yeah you need to do `state.SetComplexityN(state.range(0))` after the for loop completes. Also, don't add the `benchmark::oLogN` key or else you're assuming the desired result.


---

## Review 15 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-12 22:49:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/376#pullrequestreview-446915962  

📁 reporting/performance/test_fibonacci.cpp

```diff
  30 |+         benchmark::DoNotOptimize(boost::math::fibonacci<T>(state.range(0)));
  31 |+ }
  32 |+ BENCHMARK(iterative_fast)->Range(bm_start, bm_end);
```

> Username: NAThompson  
> Created_at: 2020-07-12 22:49:01 UTC  
> Updated_at: 2021-03-15 15:05:25 UTC  
> Url: https://github.com/boostorg/math/pull/376#discussion_r453372754  

Same thing here with `Complexity()`.


---

## Review 16 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-13 11:15:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/376#pullrequestreview-447157433  

📁 include/boost/math/special_functions/fibonacci.hpp

```diff
  21 |+ namespace math {
  22 |+ namespace detail {
  23 |+ // this should be constexpr in future
```

> Username: NAThompson  
> Created_at: 2020-07-13 11:15:07 UTC  
> Updated_at: 2021-03-15 15:05:25 UTC  
> Url: https://github.com/boostorg/math/pull/376#discussion_r453576502  

Have you checked godbolt to see if these get compiled into constants? If not, we might want to put:  
  
```  
fib_bits_phi = 0.6942419136306174; // = ln(phi)/ln(2)  
```

> Username: madhur4127  
> Created_at: 2020-07-13 11:25:28 UTC  
> Updated_at: 2021-03-15 15:05:25 UTC  
> Url: https://github.com/boostorg/math/pull/376#discussion_r453581686  

[Yes, they are reduced into constants](https://compiler-explorer.com/z/jK93M5)  
  
**Edit**: I made a typo at `2`. Updated the link.


---

## Review 17 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-13 11:16:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/376#pullrequestreview-447158337  

📁 include/boost/math/special_functions/fibonacci.hpp

```diff
  50 |+     return fibonacci_unchecked<T>(n);
  51 |+ }
  52 |+ 
```

> Username: NAThompson  
> Created_at: 2020-07-13 11:16:39 UTC  
> Updated_at: 2021-03-15 15:05:25 UTC  
> Url: https://github.com/boostorg/math/pull/376#discussion_r453577303  

It occurs to me we might want to add `fibonacci_next` generator so that we can call it in a loop; do you agree?

> Username: madhur4127  
> Created_at: 2020-07-13 11:32:01 UTC  
> Updated_at: 2021-03-15 15:05:25 UTC  
> Url: https://github.com/boostorg/math/pull/376#discussion_r453584968  

I don't understand what do you mean by a generator here. Can you please share some examples or elaborate more.  
  
I think you meant like generating next Fibonacci number continuously but that can be done trivially by a user:  
```c++  
int a = 0, b = 1;  
while (n--)   
    swap(a,b), b += a;  
```  
Fibonacci numbers need previous two values to compute the next term, so generator in that case would take the arguments and simply return the sum?

> Username: madhur4127  
> Created_at: 2020-07-13 11:39:46 UTC  
> Updated_at: 2021-03-15 15:05:25 UTC  
> Url: https://github.com/boostorg/math/pull/376#discussion_r453588820  

Or like Python's `yield`? Where calling `next()` would return next fibonacci number?

> Username: NAThompson  
> Created_at: 2020-07-16 13:31:01 UTC  
> Updated_at: 2021-03-15 15:05:25 UTC  
> Url: https://github.com/boostorg/math/pull/376#discussion_r455787267  

@madhur4127: Yes, that's what I mean. For an example, [here's](https://github.com/boostorg/multiprecision/blob/aee74ade95f6ebb458b63b9796b3e0923e214783/example/representations/cfrac_patterns.cpp) one that generates a particular continued fraction; you then pass it to the continued fraction evaluator which calls it's call operator.  
  
Could you write a quick code that evaluates the [reciprocal Fibonacci constant](https://en.wikipedia.org/wiki/Reciprocal_Fibonacci_constant) and add it to `examples`? I think you'll see how natural a generator is for that use; but if you find it can be done easily without a generator feel free to ignore this request.


---

## Review 18 [Commented]

> Username: madhur4127  
> Created_at: 2020-07-13 11:26:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/376#pullrequestreview-447164069  

📁 test/Jamfile.v2

```diff
 491 |    [ run test_zeta.cpp ../../test/build//boost_unit_test_framework test_instances//test_instances pch_light  ]
 492 |    [ run test_sinc.cpp ../../test/build//boost_unit_test_framework pch_light ]
 493 |+    [ run test_fibonacci.cpp ../../test/build//boost_unit_test_framework : : : <linkflags>-lgmp [ requires cpp_lambdas ] ]
```

> Username: madhur4127  
> Created_at: 2020-07-13 11:26:16 UTC  
> Updated_at: 2021-03-15 15:05:25 UTC  
> Url: https://github.com/boostorg/math/pull/376#discussion_r453582089  

I don't really require lamdba, but it's a hack for C++11 check required by `<cstdint>`

> Username: NAThompson  
> Created_at: 2020-07-14 13:47:45 UTC  
> Updated_at: 2021-03-15 15:05:25 UTC  
> Url: https://github.com/boostorg/math/pull/376#discussion_r454368908  

@madhur4127: I personally don't mind if this is written in C++17. I'm doing all my new features in C++17 so that if I need to make changes that require C++17 I won't break anything.  
  
Hopefully that's not being obtuse.


---

## Comment 19

> Username: madhur4127  
> Created_at: 2020-07-14 03:42:31 UTC  
> Updated_at: 2020-07-14 03:42:56 UTC  
> Url: https://github.com/boostorg/math/pull/376#issuecomment-657948555  

Sorry for a newbie type question. But is there a way to add gmp to Appveyor (MSVC) build? Some tests are failing because of the `gmp.h` not found error.  
  
My current solution is to add a check for gmp but that'll make the tests avoid builds on msvc which is undesirable.  
  
```  
// Jamfile.v2  
[ run test_fibonacci.cpp ../../test/build//boost_unit_test_framework : : : <linkflags>-lgmp   
    [ check-target-builds ../config//has_gmp : : <build>no ] [ requires cpp_lambdas ] ]  
```

---

## Comment 20

> Username: NAThompson  
> Created_at: 2020-07-16 22:42:02 UTC  
> Url: https://github.com/boostorg/math/pull/376#issuecomment-659714918  

@madhur4127 : Please tag your commits with `[CI SKIP]` until you are confident that it's ready to go; we're getting backed up on the CI system.

---

## Comment 21

> Username: madhur4127  
> Created_at: 2020-07-17 03:46:20 UTC  
> Updated_at: 2020-07-17 03:47:15 UTC  
> Url: https://github.com/boostorg/math/pull/376#issuecomment-659825830  

@NAThompson Sure, I am confident about passing CI tests this time. I wish to make the CI run for this once successfully so that I could be sure about the working of the Fibonacci code without generator.  
  
I will be working on the generator next. Thank you for the idea.

---

## Comment 22

> Username: madhur4127  
> Created_at: 2020-07-25 07:58:27 UTC  
> Url: https://github.com/boostorg/math/pull/376#issuecomment-663825414  

@NAThompson, I think the PR is in good shape.  
  
1. Changes till Fibonacci numbers passed the CI tests.  
2. Fibonacci generator is added with test and an example which hopefully will pass the CI too (so no tagging of CI SKIP).

---

## Comment 23

> Username: NAThompson  
> Created_at: 2020-07-25 13:38:47 UTC  
> Url: https://github.com/boostorg/math/pull/376#issuecomment-663856654  

@madhur4127 : Ok, looking good. Could you add documentation to `doc/sf`?

---

## Comment 24

> Username: madhur4127  
> Created_at: 2020-08-14 02:37:27 UTC  
> Url: https://github.com/boostorg/math/pull/376#issuecomment-673851057  

Sorry guys, I will add the docs soon. Sorry for the loooong delay!

---

## Comment 25

> Username: madhur4127  
> Created_at: 2020-09-06 04:54:08 UTC  
> Url: https://github.com/boostorg/math/pull/376#issuecomment-687700713  

Hi All,  
  
Sorry for the looooong delay. I had added the documentation into `number_series.qbk` as it is similar in spirit to prime_numbers, bernoulli_numbers. I am open to relocating it 😄   
  
I also added comments and made the unchecked function constexpr. I couldn't make all of them constexpr because of `std::log` not being constexpr. Although compilers are smart in precomputing the results as seen in the godbolt link in the review comments.  
  
Lastly the feature will require atleast C++14 because of the constexpr changes, but previously required C++11. But I plan to upgrade it as and when the constexpr `cmath` is available. I have added C++17 as the requirement in the test as @NAThompson pointed out.  
  
The test failures are in `distribution_tests/test_kolmogorov_smirnov.cpp:59:44: error: no matching function` whereas my changes are in `special_functions` so I guess that's okay :)

---

## Comment 26

> Username: NAThompson  
> Created_at: 2021-01-17 16:38:26 UTC  
> Url: https://github.com/boostorg/math/pull/376#issuecomment-761840942  

@madhur4127 : Are you happy with this? If so could you rebase on develop?

---

## Comment 27

> Username: madhur4127  
> Created_at: 2021-01-17 17:14:49 UTC  
> Updated_at: 2021-01-17 17:15:30 UTC  
> Url: https://github.com/boostorg/math/pull/376#issuecomment-761846096  

Yes @NAThompson, I think it is complete with the documentation + tests.  
  
I'll rebase it. Thanks!

---

## Comment 28

> Username: jzmaddock  
> Created_at: 2021-01-17 18:00:33 UTC  
> Url: https://github.com/boostorg/math/pull/376#issuecomment-761853103  

I have only 2 comments:  
  
1) The existing "unchecked" functions are called "unchecked_factorial" and "unchecked_bernoulli_b2n", right or wrong I think we should probably stick with that convention and use "unchecked_fibonacci" here.  
2) The example could usefully go in the docs at the end: use quickbooks code import feature to directly import markup from the example rather than cut and pasting, see: https://www.boost.org/doc/libs/1_75_0/doc/html/quickbook/syntax/block.html#quickbook.syntax.block.import

---

## Comment 29

> Username: madhur4127  
> Created_at: 2021-03-14 16:49:30 UTC  
> Url: https://github.com/boostorg/math/pull/376#issuecomment-798939763  

Sorry Nick (@NAThompson) and John (@jzmaddock),  
  
This PR somehow got flushed out from my memory. Terrific 2020 eh.  
  
1. Agreed, for consistency, I'll renamed it to `unchecked_fibonacci`  
2. But there's a problem, the documentation, I wrote is kinda snippet based. For example:  
```  
   boost::math::fibonacci_generator<int> gen;  
   int x = gen(); // x is 0  
   int y = gen(); // y is 1  
   for(int i = 0; i < 5; ++i) // this loop is same as gen.set(7);  
       gen();  
   int z = gen(); // z is 13boost::math::fibonacci_generator<int> gen;  
 ```  
This should really be a new file instead of a function in the same file of the documentation.  
  
I like that way of documentation, for example, Boost.Asio is a nice example of that but this will be a overkill, I think.  
  
I'm open to ideas :)

---

## Comment 30

> Username: NAThompson  
> Created_at: 2021-03-15 12:27:04 UTC  
> Url: https://github.com/boostorg/math/pull/376#issuecomment-799379029  

> This should really be a new file instead of a function in the same file of the documentation.  
  
Well, it could also be a separate PR just to get this one past the finish line.

---

## Comment 31

> Username: madhur4127  
> Created_at: 2021-03-15 14:02:39 UTC  
> Url: https://github.com/boostorg/math/pull/376#issuecomment-799445017  

This seems a clever solution. I'll remove the documentation and finish this.

---
