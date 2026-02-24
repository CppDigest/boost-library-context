# #98 Quasi-Monte Carlo integration. [Closed]

> Username: NAThompson  
> Created at: 2018-01-03 23:56:28 UTC  
> Updated at: 2024-01-24 21:32:32 UTC  
> Closed at: 2024-01-24 20:51:51 UTC  
> Url: https://github.com/boostorg/math/pull/98  

This was getting a bit too large for me to keep track of, so I'm PR'ing now.  
  
The quasi-Monte Carlo integration takes quite a few tools. You need a quasi-random sequence, and Kocis and Whiten show that the leaped Halton sequence is excellent. So this PR has a Halton sequence. But for a Halton sequence, you need a prime table. Boost.Math has a nice big prime table of 10,000 entries, but for QMC it's not clear that's sufficient. So the PR has a sieve of Eratosthenes. The sieve requires a bigint square root, so it has that too!  
  
This won't be done until I figure out how to allow people to provide quasi-random sequences, or at least provide (say) a Niederreiter and a Sobol sequence. But the leaped Halton is a nice starting place.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2018-01-08 19:09:58 UTC  
> Url: https://github.com/boostorg/math/pull/98#issuecomment-356064527  

Re large integer square root - Boost.Multiprecision support integer square roots natively.  The algorithm used by cpp_int is rather poor compared to GMP's though :(  
  
There are also some bit-fiddling algorithms (most/least significant bit) which may or may not be useful.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2018-01-13 20:59:11 UTC  
> Url: https://github.com/boostorg/math/pull/98#issuecomment-357466854  

You integer sqrt root is much faster that my `floor_sqrt`:  
  
```  
BM_FloorSqrt/2                        76 ns         76 ns    9355287  
BM_FloorSqrt/8                       156 ns        155 ns    4513421  
BM_FloorSqrt/64                      195 ns        195 ns    3187396  
BM_FloorSqrt/512                     285 ns        284 ns    2438625  
BM_FloorSqrt/4096                    366 ns        365 ns    1890354  
BM_FloorSqrt/32768                   449 ns        448 ns    1549993  
BM_FloorSqrt/262144                  486 ns        485 ns    1329636  
BM_FloorSqrt/2097152                 641 ns        625 ns    1187890  
BM_FloorSqrt/16777216                689 ns        672 ns     966370  
BM_FloorSqrt/134217728               697 ns        694 ns    1020081  
BM_FloorSqrt/1073741824              794 ns        788 ns     872176  
BM_FloorSqrt_BigO                  27.92 lgN      27.62 lgN  
BM_FloorSqrt_RMS                      10 %          9 %  
BM_EvalIntegerSqrt/2                  47 ns         46 ns   12866937  
BM_EvalIntegerSqrt/8                  43 ns         43 ns   16288957  
BM_EvalIntegerSqrt/64                 30 ns         30 ns   24683783  
BM_EvalIntegerSqrt/512               112 ns        111 ns    6839210  
BM_EvalIntegerSqrt/4096               33 ns         33 ns   20804418  
BM_EvalIntegerSqrt/32768             179 ns        179 ns    3862197  
BM_EvalIntegerSqrt/262144             38 ns         38 ns   18134527  
BM_EvalIntegerSqrt/2097152           210 ns        209 ns    3359441  
BM_EvalIntegerSqrt/16777216           49 ns         49 ns   14446036  
BM_EvalIntegerSqrt/134217728         275 ns        273 ns    2624259  
BM_EvalIntegerSqrt/1073741824         48 ns         48 ns   14406286  
BM_EvalIntegerSqrt_BigO             5.65 lgN       5.62 lgN  
BM_EvalIntegerSqrt_RMS                80 %         80 %  
```  
  
The only problem is you can't call yours with just an `int` or `size_t`; you need to call it via `eval_integer_sqrt(s.backend(), r.backend(), x.backend())`.  
  
Is there a simple workaround I'm not seeing?

---
