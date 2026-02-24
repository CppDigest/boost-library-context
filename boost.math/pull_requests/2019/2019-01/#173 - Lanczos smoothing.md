# #173 Lanczos smoothing [Merged]

> Username: NAThompson  
> Created at: 2019-01-01 03:12:31 UTC  
> Updated at: 2019-03-03 00:15:00 UTC  
> Merged at: 2019-01-28 21:16:50 UTC  
> Closed at: 2019-01-28 21:16:50 UTC  
> Url: https://github.com/boostorg/math/pull/173  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2019-01-04 20:17:30 UTC  
> Url: https://github.com/boostorg/math/pull/173#issuecomment-451556716  

I'm now happy with the design of this class, as well as its performance. Pending a successful build, I believe it to be finished. There is of course no rush to merge it, but if anyone wishes to try it out, they won't be doing so on top of unstable code.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2019-01-22 22:54:01 UTC  
> Updated_at: 2019-01-27 04:50:32 UTC  
> Url: https://github.com/boostorg/math/pull/173#issuecomment-456594990  

Benchmarks:  
  
```cpp  
#include <benchmark/benchmark.h>  
#include <array>  
#include <random>  
#include <iostream>  
#include <algorithm>  
#include <boost/math/tools/univariate_statistics.hpp>  
#include <boost/math/tools/bivariate_statistics.hpp>  
#include <boost/math/tools/signal_statistics.hpp>  
#include <boost/math/tools/norms.hpp>  
#include <boost/math/differentiation/lanczos_smoothing.hpp>  
  
template<class Real>  
void BM_LanczosVelocity(benchmark::State& state)  
{  
    std::vector<Real> v(state.range(0));  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = i;  
    }  
  
    using boost::math::differentiation::discrete_lanczos_derivative;  
    auto lanczos = discrete_lanczos_derivative(Real(1));  
    for (auto _ : state)  
    {  
        auto w = lanczos(v);  
        benchmark::DoNotOptimize(w[0]);  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_LanczosVelocity, double)->RangeMultiplier(2)->Range(1<<6, 1<<22)->Complexity(benchmark::oN);  
  
  
template<class Real>  
void BM_LanczosVelocityPointEvaluation(benchmark::State& state)  
{  
    std::vector<Real> v(state.range(0));  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = i;  
    }  
  
    using boost::math::differentiation::discrete_lanczos_derivative;  
    auto lanczos = discrete_lanczos_derivative(Real(1));  
    for (auto _ : state)  
    {  
        auto w = lanczos(v, v.size()/2);  
        benchmark::DoNotOptimize(w);  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_LanczosVelocityPointEvaluation, double)->RangeMultiplier(2)->Range(1<<6, 1<<22)->Complexity();  
  
template<class Real>  
void BM_LanczosVelocityConstructor(benchmark::State& state)  
{  
    using boost::math::differentiation::discrete_lanczos_derivative;  
    for (auto _ : state)  
    {  
        auto lanczos = discrete_lanczos_derivative(Real(1), state.range(0));  
        benchmark::DoNotOptimize(lanczos.get_spacing());  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_LanczosVelocityConstructor, double)->Arg(3)->Arg(6)->Arg(12)->Arg(18)->Arg(24)->Arg(30)->Arg(36)->Arg(42)->Arg(48)->Arg(96)->Arg(200)->Arg(400)->Arg(800)->Arg(1600)->Complexity();  
  
template<class Real>  
void BM_LanczosAcceleration(benchmark::State& state)  
{  
    std::vector<Real> v(state.range(0));  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = i;  
    }  
  
    using boost::math::differentiation::discrete_lanczos_derivative;  
    auto lanczos = discrete_lanczos_derivative<Real, 2>(Real(1));  
    for (auto _ : state)  
    {  
        auto w = lanczos(v);  
        benchmark::DoNotOptimize(w[0]);  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_LanczosAcceleration, double)->RangeMultiplier(2)->Range(1<<6, 1<<22)->Complexity(benchmark::oN);  
  
template<class Real>  
void BM_LanczosAccelerationPointEvaluation(benchmark::State& state)  
{  
    std::vector<Real> v(state.range(0));  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = i;  
    }  
  
    using boost::math::differentiation::discrete_lanczos_derivative;  
    auto lanczos = discrete_lanczos_derivative<Real, 2>(Real(1));  
    for (auto _ : state)  
    {  
        auto w = lanczos(v, v.size()/2);  
        benchmark::DoNotOptimize(w);  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_LanczosAccelerationPointEvaluation, double)->RangeMultiplier(2)->Range(1<<6, 1<<22)->Complexity();  
  
template<class Real>  
void BM_LanczosAccelerationConstructor(benchmark::State& state)  
{  
    using boost::math::differentiation::discrete_lanczos_derivative;  
    for (auto _ : state)  
    {  
        auto lanczos = discrete_lanczos_derivative<Real, 2>(Real(1), state.range(0));  
        benchmark::DoNotOptimize(lanczos.get_spacing());  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_LanczosAccelerationConstructor, double)->Arg(3)->Arg(6)->Arg(12)->Arg(18)->Arg(24)->Arg(30)->Arg(36)->Arg(42)->Arg(48)->Arg(96)->Arg(200)->Arg(400)->Arg(800)->Arg(1600)->Complexity();  
  
BENCHMARK_MAIN();  
```  
  
Results:  
  
  
```bash  
---------------------------------------------------------------------------------------------  
Benchmark                                                      Time           CPU Iterations  
---------------------------------------------------------------------------------------------  
BM_LanczosVelocity<double>/64                                793 ns        792 ns     837932  
BM_LanczosVelocity<double>/128                              1182 ns       1181 ns     566325  
BM_LanczosVelocity<double>/256                              1883 ns       1882 ns     348366  
BM_LanczosVelocity<double>/512                              3766 ns       3764 ns     173501  
BM_LanczosVelocity<double>/1024                             7475 ns       7471 ns      82524  
BM_LanczosVelocity<double>/2048                            15970 ns      15942 ns      46196  
BM_LanczosVelocity<double>/4096                            30077 ns      30062 ns      23320  
BM_LanczosVelocity<double>/8192                            58759 ns      58726 ns      10775  
BM_LanczosVelocity<double>/16384                          142639 ns     142369 ns       4914  
BM_LanczosVelocity<double>/32768                          327410 ns     326635 ns       2156  
BM_LanczosVelocity<double>/65536                          591048 ns     590672 ns       1157  
BM_LanczosVelocity<double>/131072                        1175491 ns    1172943 ns        548  
BM_LanczosVelocity<double>/262144                        2168417 ns    2166132 ns        318  
BM_LanczosVelocity<double>/524288                        4761735 ns    4755333 ns        132  
BM_LanczosVelocity<double>/1048576                       9784063 ns    9776449 ns         69  
BM_LanczosVelocity<double>/2097152                      20809915 ns   20791486 ns         35  
BM_LanczosVelocity<double>/4194304                      41776723 ns   41699385 ns         13  
BM_LanczosVelocity<double>_BigO                             9.91 N       9.89 N  
BM_LanczosVelocity<double>_RMS                                 5 %          4 %  
BM_LanczosVelocityPointEvaluation<double>/64                  10 ns         10 ns   69143997  
BM_LanczosVelocityPointEvaluation<double>/128                 15 ns         15 ns   69834739  
BM_LanczosVelocityPointEvaluation<double>/256                 10 ns         10 ns   67561046  
BM_LanczosVelocityPointEvaluation<double>/512                 10 ns         10 ns   60394809  
BM_LanczosVelocityPointEvaluation<double>/1024                10 ns         10 ns   66556373  
BM_LanczosVelocityPointEvaluation<double>/2048                10 ns         10 ns   69324777  
BM_LanczosVelocityPointEvaluation<double>/4096                10 ns         10 ns   68318677  
BM_LanczosVelocityPointEvaluation<double>/8192                10 ns         10 ns   68691428  
BM_LanczosVelocityPointEvaluation<double>/16384               10 ns         10 ns   68878655  
BM_LanczosVelocityPointEvaluation<double>/32768               10 ns         10 ns   69217838  
BM_LanczosVelocityPointEvaluation<double>/65536               10 ns         10 ns   66329963  
BM_LanczosVelocityPointEvaluation<double>/131072              10 ns         10 ns   69149462  
BM_LanczosVelocityPointEvaluation<double>/262144              10 ns         10 ns   60002400  
BM_LanczosVelocityPointEvaluation<double>/524288              10 ns         10 ns   66558905  
BM_LanczosVelocityPointEvaluation<double>/1048576             10 ns         10 ns   66723859  
BM_LanczosVelocityPointEvaluation<double>/2097152             10 ns         10 ns   65034608  
BM_LanczosVelocityPointEvaluation<double>/4194304             10 ns         10 ns   68759577  
BM_LanczosVelocityPointEvaluation<double>_BigO             10.50 (1)      10.49 (1)  
BM_LanczosVelocityPointEvaluation<double>_RMS                 11 %         11 %  
BM_LanczosVelocityConstructor<double>/3                     2714 ns       2709 ns     260195  
BM_LanczosVelocityConstructor<double>/6                     6091 ns       6087 ns     110642  
BM_LanczosVelocityConstructor<double>/12                   16161 ns      16150 ns      42783  
BM_LanczosVelocityConstructor<double>/18                   30807 ns      30797 ns      22534  
BM_LanczosVelocityConstructor<double>/24                   50395 ns      50364 ns      13575  
BM_LanczosVelocityConstructor<double>/30                   74744 ns      74689 ns       9285  
BM_LanczosVelocityConstructor<double>/36                  101279 ns     101153 ns       6863  
BM_LanczosVelocityConstructor<double>/42                  136337 ns     136139 ns       5166  
BM_LanczosVelocityConstructor<double>/48                  172611 ns     172471 ns       3989  
BM_LanczosVelocityConstructor<double>/96                  651954 ns     650503 ns       1063  
BM_LanczosVelocityConstructor<double>/200                3050902 ns    2932798 ns        253  
BM_LanczosVelocityConstructor<double>/400               10974438 ns   10907524 ns         63  
BM_LanczosVelocityConstructor<double>/800               45438448 ns   44583125 ns         16  
BM_LanczosVelocityConstructor<double>/1600             179085081 ns  178908250 ns          4  
BM_LanczosVelocityConstructor<double>_BigO                 70.01 N^2      69.87 N^2  
BM_LanczosVelocityConstructor<double>_RMS                      1 %          1 %  
BM_LanczosAcceleration<double>/64                            779 ns        779 ns     868292  
BM_LanczosAcceleration<double>/128                          1167 ns       1166 ns     594318  
BM_LanczosAcceleration<double>/256                          2163 ns       2161 ns     322487  
BM_LanczosAcceleration<double>/512                          4807 ns       4804 ns     139888  
BM_LanczosAcceleration<double>/1024                        10140 ns      10001 ns      68720  
BM_LanczosAcceleration<double>/2048                        20538 ns      20308 ns      35746  
BM_LanczosAcceleration<double>/4096                        38839 ns      38534 ns      18397  
BM_LanczosAcceleration<double>/8192                        75572 ns      75243 ns       9253  
BM_LanczosAcceleration<double>/16384                      178879 ns     178554 ns       3905  
BM_LanczosAcceleration<double>/32768                      417006 ns     403684 ns       1953  
BM_LanczosAcceleration<double>/65536                      875437 ns     840852 ns        970  
BM_LanczosAcceleration<double>/131072                    1514339 ns    1505888 ns        419  
BM_LanczosAcceleration<double>/262144                    4390533 ns    4037105 ns        237  
BM_LanczosAcceleration<double>/524288                    9746924 ns    9036975 ns         79  
BM_LanczosAcceleration<double>/1048576                  17388375 ns   16827500 ns         42  
BM_LanczosAcceleration<double>/2097152                  51284605 ns   43069125 ns         16  
BM_LanczosAcceleration<double>/4194304                  90444092 ns   82704000 ns          7  
BM_LanczosAcceleration<double>_BigO                        21.81 N      19.65 N  
BM_LanczosAcceleration<double>_RMS                            19 %         12 %  
BM_LanczosAccelerationPointEvaluation<double>/64              21 ns         17 ns   46782062  
BM_LanczosAccelerationPointEvaluation<double>/128             15 ns         14 ns   38458158  
BM_LanczosAccelerationPointEvaluation<double>/256             11 ns         11 ns   64732700  
BM_LanczosAccelerationPointEvaluation<double>/512             11 ns         11 ns   65421172  
BM_LanczosAccelerationPointEvaluation<double>/1024            11 ns         11 ns   65565786  
BM_LanczosAccelerationPointEvaluation<double>/2048            11 ns         11 ns   64442480  
BM_LanczosAccelerationPointEvaluation<double>/4096            11 ns         11 ns   65369853  
BM_LanczosAccelerationPointEvaluation<double>/8192            11 ns         11 ns   64637063  
BM_LanczosAccelerationPointEvaluation<double>/16384           11 ns         11 ns   62075999  
BM_LanczosAccelerationPointEvaluation<double>/32768           11 ns         11 ns   65037629  
BM_LanczosAccelerationPointEvaluation<double>/65536           11 ns         11 ns   64359531  
BM_LanczosAccelerationPointEvaluation<double>/131072          11 ns         11 ns   64479878  
BM_LanczosAccelerationPointEvaluation<double>/262144          11 ns         11 ns   64542898  
BM_LanczosAccelerationPointEvaluation<double>/524288          11 ns         11 ns   64466813  
BM_LanczosAccelerationPointEvaluation<double>/1048576         11 ns         11 ns   64774631  
BM_LanczosAccelerationPointEvaluation<double>/2097152         11 ns         11 ns   64812414  
BM_LanczosAccelerationPointEvaluation<double>/4194304         11 ns         11 ns   64435955  
BM_LanczosAccelerationPointEvaluation<double>_BigO         11.60 (1)      11.30 (1)  
BM_LanczosAccelerationPointEvaluation<double>_RMS             22 %         14 %  
BM_LanczosAccelerationConstructor<double>/3                 2882 ns       2875 ns     243280  
BM_LanczosAccelerationConstructor<double>/6                 6255 ns       6240 ns     110175  
BM_LanczosAccelerationConstructor<double>/12               15994 ns      15944 ns      43201  
BM_LanczosAccelerationConstructor<double>/18               31488 ns      31035 ns      23352  
BM_LanczosAccelerationConstructor<double>/24               51118 ns      50613 ns      13665  
BM_LanczosAccelerationConstructor<double>/30               76670 ns      75157 ns       8604  
BM_LanczosAccelerationConstructor<double>/36               96361 ns      96143 ns       7294  
BM_LanczosAccelerationConstructor<double>/42              124742 ns     124688 ns       5413  
BM_LanczosAccelerationConstructor<double>/48              161445 ns     161191 ns       4351  
BM_LanczosAccelerationConstructor<double>/96              596369 ns     594582 ns       1158  
BM_LanczosAccelerationConstructor<double>/200            2827832 ns    2772894 ns        264  
BM_LanczosAccelerationConstructor<double>/400            9969951 ns    9912125 ns         56  
BM_LanczosAccelerationConstructor<double>/800           40222987 ns   40050222 ns         18  
BM_LanczosAccelerationConstructor<double>/1600         159421210 ns  159332750 ns          4  
BM_LanczosAccelerationConstructor<double>_BigO             62.31 N^2      62.26 N^2  
BM_LanczosAccelerationConstructor<double>_RMS                  1 %          1 %  
```  
  
These numbers came out of `clang` with compiler flags `-march=native -O3 -ffast-math`.  
  
Here's the assembly with hotspots via perf:  
<img width="599" alt="screen shot 2019-01-26 at 8 17 58 pm" src="https://user-images.githubusercontent.com/5459618/51796686-61ca7980-21b4-11e9-86c0-eec9001bace9.png">

---

## Review 3 [Commented]

> Username: jzmaddock  
> Created_at: 2019-01-28 20:01:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/173#pullrequestreview-197231992  

📁 doc/differentiation/lanczos_smoothing.qbk

```diff
  29 |+ 
  30 |+         template<class RandomAccessContainer>
  31 |+         RandomAccessContainer operator()(RandomAccessContainer const & v) const;
```

> Username: jzmaddock  
> Created_at: 2019-01-28 20:01:07 UTC  
> Updated_at: 2019-01-28 21:06:28 UTC  
> Url: https://github.com/boostorg/math/pull/173#discussion_r251571554  

Good to see these work on any RandomAccessContainer - can we add a test case for something other than vector<> or else a compile-time concept check using a concept-archetype? I had hoped that Boost.Concepts would have an archetype already written but apparently not :(  
  
BTW the main reason for templating on the container type that I can see, is so that we can apply smoothing to a "view" of a much larger data set.  Idle thought - how much can work with Boost.Range?  Nothing that returns a container obviously, but maybe the rest can?

> Username: NAThompson  
> Created_at: 2019-01-28 21:11:28 UTC  
> Updated_at: 2019-01-28 21:11:29 UTC  
> Url: https://github.com/boostorg/math/pull/173#discussion_r251594602  

The reason I wanted to template on a `RandomAccessContainer` was only to make sure people didn't have to memcopy out of (say) an Eigen array, which is fairly pedestrian relative to your use. (BTW, I didn't want to put an Eigen dependency in the unit tests, so there is only a check on `std::array` and `boost::numeric::ublas::vector`.)  
  
As for the range access: The filters is pretty long (and gcc generates [bad assembly](https://stackoverflow.com/questions/54384807/slow-vpermpd-instruction-being-generated-why)), so using the indexing syntax `lanczos(v, i)` and looping over it is not that much more expensive than populating the entire vector via `lanczos(v, dvdt)`. That's why I didn't add a `lanczos(v.begin(), v.end(), dvdt.begin())`-style call.

> Username: NAThompson  
> Created_at: 2019-01-28 21:16:35 UTC  
> Url: https://github.com/boostorg/math/pull/173#discussion_r251596356  

Note that if the range-style call is desirable, it's easily added and I'm glad to do it.

> Username: jzmaddock  
> Created_at: 2019-01-29 08:55:47 UTC  
> Url: https://github.com/boostorg/math/pull/173#discussion_r251739963  

Actually I wsn't suggesting changing the call: What I meant was we have Boost.Range which implements a subset of a (read-only) containers members and internally is just a wrapper around a pair of iterators  (https://www.boost.org/doc/libs/1_54_0/libs/range/doc/html/range/reference/utilities/iterator_range.html).  I think ranges are promised as a first-class-citizen for C++20 as well.  So I guess what I was wondering was whether:  
  
```  
my_smoother(boost::make_iterator_range(x, y), my_container);  
```  
  
would work.  It would need a decoupling of the type of the 2 arguments, but a quick scan of your code looked like it might all "just work"?  
  
Mostly thinking out loud yours....

> Username: NAThompson  
> Created_at: 2019-01-29 18:36:11 UTC  
> Url: https://github.com/boostorg/math/pull/173#discussion_r251962173  

Strangely, the iterator ranges have a free function `size()`, but it doesn't work as expected:  
  
```bash  
In file included from lanczos_smoothing_test.cpp:16:  
../../../boost/math/differentiation/lanczos_smoothing.hpp:500:49: fatal error: call to 'size' is ambiguous  
            for(size_t i = m_f.size() - 1; i <= size(v) - m_f.size(); ++i)  
                                                ^~~~  
lanczos_smoothing_test.cpp:669:5: note: in instantiation of function template specialization 'boost::math::differentiation::discrete_lanczos_derivative<double, 1>::operator()<boost::iterator_range<double *> >' requested here  
    lanczos(v1, v2);  
    ^  
lanczos_smoothing_test.cpp:701:5: note: in instantiation of function template specialization 'test_data_representations<double>' requested here  
    test_data_representations<double>();  
    ^  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/include/c++/v1/iterator:1832:16: note: candidate function [with _Cont = boost::iterator_range<double *>]  
constexpr auto size(const _Cont& __c)  
               ^  
../../../boost/range/size.hpp:55:5: note: candidate function [with SinglePassRange = boost::iterator_range<double *>]  
    size(const SinglePassRange& rng)  
    ^  
1 error generated.  
```  
  
If I change all the free size functions to `v.size()`, then it works. I had some reason, which I currently do not recall for using `using std::size; size(v)` rather than `v.size()`.  
  
I'm gonna look up why std::size exists and why I was using it.


---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2019-01-28 20:03:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/173#pullrequestreview-197233177  

📁 doc/differentiation/lanczos_smoothing.qbk

```diff
 104 |+ for SNR = 10, p = n/7 was the best, and for SNR = 100, p = n/6 was the most reasonable choice.
 105 |+ For SNR = 0.1, the method appears to be useless.
 106 |+ The user is urged to use these results with caution-they have no theoretical backing and are extrapolated from a single case.
```

> Username: jzmaddock  
> Created_at: 2019-01-28 20:03:51 UTC  
> Updated_at: 2019-01-28 21:06:28 UTC  
> Url: https://github.com/boostorg/math/pull/173#discussion_r251572503  

Nitpick: There's a few places where you use a hyphen with no space around it which looks like a single hyphenated word to me rather than a separator between clauses.  Should be a colon maybe?  My grammatical abilities are severely limited so take my comments with a pinch of salt! ;)

> Username: NAThompson  
> Created_at: 2019-01-28 21:06:38 UTC  
> Url: https://github.com/boostorg/math/pull/173#discussion_r251592776  

That indeed should be a colon. My grammatical training in English ended in the 6th grade, so I'm just going with what sounds best. (The other hyphen in `The filters are (regrettably) computed at runtime-the vast number of combinations` sounds good to me.)


---

## Comment 5

> Username: jzmaddock  
> Created_at: 2019-01-29 08:57:21 UTC  
> Url: https://github.com/boostorg/math/pull/173#issuecomment-458458804  

One other minor documentation nitpick: can you document the C++ std level required if it's not C++03?  
  
And now I'll shut up I promise ;)

---

## Comment 6

> Username: NAThompson  
> Created_at: 2019-01-29 19:47:56 UTC  
> Url: https://github.com/boostorg/math/pull/173#issuecomment-458681457  

Nice catch on the iterator ranges; it was just a few lines to make it work with this additional datatype.

---
