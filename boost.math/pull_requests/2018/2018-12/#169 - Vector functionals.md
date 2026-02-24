# #169 Vector functionals [Merged]

> Username: NAThompson  
> Created at: 2018-12-08 01:45:34 UTC  
> Updated at: 2019-03-03 00:15:19 UTC  
> Merged at: 2019-01-21 19:48:50 UTC  
> Closed at: 2019-01-21 19:48:50 UTC  
> Url: https://github.com/boostorg/math/pull/169  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2018-12-08 11:59:13 UTC  
> Url: https://github.com/boostorg/math/pull/169#issuecomment-445453962  

Looks reasonable to me, my only comments are of a slightly bikeshedding nature:  
* I'm not sure about the title of these, are they not reduction algorithms?  
* I wonder if we should overload these to accept any iterable object as well as iterator ranges?  
```  
template<class Sequence>  
    auto mean(const Sequence& s) { return mean(s.begin(), s.end()); }  
```  
* To me "vector" algorithms imply some sort of accelerated code via SMID, blas, cuda or whatever.  Would any of these benefit from these?

---

## Comment 2

> Username: pabristow  
> Created_at: 2018-12-08 13:01:36 UTC  
> Url: https://github.com/boostorg/math/pull/169#issuecomment-445457535  

All looks very useful to me.  
  
Perhaps it is time for testing by a ‘useful idiot’ ;-)  
  
Docs might be a bit mathy for the hoi polloi?  
  
I wonder if it will/should cope with warning that the mean, for example, may not be a informative number, for example when you have a lot of noisy observations, but one outlier massively different.  
  
There are methods of detecting this I seem to remember, and perhaps better than comparing the mean to the slow sorting median that could be time-consuming to discover that it is GIGO again?  
  
Paul  
  
From: Nick [mailto:notifications@github.com]  
Sent: 08 December 2018 01:46  
To: boostorg/math  
Cc: Subscribed  
Subject: [boostorg/math] Sequence tools (#169)  
  
________________________________  
You can view, comment on, or merge this pull request online at:  
  
  https://github.com/boostorg/math/pull/169  
  
Commit Summary  
  
  *   Vector functionals, first pass [CI SKIP]  
  *   Hoyer sparsity [CI SKIP]  
  *   Merge branch 'develop' into sequence_tools  
  *   L1 and L2 norms. [CI SKIP]  
  *   Add Shannon entropy and kick off build.  
  
File Changes  
  
  *   M doc/math.qbk<https://github.com/boostorg/math/pull/169/files#diff-0> (4)  
  *   A doc/vector_functionals/vector_functionals.qbk<https://github.com/boostorg/math/pull/169/files#diff-1> (279)  
  *   A include/boost/math/tools/vector_functionals.hpp<https://github.com/boostorg/math/pull/169/files#diff-2> (387)  
  *   M test/Jamfile.v2<https://github.com/boostorg/math/pull/169/files#diff-3> (1)  
  *   A test/vector_functionals_test.cpp<https://github.com/boostorg/math/pull/169/files#diff-4> (594)  
  
Patch Links:  
  
  *   https://github.com/boostorg/math/pull/169.patch  
  *   https://github.com/boostorg/math/pull/169.diff  
  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/math/pull/169>, or mute the thread<https://github.com/notifications/unsubscribe-auth/ABBuAfeOr1hKRQJxJGRlRXX7YEv4kXIFks5u2xm-gaJpZM4ZJZYr>.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2018-12-08 18:12:12 UTC  
> Url: https://github.com/boostorg/math/pull/169#issuecomment-445478600  

> I'm not sure about the title of these, are they not reduction algorithms?  
  
I really don't like the title either. They are reduction algorithms; will that language be preferable? Or they could be split into `vector_statistics`, `vector_norms`, and `vector_functionals`, which would be a catchall for those algorithms which don't fit cleanly in the first two categories. The number of algorithms that could be put into `vector_statistics` could grow without bound.  
  
> I wonder if we should overload these to accept any iterable object as well as iterator ranges?  
  
Yes, done.  
  
> To me "vector" algorithms imply some sort of accelerated code via SMID, blas, cuda or whatever. Would any of these benefit from these?  
  
I checked godbolt for generation of vector instructions. It's super painful since you kinda have to pare down the code and get rid of all the dependencies, but AVX-256 instructions are certainly generated and I think I could get AVX-512 generation if I played some more with compiler flags. So SIMD is already taken care of just by compiler tech.  
  
CUDA is an interesting question. It could certainly be useful, but I don't actively program on CUDA, so I'm not very good at it and will probably write a bunch of garbage. I also would need to read the NVIDIA docs to make sure we aren't duplicating the functionality in (say) cublas.  
  
> Perhaps it is time for testing by a ‘useful idiot’ ;-)  
  
I posted this on [codereview](https://codereview.stackexchange.com/questions/209253/a-collection-of-vector-functionals/209279) and it turns out that these functions are useful for image processing as well, where they use 8 bit integers, and in 8 bit arithmetic most of these are totally broken. I'm trying to decide if it's worth supporting that use case. . . .  
  
> Docs might be a bit mathy for the hoi polloi?  
  
Y'all set the tone. :)

---

## Comment 4

> Username: NAThompson  
> Created_at: 2018-12-08 20:16:27 UTC  
> Url: https://github.com/boostorg/math/pull/169#issuecomment-445486778  

> my only comments are of a slightly bikeshedding nature  
  
I always hate it when imprecise language is chosen and gets locked into code forever, so IMO it's far from bikeshedding. How about the following:  
  
- `descriptive_statistics.hpp`, which will have mean, variance, median, Gini coefficient, Hoyer sparsity, Shannon entropy, and Shannon cost. A time goes on, maybe build it out to include skewness, covariance, kurtosis, correlation coefficient, quantiles. Another option is to further split this into `univariate_statistics.hpp` and `bivariate_statistics.hpp`.  
- `norms.hpp` which will have l1, l2, lp, l0, sup, total variation, as well as `l2_distance(u, v)` and so on.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2018-12-10 18:25:05 UTC  
> Url: https://github.com/boostorg/math/pull/169#issuecomment-445919426  

>I always hate it when imprecise language is chosen and gets locked into code forever, so IMO it's far from bikeshedding. How about the following:  
  
>    descriptive_statistics.hpp, which will have mean, variance, median, Gini coefficient, Hoyer sparsity, Shannon entropy, and Shannon cost. A time goes on, maybe build it out to include skewness, covariance, kurtosis, correlation coefficient, quantiles. Another option is to further split this into univariate_statistics.hpp and bivariate_statistics.hpp.  
  
I rather like univariate_statistics.hpp/bivariate_statistics.hpp  
  
>norms.hpp which will have l1, l2, lp, l0, sup, total variation, as well as l2_distance(u, v) and so on.  
  
Also sounds good.

---

## Review 6 [Commented]

> Username: pabristow  
> Created_at: 2018-12-11 14:27:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/169#pullrequestreview-183702509  

Is this is right moment to raise the issue of 'what to do when things go wrong'?  
  
I see you have issued a warning message, which is good, for example, for not enough values, but should there be a way of controlling more finely what happens, perhaps using John's 'messy to program, but user-friendly', policies?  Policies allow ignoring errors, returning NaN or inf, or throwing.  Need more examples to help the users use it but not too much work.

---

## Comment 7

> Username: NAThompson  
> Created_at: 2018-12-11 16:35:01 UTC  
> Url: https://github.com/boostorg/math/pull/169#issuecomment-446269457  

It seems to me that most of these are so simple that the error communication built into IEEE floating point is sufficient. I haven't typed `throw` in any of these yet, so I think one of the main motivations for using policies is obviated. I don't object to adding policies, but the failure modes are much more complex and variegated for special function evaluation than summary statistics.  
  
As an aside, do we have any telemetry on how often policies are used outside of boost.math? I searched `boost::math::policies` on github, but didn't see any way to filter out the vendorizing commits as well as the boost.math commits.

---

## Comment 8

> Username: pabristow  
> Created_at: 2018-12-12 10:31:04 UTC  
> Url: https://github.com/boostorg/math/pull/169#issuecomment-446539374  

From: Nick [mailto:notifications@github.com]  
Sent: 11 December 2018 16:35  
To: boostorg/math  
Cc: Paul A. Bristow; Comment  
Subject: Re: [boostorg/math] Vector functionals (#169)  
  
  
It seems to me that most of these are so simple that the error communication built into IEEE floating point is sufficient. I haven't typed throw in any of these yet, so I think one of the main motivations for using policies is obviated. I don't object to adding policies, but the failure modes are much more complex and variegated for special function evaluation than summary statistics.  
  
I don’t have strong views but was just raising the issue for consideration.  
  
Many of these functions will be used by ‘amateurs’ and there is a danger of outliers giving misleading or at least meaningless results, despite being mathematically correct.  
  
But at least you have provided some messages.  
  
As an aside, do we have any telemetry on how often policies are used outside of boost.math? I searched boost::math::policies on github, but didn't see any way to filter out the vendorizing commits as well as the boost.math commits.  
  
I don’t think John’s concept has caught on, even within Boost, and many people fail to enclose their stuff in try’n’catch blocks, so fail to even see the messages ☹  
  
But policies works well for me and the messages are good.  So we tried.

---

## Comment 9

> Username: NAThompson  
> Created_at: 2018-12-12 18:51:29 UTC  
> Url: https://github.com/boostorg/math/pull/169#issuecomment-446700387  

> I don’t think John’s concept has caught on, even within Boost, and many people fail to enclose their stuff in try’n’catch blocks, so fail to even see the messages ☹   
  
I think I should recant my implication that if something isn't commonly used, then it shouldn't be done. Certainly there are use cases where throws are totally unacceptable and if there's no way to disable them then the only option is an in-house rewrite. However, I still would like to know how to figure out what features are being used and where.  
  
> Many of these functions will be used by ‘amateurs’ and there is a danger of outliers giving misleading or at least meaningless results, despite being mathematically correct.  
  
I'm not sure what how we can mitigate this problem at our level. Careful documentation is the only thing I can think of.

---

## Review 10 [Commented]

> Username: pabristow  
> Created_at: 2018-12-13 09:58:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/169#pullrequestreview-184580484  

Very useful!

---

## Comment 11

> Username: NAThompson  
> Created_at: 2018-12-17 22:23:49 UTC  
> Url: https://github.com/boostorg/math/pull/169#issuecomment-448021856  

Ok, I think this is done.

---

## Comment 12

> Username: pabristow  
> Created_at: 2018-12-18 09:32:13 UTC  
> Url: https://github.com/boostorg/math/pull/169#issuecomment-448155478  

All looks a useful addition.  I hope to try it out when I solve a mot annoying hardware problem on a new machine.

---

## Comment 13

> Username: NAThompson  
> Created_at: 2018-12-27 01:32:44 UTC  
> Url: https://github.com/boostorg/math/pull/169#issuecomment-450051817  

>  I hope to try it out when I solve a mot annoying hardware problem on a new machine.  
  
Does your machine have the fused-multiply add instruction? If so I'd like to see if MSVC generates it in the asm from the `std::fma`.

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2018-12-27 11:53:43 UTC  
> Url: https://github.com/boostorg/math/pull/169#issuecomment-450137221  

I was expecting something a lot simpler, but std::fma resolves to a function call and while the inside of the function is complex, ultimately I see it calling vfmadd213sd which is one of Intel's FMA instructions.  This is on x64, msvc.  I see the same instruction called with Win32 builds too.  I suspect there may be some complicated runtime-selection logic in there to select the implementation depending on the hardware, but I can't be certain.    
  
HTH, John.

---

## Comment 15

> Username: NAThompson  
> Created_at: 2018-12-27 16:45:38 UTC  
> Updated_at: 2018-12-27 17:19:19 UTC  
> Url: https://github.com/boostorg/math/pull/169#issuecomment-450188220  

>  I suspect there may be some complicated runtime-selection logic in there to select the implementation depending on the hardware, but I can't be certain.  
  
If you compile with (say) `-march=haswell` or whatever your chip architecture is, will it elide all the runtime selection logic?  
  
g++-8 does exactly what I'd hoped for: For example if `fma.cpp` contains  
  
```cpp  
#include <cmath>  
  
int main()  
{  
    double a = 7;  
    double b = 2.3;  
    double c = 4.6;  
    double d = std::fma(a, b, c);  
    return (int) d;  
}  
```  
  
then compiling with `g++-8 -S -fverbose-asm -march=knl fma.cpp` gives  
  
```asm  
LCFI1:  
# fma.cpp:5:     double a = 7;  
	vmovsd	lC0(%rip), %xmm0	#, tmp89  
	vmovsd	%xmm0, -8(%rbp)	# tmp89, a  
# fma.cpp:6:     double b = 2.3;  
	vmovsd	lC1(%rip), %xmm0	#, tmp90  
	vmovsd	%xmm0, -16(%rbp)	# tmp90, b  
# fma.cpp:7:     double c = 4.6;  
	vmovsd	lC2(%rip), %xmm0	#, tmp91  
	vmovsd	%xmm0, -24(%rbp)	# tmp91, c  
# fma.cpp:8:     double d = std::fma(a, b, c);  
	vmovsd	-8(%rbp), %xmm0	# a, tmp92  
	vmovsd	-24(%rbp), %xmm1	# c, tmp96  
	vfmadd132sd	-16(%rbp), %xmm1, %xmm0	# b, tmp96, tmp92  
	vmovsd	%xmm0, -32(%rbp)	# tmp92, d  
```  
  
and if I drop the `-march=knl` I get the function call:  
  
```asm  
# fma.cpp:8:     double d = std::fma(a, b, c);  
	movsd	-24(%rbp), %xmm1	# c, tmp92  
	movsd	-16(%rbp), %xmm0	# b, tmp93  
	movq	-8(%rbp), %rax	# a, tmp94  
	movapd	%xmm1, %xmm2	# tmp92,  
	movapd	%xmm0, %xmm1	# tmp93,  
	movq	%rax, %xmm0	# tmp94,  
	call	_fma	#  
	movq	%xmm0, %rax	#, tmp95  
	movq	%rax, -32(%rbp)	# tmp95, d  
```  
  
Also, what's y'alls opinion on the language? Is `solve_quadratic` correct or do you like `quadratic` or `quadratic_roots`?

---

## Comment 16

> Username: jzmaddock  
> Created_at: 2018-12-27 18:35:59 UTC  
> Url: https://github.com/boostorg/math/pull/169#issuecomment-450206739  

>If you compile with (say) -march=haswell or whatever your chip architecture is, will it elide all the runtime selection logic?  
  
No, because msvc has no such switch, and std::fma always resolves to a function call into the (closed source/black box) std lib.  GCC does what one would hope it would do and expands it as an inline intrinsic.  
  
>Also, what's y'alls opinion on the language? Is solve_quadratic correct or do you like quadratic or quadratic_roots?  
  
Weakly for quadratic_roots, not much in it though.  Paul?

---

## Comment 17

> Username: NAThompson  
> Created_at: 2018-12-27 19:38:35 UTC  
> Url: https://github.com/boostorg/math/pull/169#issuecomment-450216900  

> No, because msvc has no such switch, and std::fma always resolves to a function call into the (closed source/black box) std lib.  
  
I guess in that sense `std::fma` might make the performance *worse*. If instead I wrote `T c = a*x+b`, then that'd be equivalent to saying "please generate an inline fma instruction here!" Whether or not that actually happens is of course up to the compiler. Perhaps I should just trust that the Microsoft team did their due diligence and their runtime selection logic is sufficiently fast.  
  
We could go the route of using [intel fma intrinsics](https://software.intel.com/sites/landingpage/IntrinsicsGuide/#techs=FMA) but I think it might be for the best to just stop agonizing.

---

## Comment 18

> Username: pabristow  
> Created_at: 2018-12-30 15:02:27 UTC  
> Url: https://github.com/boostorg/math/pull/169#issuecomment-450566333  

From: jzmaddock [mailto:notifications@github.com]  
Sent: 27 December 2018 18:36  
To: boostorg/math  
Cc: Paul A. Bristow; Comment  
Subject: Re: [boostorg/math] Vector functionals (#169)  
  
  
If you compile with (say) -march=haswell or whatever your chip architecture is, will it elide all the runtime selection logic?  
  
No, because msvc has no such switch, and std::fma always resolves to a function call into the (closed source/black box) std lib. GCC does what one would hope it would do and expands it as an inline intrinsic.  
  
Also, what's y'alls opinion on the language? Is solve_quadratic correct or do you like quadratic or quadratic_roots?  
  
Weakly for quadratic_roots, not much in it though. Paul?  
  
Sorry for late reply – overrun by visitors at present, and probably about to fall visit to their viruses!  
  
I suspect people will be searching for quadratic first?  
  
So I feel that quadratic should come first.  
  
But might also be searching for solve (or even roots), so no name is certain to get you to where you are searching for. That’s where indexes are good, but people don’t expect to find them or use them in this googling era ☹  
  
As a non-mathy man, I don’t like ‘roots’ – sounds a bit horticultural ;-)  
  
But then we have a whole section on root finding already.  Should add these function to this section, or at least cross reference?  
  
Will we also be adding specific cubic and quartic solve later?  In which case quadratic_solve, cubic_solve… might be better?  
  
Will look at the whole code when things have quietened down here ☺  
  
Cheers  
  
Paul  
  
  
  
—  
You are receiving this because you commented.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/math/pull/169#issuecomment-450206739>, or mute the thread<https://github.com/notifications/unsubscribe-auth/ABBuAT5tptDQS8X8at7auIKmtJWG7kM0ks5u9RMPgaJpZM4ZJZYr>.

---

## Comment 19

> Username: NAThompson  
> Created_at: 2018-12-30 19:08:32 UTC  
> Url: https://github.com/boostorg/math/pull/169#issuecomment-450580519  

> Will we also be adding specific cubic and quartic solve later? In which case quadratic_solve, cubic_solve… might be better?  
  
Good question. I've never once had cause to numerically solve a quartic, and only once had cause to numerically solve a cubic. So to me it's not a high priority, especially since we can extract all the roots via Newton's method + polynomial division. But more importantly, I think the reason why we need the quadratic solver in Boost is because there are very subtle floating point issues which *can* be resolved via various tricks. But I googled around and couldn't find any research attacking the same problems for cubic and quartics. So we don't have a "take" which would make our implementation any better than what you'd just write down naively from (say) Viete's formula.

---

## Comment 20

> Username: jzmaddock  
> Created_at: 2019-01-17 18:58:18 UTC  
> Url: https://github.com/boostorg/math/pull/169#issuecomment-455288898  

This all looks good to go to me - my only query is have you tried comparing performance with the Accumulators library?  I'm assuming that a one-shot / single pass algorithm should be faster than the (more flexible) restart-able accumulators algorithms?

---

## Comment 21

> Username: NAThompson  
> Created_at: 2019-01-21 16:51:25 UTC  
> Url: https://github.com/boostorg/math/pull/169#issuecomment-456137987  

```cpp  
#include <benchmark/benchmark.h>  
#include <array>  
#include <random>  
#include <iostream>  
#include <algorithm>  
#include <boost/accumulators/accumulators.hpp>  
#include <boost/accumulators/statistics.hpp>  
#include <boost/math/tools/univariate_statistics.hpp>  
#include <boost/math/tools/bivariate_statistics.hpp>  
#include <boost/math/tools/signal_statistics.hpp>  
#include <boost/math/tools/norms.hpp>  
  
  
template<class Real>  
void BM_HoyerSparsity(benchmark::State& state)  
{  
    std::vector<Real> v(state.range(0));  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = i;  
    }  
  
    for (auto _ : state)  
    {  
        auto hs = boost::math::tools::hoyer_sparsity(v);  
        benchmark::DoNotOptimize(hs);  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_HoyerSparsity, double)->RangeMultiplier(2)->Range(1<<2, 1<<15)->Complexity(benchmark::oN);  
  
template<class Real>  
void BM_Median(benchmark::State& state)  
{  
    std::vector<Real> v(state.range(0));  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = i;  
    }  
  
    for (auto _ : state)  
    {  
        auto med = boost::math::tools::median(v);  
        benchmark::DoNotOptimize(med);  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_Median, double)->RangeMultiplier(2)->Range(1<<2, 1<<15)->Complexity();  
  
  
template<class Real>  
void BM_MedianWithShuffle(benchmark::State& state)  
{  
    std::vector<Real> v(state.range(0));  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = i;  
    }  
  
    std::random_device rd;  
    std::mt19937 g(rd());  
  
    for (auto _ : state)  
    {  
        std::shuffle(v.begin(), v.end(), g);  
        auto med = boost::math::tools::median(v);  
        benchmark::DoNotOptimize(med);  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_MedianWithShuffle, double)->RangeMultiplier(2)->Range(1<<2, 1<<15)->Complexity();  
  
  
template<class Real>  
void BM_Covariance(benchmark::State& state)  
{  
    std::vector<Real> v(state.range(0));  
    std::vector<Real> u(state.range(0));  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = i;  
        u[i] = -i;  
    }  
  
    for (auto _ : state)  
    {  
        auto cov = boost::math::tools::covariance(u, v);  
        benchmark::DoNotOptimize(cov);  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_Covariance, double)->RangeMultiplier(2)->Range(1<<2, 1<<15)->Complexity(benchmark::oN);  
  
  
template<class Real>  
void BM_Mean(benchmark::State& state)  
{  
    std::vector<Real> v(state.range(0));  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = i;  
    }  
  
    for (auto _ : state)  
    {  
        benchmark::DoNotOptimize(boost::math::tools::mean(v));  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_Mean, double)->RangeMultiplier(2)->Range(1<<5, 1<<24)->Complexity(benchmark::oN);  
  
template<class Real>  
void BM_MeanAccumulator(benchmark::State& state)  
{  
    using namespace boost::accumulators;  
    std::vector<Real> v(state.range(0));  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = i;  
    }  
  
    for (auto _ : state)  
    {  
        accumulator_set<Real, stats<tag::mean(immediate)>> acc;  
        acc = std::for_each(v.begin(), v.end(), acc);  
        benchmark::DoNotOptimize(mean(acc));  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_MeanAccumulator, double)->RangeMultiplier(2)->Range(1<<5, 1<<24)->Complexity(benchmark::oN);  
  
  
template<class Real>  
void BM_Variance(benchmark::State& state)  
{  
    std::vector<Real> v(state.range(0));  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = i;  
    }  
  
    for (auto _ : state)  
    {  
        benchmark::DoNotOptimize(boost::math::tools::variance(v.begin(), v.end()));  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_Variance, double)->RangeMultiplier(2)->Range(1<<2, 1<<20)->Complexity(benchmark::oN);  
  
  
template<class Real>  
void BM_Skewness(benchmark::State& state)  
{  
    std::vector<Real> v(state.range(0));  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = i;  
    }  
  
    for (auto _ : state)  
    {  
        benchmark::DoNotOptimize(boost::math::tools::skewness(v.begin(), v.end()));  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_Skewness, double)->RangeMultiplier(2)->Range(1<<2, 1<<20)->Complexity(benchmark::oN);  
  
template<class Real>  
void BM_Kurtosis(benchmark::State& state)  
{  
    std::vector<Real> v(state.range(0));  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = i;  
    }  
  
    for (auto _ : state)  
    {  
        benchmark::DoNotOptimize(boost::math::tools::kurtosis(v.begin(), v.end()));  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_Kurtosis, double)->RangeMultiplier(2)->Range(1<<2, 1<<20)->Complexity(benchmark::oN);  
  
  
template<class Real>  
void BM_L1(benchmark::State& state)  
{  
    std::vector<Real> v(state.range(0));  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = i;  
    }  
  
    for (auto _ : state)  
    {  
        benchmark::DoNotOptimize(boost::math::tools::l1_norm(v.begin(), v.end()));  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_L1, double)->RangeMultiplier(2)->Range(1<<2, 1<<15)->Complexity(benchmark::oN);  
  
template<class Real>  
void BM_L2(benchmark::State& state)  
{  
    std::vector<Real> v(state.range(0));  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = i;  
    }  
  
    for (auto _ : state)  
    {  
        benchmark::DoNotOptimize(boost::math::tools::l2_norm(v.begin(), v.end()));  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_L2, double)->RangeMultiplier(2)->Range(1<<2, 1<<15)->Complexity(benchmark::oN);  
  
template<class Real>  
void BM_L3(benchmark::State& state)  
{  
    std::vector<Real> v(state.range(0));  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = i;  
    }  
  
    for (auto _ : state)  
    {  
        benchmark::DoNotOptimize(boost::math::tools::lp_norm(v.begin(), v.end(), 3));  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_L3, double)->RangeMultiplier(2)->Range(1<<2, 1<<15)->Complexity(benchmark::oN);  
  
template<class Real>  
void BM_TV(benchmark::State& state)  
{  
    std::vector<Real> v(state.range(0));  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = i;  
    }  
  
    for (auto _ : state)  
    {  
        benchmark::DoNotOptimize(boost::math::tools::total_variation(v.begin(), v.end()));  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_TV, double)->RangeMultiplier(2)->Range(1<<2, 1<<15)->Complexity(benchmark::oN);  
  
template<class Real>  
void BM_SupNorm(benchmark::State& state)  
{  
    std::vector<Real> v(state.range(0));  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = i;  
    }  
  
    for (auto _ : state)  
    {  
        benchmark::DoNotOptimize(boost::math::tools::sup_norm(v.begin(), v.end()));  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_SupNorm, double)->RangeMultiplier(2)->Range(1<<2, 1<<15)->Complexity(benchmark::oN);  
  
template<class Real>  
void BM_Gini(benchmark::State& state)  
{  
    std::vector<Real> v(state.range(0));  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = i;  
    }  
  
    for (auto _ : state)  
    {  
        benchmark::DoNotOptimize(boost::math::tools::gini_coefficient(v.begin(), v.end()));  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_Gini, double)->RangeMultiplier(2)->Range(1<<2, 1<<15)->Complexity();  
  
  
template<class Real>  
void BM_GiniUnsorted(benchmark::State& state)  
{  
    std::vector<Real> v(state.range(0));  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = i;  
    }  
    std::random_device rd;  
    std::mt19937 g(rd());  
  
    for (auto _ : state)  
    {  
        std::shuffle(v.begin(), v.end(), g);  
        benchmark::DoNotOptimize(boost::math::tools::gini_coefficient(v.begin(), v.end()));  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_GiniUnsorted, double)->RangeMultiplier(2)->Range(1<<2, 1<<15)->Complexity();  
  
template<class Real>  
void BM_Shuffle(benchmark::State& state)  
{  
    std::vector<Real> v(state.range(0));  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = i;  
    }  
    std::random_device rd;  
    std::mt19937 g(rd());  
  
    for (auto _ : state)  
    {  
        std::shuffle(v.begin(), v.end(), g);  
        benchmark::DoNotOptimize(v[0]);  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BM_Shuffle, double)->RangeMultiplier(2)->Range(1<<2, 1<<15)->Complexity();  
  
  
BENCHMARK_MAIN();  
```  
  
Results:  
  
```bash  
---------------------------------------------------------------------------  
Benchmark                                    Time           CPU Iterations  
---------------------------------------------------------------------------  
BM_HoyerSparsity<double>/4                   7 ns          7 ns   86577945  
BM_HoyerSparsity<double>/8                   9 ns          9 ns   71843504  
BM_HoyerSparsity<double>/16                 16 ns         16 ns   44655958  
BM_HoyerSparsity<double>/32                 31 ns         31 ns   22554308  
BM_HoyerSparsity<double>/64                 61 ns         61 ns   11553443  
BM_HoyerSparsity<double>/128               123 ns        123 ns    5655012  
BM_HoyerSparsity<double>/256               247 ns        247 ns    2841267  
BM_HoyerSparsity<double>/512               495 ns        495 ns    1405112  
BM_HoyerSparsity<double>/1024              992 ns        991 ns     704048  
BM_HoyerSparsity<double>/2048             1985 ns       1984 ns     352931  
BM_HoyerSparsity<double>/4096             3983 ns       3976 ns     173433  
BM_HoyerSparsity<double>/8192             7938 ns       7935 ns      87999  
BM_HoyerSparsity<double>/16384           16014 ns      15980 ns      44111  
BM_HoyerSparsity<double>/32768           34182 ns      33583 ns      20654  
BM_HoyerSparsity<double>_BigO             1.03 N       1.01 N   
BM_HoyerSparsity<double>_RMS                 6 %          5 %   
BM_Median<double>/4                         23 ns         23 ns   28614409  
BM_Median<double>/8                         38 ns         37 ns   18352954  
BM_Median<double>/16                        35 ns         35 ns   19967254  
BM_Median<double>/32                        61 ns         59 ns   12112822  
BM_Median<double>/64                       110 ns        104 ns    6764200  
BM_Median<double>/128                      193 ns        190 ns    3516863  
BM_Median<double>/256                      377 ns        365 ns    2094253  
BM_Median<double>/512                      671 ns        652 ns    1183572  
BM_Median<double>/1024                    1207 ns       1184 ns     571835  
BM_Median<double>/2048                    2215 ns       2194 ns     317099  
BM_Median<double>/4096                    4219 ns       4180 ns     168758  
BM_Median<double>/8192                    8733 ns       8654 ns      80815  
BM_Median<double>/16384                  20252 ns      18742 ns      40817  
BM_Median<double>/32768                  34000 ns      33910 ns      20412  
BM_Median<double>_BigO                    1.08 N       1.06 N   
BM_Median<double>_RMS                       15 %          9 %   
BM_MedianWithShuffle<double>/4             151 ns        150 ns    4632694  
BM_MedianWithShuffle<double>/8             389 ns        389 ns    1797725  
BM_MedianWithShuffle<double>/16            886 ns        875 ns     820373  
BM_MedianWithShuffle<double>/32           1792 ns       1785 ns     390059  
BM_MedianWithShuffle<double>/64           3599 ns       3573 ns     199886  
BM_MedianWithShuffle<double>/128          6997 ns       6969 ns     100439  
BM_MedianWithShuffle<double>/256         13771 ns      13739 ns      51377  
BM_MedianWithShuffle<double>/512         27656 ns      27514 ns      25525  
BM_MedianWithShuffle<double>/1024        55099 ns      54747 ns      12824  
BM_MedianWithShuffle<double>/2048       110696 ns     110004 ns       6517  
BM_MedianWithShuffle<double>/4096       218770 ns     216876 ns       3280  
BM_MedianWithShuffle<double>/8192       424385 ns     423896 ns       1630  
BM_MedianWithShuffle<double>/16384      845428 ns     844233 ns        821  
BM_MedianWithShuffle<double>/32768     1691344 ns    1688892 ns        417  
BM_MedianWithShuffle<double>_BigO        51.65 N      51.57 N   
BM_MedianWithShuffle<double>_RMS             1 %          1 %   
BM_Covariance<double>/4                     13 ns         13 ns   53263127  
BM_Covariance<double>/8                     33 ns         33 ns   21377310  
BM_Covariance<double>/16                    73 ns         73 ns    9618952  
BM_Covariance<double>/32                   175 ns        175 ns    3986832  
BM_Covariance<double>/64                   386 ns        384 ns    1828493  
BM_Covariance<double>/128                  806 ns        802 ns     875164  
BM_Covariance<double>/256                 1633 ns       1631 ns     429595  
BM_Covariance<double>/512                 3300 ns       3297 ns     211489  
BM_Covariance<double>/1024                6708 ns       6670 ns     105705  
BM_Covariance<double>/2048               13362 ns      13326 ns      52666  
BM_Covariance<double>/4096               26825 ns      26724 ns      26165  
BM_Covariance<double>/8192               53626 ns      53457 ns      13090  
BM_Covariance<double>/16384             107896 ns     107541 ns       6573  
BM_Covariance<double>/32768             213660 ns     213480 ns       3283  
BM_Covariance<double>_BigO                6.53 N       6.52 N   
BM_Covariance<double>_RMS                    1 %          1 %   
BM_Mean<double>/32                         108 ns        108 ns    6470278  
BM_Mean<double>/64                         314 ns        313 ns    2233525  
BM_Mean<double>/128                        747 ns        741 ns     951992  
BM_Mean<double>/256                       1580 ns       1573 ns     447076  
BM_Mean<double>/512                       3268 ns       3253 ns     215279  
BM_Mean<double>/1024                      6681 ns       6642 ns     105972  
BM_Mean<double>/2048                     13351 ns      13297 ns      52487  
BM_Mean<double>/4096                     26768 ns      26684 ns      26175  
BM_Mean<double>/8192                     53389 ns      53356 ns      13128  
BM_Mean<double>/16384                   107196 ns     106956 ns       6562  
BM_Mean<double>/32768                   214128 ns     213900 ns       3276  
BM_Mean<double>/65536                   427630 ns     427427 ns       1636  
BM_Mean<double>/131072                  856783 ns     855930 ns        818  
BM_Mean<double>/262144                 1714521 ns    1713289 ns        409  
BM_Mean<double>/524288                 3456346 ns    3444089 ns        202  
BM_Mean<double>/1048576                6877362 ns    6868647 ns        102  
BM_Mean<double>/2097152               14307281 ns   14018059 ns         51  
BM_Mean<double>/4194304               27585886 ns   27526880 ns         25  
BM_Mean<double>/8388608               55285568 ns   55173077 ns         13  
BM_Mean<double>/16777216             111112931 ns  110830000 ns          6  
BM_Mean<double>_BigO                      6.62 N       6.60 N   
BM_Mean<double>_RMS                          1 %          1 %   
BM_MeanAccumulator<double>/32              165 ns        157 ns    5146416  
BM_MeanAccumulator<double>/64              342 ns        341 ns    2044727  
BM_MeanAccumulator<double>/128             792 ns        767 ns     920350  
BM_MeanAccumulator<double>/256            1615 ns       1598 ns     443125  
BM_MeanAccumulator<double>/512            3252 ns       3243 ns     216408  
BM_MeanAccumulator<double>/1024           6565 ns       6550 ns     107086  
BM_MeanAccumulator<double>/2048          13220 ns      13191 ns      53236  
BM_MeanAccumulator<double>/4096          28072 ns      27011 ns      26534  
BM_MeanAccumulator<double>/8192          54856 ns      53976 ns      12644  
BM_MeanAccumulator<double>/16384        105851 ns     105794 ns       6620  
BM_MeanAccumulator<double>/32768        211977 ns     211812 ns       3302  
BM_MeanAccumulator<double>/65536        662019 ns     454792 ns       1639  
BM_MeanAccumulator<double>/131072       878295 ns     862072 ns        778  
BM_MeanAccumulator<double>/262144      2326289 ns    1923445 ns        366  
BM_MeanAccumulator<double>/524288      4623663 ns    3953169 ns        189  
BM_MeanAccumulator<double>/1048576    10415909 ns    8318936 ns         94  
BM_MeanAccumulator<double>/2097152    22098536 ns   16349273 ns         44  
BM_MeanAccumulator<double>/4194304    28312979 ns   27916174 ns         23  
BM_MeanAccumulator<double>/8388608    55674964 ns   54773769 ns         13  
BM_MeanAccumulator<double>/16777216  127979931 ns  117288333 ns          6  
BM_MeanAccumulator<double>_BigO           7.44 N       6.90 N   
BM_MeanAccumulator<double>_RMS              19 %          8 %   
BM_Variance<double>/4                        7 ns          7 ns   99952879  
BM_Variance<double>/8                       18 ns         18 ns   37940174  
BM_Variance<double>/16                      57 ns         55 ns   13092922  
BM_Variance<double>/32                     154 ns        153 ns    4261822  
BM_Variance<double>/64                     360 ns        360 ns    1932991  
BM_Variance<double>/128                    782 ns        780 ns     897321  
BM_Variance<double>/256                   1635 ns       1623 ns     432721  
BM_Variance<double>/512                   3284 ns       3282 ns     212969  
BM_Variance<double>/1024                  6617 ns       6615 ns     105841  
BM_Variance<double>/2048                 13287 ns      13283 ns      52672  
BM_Variance<double>/4096                 26673 ns      26657 ns      26249  
BM_Variance<double>/8192                 53501 ns      53442 ns      13121  
BM_Variance<double>/16384               106861 ns     106787 ns       6542  
BM_Variance<double>/32768               224986 ns     219033 ns       2913  
BM_Variance<double>/65536               429280 ns     428998 ns       1633  
BM_Variance<double>/131072              856669 ns     855440 ns        819  
BM_Variance<double>/262144             1726154 ns    1724868 ns        409  
BM_Variance<double>/524288             3441803 ns    3440735 ns        204  
BM_Variance<double>/1048576            6901812 ns    6896588 ns        102  
BM_Variance<double>_BigO                  6.58 N       6.57 N   
BM_Variance<double>_RMS                      0 %          0 %   
BM_Skewness<double>/4                       11 ns         11 ns   65725231  
BM_Skewness<double>/8                       31 ns         31 ns   22410327  
BM_Skewness<double>/16                      82 ns         82 ns    8535961  
BM_Skewness<double>/32                     193 ns        192 ns    3654761  
BM_Skewness<double>/64                     414 ns        413 ns    1697468  
BM_Skewness<double>/128                    857 ns        856 ns     817194  
BM_Skewness<double>/256                   1742 ns       1742 ns     401964  
BM_Skewness<double>/512                   3515 ns       3514 ns     199199  
BM_Skewness<double>/1024                  7096 ns       7092 ns      99142  
BM_Skewness<double>/2048                 14439 ns      14245 ns      49442  
BM_Skewness<double>/4096                 28575 ns      28489 ns      24641  
BM_Skewness<double>/8192                 56935 ns      56838 ns      12339  
BM_Skewness<double>/16384               114104 ns     113818 ns       5889  
BM_Skewness<double>/32768               299679 ns     250822 ns       2979  
BM_Skewness<double>/65536               457545 ns     455791 ns       1366  
BM_Skewness<double>/131072              909665 ns     908848 ns        770  
BM_Skewness<double>/262144             1824335 ns    1823081 ns        384  
BM_Skewness<double>/524288             3696460 ns    3691079 ns        191  
BM_Skewness<double>/1048576            7604573 ns    7546211 ns         95  
BM_Skewness<double>_BigO                  7.20 N       7.15 N   
BM_Skewness<double>_RMS                      4 %          3 %   
BM_Kurtosis<double>/4                       30 ns         27 ns   29519050  
BM_Kurtosis<double>/8                       46 ns         46 ns   11920778  
BM_Kurtosis<double>/16                     126 ns        114 ns    6900699  
BM_Kurtosis<double>/32                     242 ns        231 ns    3324973  
BM_Kurtosis<double>/64                     435 ns        434 ns    1608763  
BM_Kurtosis<double>/128                   1168 ns        999 ns     773241  
BM_Kurtosis<double>/256                   2113 ns       1900 ns     352746  
BM_Kurtosis<double>/512                   3515 ns       3513 ns     198518  
BM_Kurtosis<double>/1024                  7039 ns       7037 ns      99404  
BM_Kurtosis<double>/2048                 14094 ns      14082 ns      49226  
BM_Kurtosis<double>/4096                 38565 ns      32863 ns      24848  
BM_Kurtosis<double>/8192                 75344 ns      70334 ns      10731  
BM_Kurtosis<double>/16384               123380 ns     118852 ns       4955  
BM_Kurtosis<double>/32768               228219 ns     227514 ns       3089  
BM_Kurtosis<double>/65536               528807 ns     508306 ns       1551  
BM_Kurtosis<double>/131072              991354 ns     968699 ns        662  
BM_Kurtosis<double>/262144             1992192 ns    1962777 ns        376  
BM_Kurtosis<double>/524288             6134829 ns    4180832 ns        179  
BM_Kurtosis<double>/1048576           12021244 ns    8336276 ns         76  
BM_Kurtosis<double>_BigO                 11.27 N       7.92 N   
BM_Kurtosis<double>_RMS                     23 %          4 %   
BM_L1<double>/4                              3 ns          3 ns  249476642  
BM_L1<double>/8                              3 ns          3 ns  234943479  
BM_L1<double>/16                             6 ns          6 ns  110938540  
BM_L1<double>/32                            16 ns         16 ns   45787845  
BM_L1<double>/64                            38 ns         38 ns   18202195  
BM_L1<double>/128                           89 ns         89 ns    7896934  
BM_L1<double>/256                          232 ns        219 ns    3257102  
BM_L1<double>/512                          473 ns        470 ns    1490402  
BM_L1<double>/1024                         980 ns        972 ns     723537  
BM_L1<double>/2048                        1966 ns       1960 ns     357758  
BM_L1<double>/4096                        3966 ns       3956 ns     176517  
BM_L1<double>/8192                        8013 ns       7964 ns      88367  
BM_L1<double>/16384                      15977 ns      15924 ns      43562  
BM_L1<double>/32768                      32324 ns      32089 ns      21831  
BM_L1<double>_BigO                        0.98 N       0.98 N   
BM_L1<double>_RMS                            1 %          1 %   
BM_L2<double>/4                              5 ns          5 ns  127998830  
BM_L2<double>/8                              5 ns          5 ns  125949117  
BM_L2<double>/16                            10 ns         10 ns   74534691  
BM_L2<double>/32                            20 ns         20 ns   36316661  
BM_L2<double>/64                            45 ns         44 ns   15773084  
BM_L2<double>/128                           99 ns         98 ns    7075353  
BM_L2<double>/256                          224 ns        223 ns    3072547  
BM_L2<double>/512                          472 ns        471 ns    1474199  
BM_L2<double>/1024                         973 ns        970 ns     718789  
BM_L2<double>/2048                        1971 ns       1965 ns     355487  
BM_L2<double>/4096                        3999 ns       3976 ns     176601  
BM_L2<double>/8192                        8508 ns       8148 ns      88180  
BM_L2<double>/16384                      16012 ns      15949 ns      43797  
BM_L2<double>/32768                      34295 ns      33859 ns      21945  
BM_L2<double>_BigO                        1.03 N       1.02 N   
BM_L2<double>_RMS                            6 %          5 %   
BM_L3<double>/4                             90 ns         90 ns    7674513  
BM_L3<double>/8                            167 ns        167 ns    4193299  
BM_L3<double>/16                           352 ns        349 ns    2094272  
BM_L3<double>/32                           679 ns        678 ns    1004924  
BM_L3<double>/64                          1384 ns       1381 ns     511905  
BM_L3<double>/128                         2837 ns       2825 ns     251126  
BM_L3<double>/256                         5668 ns       5656 ns     121822  
BM_L3<double>/512                        11323 ns      11302 ns      61744  
BM_L3<double>/1024                       22733 ns      22687 ns      31010  
BM_L3<double>/2048                       45550 ns      45446 ns      15476  
BM_L3<double>/4096                       91947 ns      91643 ns       7680  
BM_L3<double>/8192                      183476 ns     182760 ns       3826  
BM_L3<double>/16384                     372254 ns     370373 ns       1896  
BM_L3<double>/32768                     741032 ns     738070 ns        946  
BM_L3<double>_BigO                       22.62 N      22.53 N   
BM_L3<double>_RMS                            1 %          1 %   
BM_TV<double>/4                              3 ns          3 ns  265102311  
BM_TV<double>/8                              7 ns          6 ns  112516677  
BM_TV<double>/16                            20 ns         12 ns   66906255  
BM_TV<double>/32                            30 ns         23 ns   28637119  
BM_TV<double>/64                            68 ns         49 ns   13152209  
BM_TV<double>/128                          117 ns        111 ns    6094429  
BM_TV<double>/256                          234 ns        229 ns    3089430  
BM_TV<double>/512                          495 ns        484 ns    1378251  
BM_TV<double>/1024                         979 ns        977 ns     717529  
BM_TV<double>/2048                        2037 ns       1994 ns     322255  
BM_TV<double>/4096                        4480 ns       4232 ns     163579  
BM_TV<double>/8192                        7983 ns       7968 ns      87056  
BM_TV<double>/16384                      17425 ns      16613 ns      43537  
BM_TV<double>/32768                      31889 ns      31845 ns      21806  
BM_TV<double>_BigO                        0.99 N       0.98 N   
BM_TV<double>_RMS                            8 %          4 %   
BM_SupNorm<double>/4                         3 ns          3 ns  209723375  
BM_SupNorm<double>/8                         7 ns          7 ns   97813177  
BM_SupNorm<double>/16                       14 ns         14 ns   52035325  
BM_SupNorm<double>/32                       27 ns         27 ns   25826827  
BM_SupNorm<double>/64                       59 ns         59 ns   12047156  
BM_SupNorm<double>/128                     111 ns        111 ns    6326997  
BM_SupNorm<double>/256                     214 ns        214 ns    3277246  
BM_SupNorm<double>/512                     420 ns        420 ns    1635399  
BM_SupNorm<double>/1024                    835 ns        835 ns     810579  
BM_SupNorm<double>/2048                   1667 ns       1667 ns     420961  
BM_SupNorm<double>/4096                   3376 ns       3367 ns     211119  
BM_SupNorm<double>/8192                   6667 ns       6662 ns     105641  
BM_SupNorm<double>/16384                 13424 ns      13397 ns      52286  
BM_SupNorm<double>/32768                 28584 ns      28178 ns      26337  
BM_SupNorm<double>_BigO                   0.86 N       0.85 N   
BM_SupNorm<double>_RMS                       6 %          5 %   
BM_Gini<double>/4                           13 ns         13 ns   54233716  
BM_Gini<double>/8                           20 ns         19 ns   35741274  
BM_Gini<double>/16                          37 ns         37 ns   20041687  
BM_Gini<double>/32                         101 ns         99 ns    7367568  
BM_Gini<double>/64                         201 ns        198 ns    3643177  
BM_Gini<double>/128                        366 ns        358 ns    2017436  
BM_Gini<double>/256                        641 ns        628 ns    1184995  
BM_Gini<double>/512                       1200 ns       1170 ns     628953  
BM_Gini<double>/1024                      2424 ns       2339 ns     314489  
BM_Gini<double>/2048                      4165 ns       4138 ns     158772  
BM_Gini<double>/4096                      8812 ns       8692 ns      80988  
BM_Gini<double>/8192                     16361 ns      16331 ns      42088  
BM_Gini<double>/16384                    41356 ns      39495 ns      21230  
BM_Gini<double>/32768                    70798 ns      70572 ns       9181  
BM_Gini<double>_BigO                      2.22 N       2.19 N   
BM_Gini<double>_RMS                         14 %         11 %   
BM_GiniUnsorted<double>/4                  152 ns        145 ns    4993651  
BM_GiniUnsorted<double>/8                  357 ns        357 ns    1952640  
BM_GiniUnsorted<double>/16                 797 ns        795 ns     883203  
BM_GiniUnsorted<double>/32                1794 ns       1793 ns     391061  
BM_GiniUnsorted<double>/64                3892 ns       3889 ns     179472  
BM_GiniUnsorted<double>/128               8393 ns       8384 ns      83913  
BM_GiniUnsorted<double>/256              18021 ns      18015 ns      38954  
BM_GiniUnsorted<double>/512              43949 ns      41399 ns      18393  
BM_GiniUnsorted<double>/1024             81827 ns      81437 ns       7406  
BM_GiniUnsorted<double>/2048            185209 ns     178943 ns       4106  
BM_GiniUnsorted<double>/4096            368836 ns     364628 ns       1966  
BM_GiniUnsorted<double>/8192            747746 ns     747071 ns        940  
BM_GiniUnsorted<double>/16384          1554195 ns    1553251 ns        450  
BM_GiniUnsorted<double>/32768          3331812 ns    3318370 ns        216  
BM_GiniUnsorted<double>_BigO              6.80 NlgN       6.77 NlgN   
BM_GiniUnsorted<double>_RMS                  3 %          3 %   
BM_Shuffle<double>/4                       118 ns        117 ns    5959425  
BM_Shuffle<double>/8                       291 ns        285 ns    2509752  
BM_Shuffle<double>/16                      622 ns        615 ns    1170784  
BM_Shuffle<double>/32                     1244 ns       1243 ns     559628  
BM_Shuffle<double>/64                     2518 ns       2516 ns     276669  
BM_Shuffle<double>/128                    5129 ns       5115 ns     137869  
BM_Shuffle<double>/256                   10271 ns      10263 ns      68492  
BM_Shuffle<double>/512                   20932 ns      20830 ns      34185  
BM_Shuffle<double>/1024                  43475 ns      42725 ns      16666  
BM_Shuffle<double>/2048                  85689 ns      84708 ns       8200  
BM_Shuffle<double>/4096                 199870 ns     189199 ns       4186  
BM_Shuffle<double>/8192                 404495 ns     379370 ns       1588  
BM_Shuffle<double>/16384               1680343 ns    1011631 ns        957  
BM_Shuffle<double>/32768               3999944 ns    2699840 ns        412  
BM_Shuffle<double>_BigO                   7.80 NlgN       5.22 NlgN   
BM_Shuffle<double>_RMS                      30 %         25 %   
```

---
