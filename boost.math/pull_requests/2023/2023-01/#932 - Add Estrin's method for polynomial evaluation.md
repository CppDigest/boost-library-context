# #932 Add Estrin's method for polynomial evaluation [Merged]

> Username: NAThompson  
> Created at: 2023-01-28 17:34:57 UTC  
> Updated at: 2023-02-04 18:35:12 UTC  
> Merged at: 2023-02-04 18:32:06 UTC  
> Closed at: 2023-02-04 18:32:06 UTC  
> Url: https://github.com/boostorg/math/pull/932  

N.B.: This is a slightly modified version of the code provided by Thomas Dybdahl Ahle in a github issue.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2023-01-28 17:51:30 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1407450428  

@thomasahle : Do you know literature which gives the roundoff bounds? IIRC for Horner's method it's μn|p'(x)| where μ is the unit roundoff and n is length of the polynomial. But I believe Estrin has something more like μlog(n)|p'(x)|...

---

## Comment 2

> Username: NAThompson  
> Created_at: 2023-01-28 19:41:32 UTC  
> Updated_at: 2023-01-28 20:30:10 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1407471692  

@jzmaddock : These are the numbers I get on an M1 Pro:  
  
```  
Run on (10 X 24.0607 MHz CPU s)  
CPU Caches:  
  L1 Data 64 KiB (x10)  
  L1 Instruction 128 KiB (x10)  
  L2 Unified 4096 KiB (x5)  
Load Average: 1.37, 1.61, 1.51  
-----------------------------------------------------------------------------------------------------  
Benchmark                                                           Time             CPU   Iterations  
-----------------------------------------------------------------------------------------------------  
HornersMethodRealCoeffsRealArg<double>/1                        0.311 ns        0.311 ns   1000000000  
HornersMethodRealCoeffsRealArg<double>/2                         1.01 ns         1.01 ns    689526099  
HornersMethodRealCoeffsRealArg<double>/4                         1.56 ns         1.56 ns    449279548  
HornersMethodRealCoeffsRealArg<double>/8                         2.80 ns         2.80 ns    249913423  
HornersMethodRealCoeffsRealArg<double>/16                        5.29 ns         5.29 ns    131782070  
HornersMethodRealCoeffsRealArg<double>/32                        12.0 ns         12.0 ns     58106717  
HornersMethodRealCoeffsRealArg<double>/64                        32.5 ns         32.5 ns     21560221  
HornersMethodRealCoeffsRealArg<double>/128                       91.4 ns         91.4 ns      7632839  
HornersMethodRealCoeffsRealArg<double>/256                        221 ns          221 ns      3170534  
HornersMethodRealCoeffsRealArg<double>/512                        540 ns          540 ns      1293159  
HornersMethodRealCoeffsRealArg<double>/1024                      1177 ns         1177 ns       593437  
HornersMethodRealCoeffsRealArg<double>/2048                      2452 ns         2452 ns       285473  
HornersMethodRealCoeffsRealArg<double>/4096                      4994 ns         4994 ns       139526  
HornersMethodRealCoeffsRealArg<double>/8192                     10094 ns        10094 ns        69236  
HornersMethodRealCoeffsRealArg<double>/16384                    20454 ns        20421 ns        34468  
HornersMethodRealCoeffsRealArg<double>/32768                    40692 ns        40692 ns        17204  
HornersMethodRealCoeffsRealArg<double>_BigO                      1.24 N          1.24 N  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/1           1.63 ns         1.63 ns    429124035  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/2           1.89 ns         1.89 ns    371413866  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/4           6.80 ns         6.80 ns     98007645  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/8           12.1 ns         12.1 ns     58090804  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/16          22.1 ns         22.1 ns     31732360  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/32          90.1 ns         90.0 ns      7722093  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/64           198 ns          198 ns      3536389  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/128          177 ns          177 ns      4020701  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/256          295 ns          294 ns      2359993  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/512          452 ns          452 ns      1543261  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/1024         773 ns          772 ns       906290  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/2048        1397 ns         1394 ns       502610  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/4096        2665 ns         2660 ns       264210  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/8192        5169 ns         5164 ns       134758  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/16384      10617 ns        10590 ns        67294  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/32768      20752 ns        20713 ns        33473  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>_BigO        0.64 N          0.64 N  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>_RMS            4 %             4 %  
HornersMethodRealCoeffsComplexArg<double>/1                     0.315 ns        0.315 ns   1000000000  
HornersMethodRealCoeffsComplexArg<double>/2                      1.15 ns         1.15 ns    609660506  
HornersMethodRealCoeffsComplexArg<double>/4                      1.56 ns         1.56 ns    443970876  
HornersMethodRealCoeffsComplexArg<double>/8                      3.43 ns         3.43 ns    204215593  
HornersMethodRealCoeffsComplexArg<double>/16                     10.1 ns         10.1 ns     69443067  
HornersMethodRealCoeffsComplexArg<double>/32                     31.5 ns         31.5 ns     22191717  
HornersMethodRealCoeffsComplexArg<double>/64                     91.7 ns         91.7 ns      7616892  
HornersMethodRealCoeffsComplexArg<double>/128                     254 ns          254 ns      2748795  
HornersMethodRealCoeffsComplexArg<double>/256                     577 ns          577 ns      1208856  
HornersMethodRealCoeffsComplexArg<double>/512                    1226 ns         1226 ns       570827  
HornersMethodRealCoeffsComplexArg<double>/1024                   2545 ns         2544 ns       275272  
HornersMethodRealCoeffsComplexArg<double>/2048                   5165 ns         5164 ns       134259  
HornersMethodRealCoeffsComplexArg<double>/4096                  10307 ns        10307 ns        67717  
HornersMethodRealCoeffsComplexArg<double>/8192                  20720 ns        20720 ns        33757  
HornersMethodRealCoeffsComplexArg<double>/16384                 41531 ns        41531 ns        16742  
HornersMethodRealCoeffsComplexArg<double>/32768                 83491 ns        83491 ns         8373  
HornersMethodRealCoeffsComplexArg<double>_BigO                   2.54 N          2.54 N  
HornersMethodRealCoeffsComplexArg<double>_RMS                       1 %             1 %  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/1           1.62 ns         1.62 ns    427530523  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/2           1.87 ns         1.87 ns    374083495  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/4           5.98 ns         5.98 ns    116820480  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/8           12.0 ns         12.0 ns     58284277  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/16          21.9 ns         21.9 ns     31884850  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/32          89.3 ns         89.3 ns      7804486  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/64           197 ns          197 ns      3547465  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/128          173 ns          173 ns      4041780  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/256          288 ns          288 ns      2395267  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/512          451 ns          451 ns      1555106  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/1024         771 ns          771 ns       905036  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/2048        1395 ns         1395 ns       499975  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/4096        2649 ns         2648 ns       264455  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/8192        5142 ns         5141 ns       135970  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/16384      10380 ns        10371 ns        67300  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>/32768      20362 ns        20359 ns        34332  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>_BigO        0.62 N          0.62 N  
EstrinsMethodRealCoeffsComplexArgWithScratch<double>_RMS            4 %             4 %  
```

---

## Comment 3

> Username: NAThompson  
> Created_at: 2023-01-28 20:03:20 UTC  
> Updated_at: 2023-01-28 20:32:02 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1407475486  

Analyzed the crossover point with DenseRange: 96 with real argument and real coefficients:  
  
```  
------------------------------------------------------------------------------------------------  
Benchmark                                                      Time             CPU   Iterations  
------------------------------------------------------------------------------------------------  
HornersMethodRealCoeffsRealArg<double>/64                   32.5 ns         32.5 ns     21404240  
HornersMethodRealCoeffsRealArg<double>/80                   50.0 ns         50.0 ns     13917331  
HornersMethodRealCoeffsRealArg<double>/96                   66.4 ns         66.4 ns     10507986  
HornersMethodRealCoeffsRealArg<double>/112                  79.1 ns         79.1 ns      8818453  
HornersMethodRealCoeffsRealArg<double>/128                  91.3 ns         91.3 ns      7636253  
  
EstrinsMethodRealCoeffsRealArgWithScratch<double>/64        45.9 ns         45.9 ns     15064649  
EstrinsMethodRealCoeffsRealArgWithScratch<double>/80        53.0 ns         53.0 ns     13170025  
EstrinsMethodRealCoeffsRealArgWithScratch<double>/96        53.1 ns         53.1 ns     13105669  
EstrinsMethodRealCoeffsRealArgWithScratch<double>/112       53.2 ns         53.2 ns     13128775  
EstrinsMethodRealCoeffsRealArgWithScratch<double>/128       75.9 ns         75.9 ns      9200720  
```

---

## Comment 4

> Username: thomasahle  
> Created_at: 2023-01-28 21:35:54 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1407493134  

Are you able to check if specialized code is being generated for the small sizes? It really should be at least as fast as horner for n=1 and 8. Maybe I should try just writing it out. But if we can template on the size, it might be enough as well.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2023-01-28 21:45:08 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1407494661  

@thomasahle : The `std::array` overload should get correct code generation for the small sizes; it's just extremely painful to use compile time integer sequences with google/benchmark.  
  
Lemme add a few cases.

---

## Comment 6

> Username: NAThompson  
> Created_at: 2023-01-28 21:56:49 UTC  
> Updated_at: 2023-01-28 21:57:28 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1407496441  

@thomasahle : The comparison between the std::array case is much more favorable to Estrin's method:  
  
```  
HornerRealCoeffsRealArg<float>/1                         0.316 ns        0.316 ns   1000000000  
HornerRealCoeffsRealArg<float>/2                          1.02 ns         1.02 ns    684027947  
HornerRealCoeffsRealArg<float>/4                          1.57 ns         1.57 ns    446793301  
HornerRealCoeffsRealArg<float>/8                          2.82 ns         2.82 ns    248411938  
HornerRealCoeffsRealArg<float>/16                         5.29 ns         5.29 ns    131260665  
HornerRealCoeffsRealArg<float>/32                         12.0 ns         12.0 ns     58177224  
HornerRealCoeffsRealArg<float>/64                         32.5 ns         32.5 ns     21553118  
EstrinRealCoeffsRealArgStdArray<float, 2>                0.931 ns        0.931 ns    749360367  
EstrinRealCoeffsRealArgStdArray<float, 4>                 1.01 ns         1.01 ns    691146414  
EstrinRealCoeffsRealArgStdArray<float, 8>                 1.33 ns         1.33 ns    527056839  
EstrinRealCoeffsRealArgStdArray<float, 16>                1.84 ns         1.84 ns    379379119  
EstrinRealCoeffsRealArgStdArray<float, 32>                12.3 ns         12.3 ns     54234557  
EstrinRealCoeffsRealArgStdArray<float, 64>                19.4 ns         19.4 ns     36444856  
HornerRealCoeffsRealArg<double>/1                        0.313 ns        0.313 ns   1000000000  
HornerRealCoeffsRealArg<double>/2                         1.02 ns         1.02 ns    682607169  
HornerRealCoeffsRealArg<double>/4                         1.57 ns         1.57 ns    445970655  
HornerRealCoeffsRealArg<double>/8                         2.81 ns         2.81 ns    249350797  
HornerRealCoeffsRealArg<double>/16                        5.29 ns         5.29 ns    132092918  
HornerRealCoeffsRealArg<double>/32                        12.0 ns         12.0 ns     58223679  
HornerRealCoeffsRealArg<double>/64                        32.8 ns         32.8 ns     21289279  
EstrinRealCoeffsRealArgStdArray<double, 2>               0.948 ns        0.947 ns    748535010  
EstrinRealCoeffsRealArgStdArray<double, 4>                1.03 ns         1.03 ns    685494927  
EstrinRealCoeffsRealArgStdArray<double, 8>                1.34 ns         1.34 ns    521062073  
EstrinRealCoeffsRealArgStdArray<double, 16>               1.95 ns         1.95 ns    352447750  
EstrinRealCoeffsRealArgStdArray<double, 32>               12.8 ns         12.8 ns     52302037  
EstrinRealCoeffsRealArgStdArray<double, 64>               21.7 ns         21.7 ns     32216198  
HornerRealCoeffsComplexArg<float>/1                      0.310 ns        0.310 ns   1000000000  
HornerRealCoeffsComplexArg<float>/2                       1.13 ns         1.13 ns    615633575  
HornerRealCoeffsComplexArg<float>/4                       1.56 ns         1.56 ns    449328575  
HornerRealCoeffsComplexArg<float>/8                       3.42 ns         3.42 ns    204882046  
HornerRealCoeffsComplexArg<float>/16                      10.0 ns         10.0 ns     69459604  
HornerRealCoeffsComplexArg<float>/32                      31.5 ns         31.5 ns     22265127  
HornerRealCoeffsComplexArg<float>/64                      91.4 ns         91.4 ns      7631840  
EstrinRealCoeffsComplexArgStdArray<float, 2>             0.956 ns        0.956 ns    728286654  
EstrinRealCoeffsComplexArgStdArray<float, 4>              1.32 ns         1.32 ns    529100529  
EstrinRealCoeffsComplexArgStdArray<float, 8>              2.44 ns         2.44 ns    288685984  
EstrinRealCoeffsComplexArgStdArray<float, 16>             15.7 ns         15.7 ns     44304639  
EstrinRealCoeffsComplexArgStdArray<float, 32>             23.4 ns         23.4 ns     29983723  
EstrinRealCoeffsComplexArgStdArray<float, 64>             30.1 ns         30.0 ns     23439357  
```  
  
Diffs pushed up; can you run the benchmark?

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2023-01-29 13:50:30 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1407668860  

This is looking very good, especially for larger polynomials, just pushed some more comparisons to Horner for the std::array case, on cygwin I see:  
  
```  
EstrinRealCoeffsRealArgStdArray<double, 2>                1.16 ns         1.15 ns    640000000  
EstrinRealCoeffsRealArgStdArray<double, 3>                1.43 ns         1.42 ns    640000000  
EstrinRealCoeffsRealArgStdArray<double, 4>                1.62 ns         1.54 ns    497777778  
EstrinRealCoeffsRealArgStdArray<double, 5>                1.85 ns         1.80 ns    373333333  
EstrinRealCoeffsRealArgStdArray<double, 8>                2.51 ns         2.49 ns    263529412  
EstrinRealCoeffsRealArgStdArray<double, 9>                2.73 ns         2.52 ns    235789474  
EstrinRealCoeffsRealArgStdArray<double, 16>               3.91 ns         3.92 ns    179200000  
EstrinRealCoeffsRealArgStdArray<double, 17>               4.34 ns         4.14 ns    165925926  
EstrinRealCoeffsRealArgStdArray<double, 32>               14.6 ns         14.3 ns     44800000  
EstrinRealCoeffsRealArgStdArray<double, 33>               15.2 ns         14.3 ns     44800000  
EstrinRealCoeffsRealArgStdArray<double, 64>               31.0 ns         30.0 ns     22400000  
EstrinRealCoeffsRealArgStdArray<double, 65>               28.7 ns         29.1 ns     26352941  
HornerRealCoeffsRealArgStdArray<double, 2>                1.15 ns         1.15 ns    640000000  
HornerRealCoeffsRealArgStdArray<double, 3>                1.17 ns         1.16 ns    497777778  
HornerRealCoeffsRealArgStdArray<double, 4>                1.34 ns         1.29 ns    448000000  
HornerRealCoeffsRealArgStdArray<double, 5>                1.71 ns         1.67 ns    448000000  
HornerRealCoeffsRealArgStdArray<double, 8>                2.34 ns         2.30 ns    298666667  
HornerRealCoeffsRealArgStdArray<double, 9>                2.58 ns         2.49 ns    263529412  
HornerRealCoeffsRealArgStdArray<double, 16>               5.47 ns         5.47 ns    100000000  
HornerRealCoeffsRealArgStdArray<double, 17>               5.96 ns         5.86 ns    112000000  
HornerRealCoeffsRealArgStdArray<double, 32>               26.4 ns         24.5 ns     24888889  
HornerRealCoeffsRealArgStdArray<double, 33>               27.7 ns         27.2 ns     23578947  
HornerRealCoeffsRealArgStdArray<double, 64>               75.2 ns         75.0 ns      8960000  
HornerRealCoeffsRealArgStdArray<double, 65>               77.5 ns         78.5 ns      8960000  
```  
  
So Horner is very slightly ahead until probably > 10 terms.  
  
Speculation: estrin involves more memory accesses, and/or pulling temp storage into the CPU cache, but that's a one time hit, and after that it just hits the same storage over and over which may explain those results.  
  
Two questions spring to mind:  
  
1) Allocating dynamic storage for estrin is so horribly expensive compared to the cost of evaluating a polynomial, that perhaps we shouldn't be providing an interface that does that?  Just feels like an invitation for someone to shoot themselves in the foot?  
2) Thinking out loud here, should the existing methods be renamed "horner", and "evaluate_polynomial" call which ever method we think is the best for the size of array and whether we have temp storage or not?  Actually being a verbose kind of guy, I would probably go with `evaluate_polynomial/evaluate_polynomial_estrin/evaluate_polynomial_horner`, but I'm open to other suggestions.  Being picky, I would probably document the whole thing on one page (Horner and Estrin) so that the user has a clear overview of the options.

---

## Comment 8

> Username: NAThompson  
> Created_at: 2023-01-29 16:47:16 UTC  
> Updated_at: 2023-01-29 16:50:42 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1407713290  

@jzmaddock : Like an idiot I just forced push over your diffs; could your repush?  
  
In any case:  
  
> Allocating dynamic storage for estrin is so horribly expensive compared to the cost of evaluating a polynomial, that perhaps we shouldn't be providing an interface that does that? Just feels like an invitation for someone to shoot themselves in the foot?  
  
Good point. I've kept the code but removed it from documentation. I want to keep the code for a little bit just to see if there are use cases . . . also maybe that gcc extension for stack arrays could help here ..  .  
  
> Thinking out loud here, should the existing methods be renamed "horner", and "evaluate_polynomial" call which ever method we think is the best for the size of array and whether we have temp storage or not? Actually being a verbose kind of guy, I would probably go with evaluate_polynomial/evaluate_polynomial_estrin/evaluate_polynomial_horner, but I'm open to other suggestions. Being picky, I would probably document the whole thing on one page (Horner and Estrin) so that the user has a clear overview of the options.  
  
Yes, I think that's a good idea. It may also make sense to add the following interface to match `evaluate_polynomial`:  
  
```cpp  
template <std::size_t N, class T, class V>  
V estrin(const T(&poly)[N], const V& val);  
```  
  
However, the tuning required to backend might take quite a huge commit; should we merge the Estrin method without making the changes to the `evaluate_polynomial` backend?  
  
Finally, I have reduced the C++ standard to 11 so that we could backend `evaluate_polynomial` to either Horner or Estrin should we choose.

---

## Comment 9

> Username: NAThompson  
> Created_at: 2023-01-29 19:05:00 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1407744559  

@jzmaddock , @mborland , @thomasahle : I have some dream that the build will succeed, or at least be close enough that this is ready for review.

---

## Review 10 [Commented]

> Username: mborland  
> Created_at: 2023-01-29 21:53:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/932#pullrequestreview-1274303910  

This looks good to me. The only thing I saw was the missing header because you use `std::is_same` in the assertion.

📁 include/boost/math/tools/estrin.hpp

```diff
  10 |+ #include <array>
  11 |+ #include <vector>
  12 |+ 
```

> Username: mborland  
> Created_at: 2023-01-29 21:48:07 UTC  
> Updated_at: 2023-01-29 21:53:23 UTC  
> Url: https://github.com/boostorg/math/pull/932#discussion_r1090057245  

```diff  
+#include <type_traits>  
```

> Username: NAThompson  
> Created_at: 2023-01-29 22:21:22 UTC  
> Url: https://github.com/boostorg/math/pull/932#discussion_r1090063766  

@mborland : Yup, fixed.


---

## Comment 11

> Username: thomasahle  
> Created_at: 2023-01-29 23:00:34 UTC  
> Updated_at: 2023-01-29 23:01:55 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1407794602  

@jzmaddock   
  
>     1. Allocating dynamic storage for estrin is so horribly expensive compared to the cost of evaluating a polynomial, that perhaps we shouldn't be providing an interface that does that?  Just feels like an invitation for someone to shoot themselves in the foot?  
  
What I was trying to suggest above was using Estrin only on blocks of up to, say, size 128. That way we will never overflow the stack. Maybe it could be even lower, like 16 or 32.  
  
Then we can just combine the blocks using horner.  
The code should also keep x, x^2, x^4, x^8,... x^32 around, so they don't have to be recomputed for each block.  
I can write a version that does this if you agree.  
  
Btw, did any of you try benchmarkingthe  Knuth Eve code?

---

## Comment 12

> Username: NAThompson  
> Created_at: 2023-01-29 23:15:40 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1407798592  

> What I was trying to suggest above was using Estrin only on blocks of up to, say, size 128. That way we will never overflow the stack. Maybe it could be even lower, like 16 or 32.  
  
Not a bad idea. Stack arrays are always tricky to get working across all supported platforms though, and won't the inlining have to die once we're using all the registers for scratch space?  
  
> Btw, did any of you try benchmarking the Knuth Eve code?  
  
I'm very interested, but isn't it conceptually distinct from this MR?

---

## Comment 13

> Username: NAThompson  
> Created_at: 2023-01-30 00:18:34 UTC  
> Updated_at: 2023-01-30 00:22:02 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1407816751  

Ok, gave the stack array a try; sadly at least with this mechanism, it made the code slower:  
  
```diff  
diff --git a/include/boost/math/tools/estrin.hpp b/include/boost/math/tools/estrin.hpp  
index b94702a71..4c3abfffb 100644  
--- a/include/boost/math/tools/estrin.hpp  
+++ b/include/boost/math/tools/estrin.hpp  
@@ -59,8 +59,14 @@ inline RealOrComplex estrin(const RandomAccessContainer &coeffs, RealOrComplex z  
   // Normally, I'd make `ds` a RandomAccessContainer, but its value type needs to be RealOrComplex,  
   // and the value_type of the passed RandomAccessContainer can just be Real.  
   // Allocation of the std::vector is not ideal, but I have no other ideas at the moment:  
-  std::vector<RealOrComplex> ds((n + 1) / 2);  
-  return estrin(coeffs, ds, z);  
+  constexpr const size_t n_max = 33;  
+  if (n >= n_max) {  
+      std::vector<RealOrComplex> ds((n + 1) / 2);  
+      return estrin(coeffs, ds, z);  
+  } else {  
+      std::array<RealOrComplex, (n_max+1)/2> ds;  
+      return estrin(coeffs, ds, z);  
+  }  
 }  
  
 } // namespace tools  
```  
  
Obviously I can't preclude the existence of some other mechanism to do a stack array that might be faster, but at this point I believe instruction generation is also playing a major role here.

---

## Comment 14

> Username: NAThompson  
> Created_at: 2023-01-30 01:54:48 UTC  
> Updated_at: 2023-01-30 02:27:50 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1407871470  

Another complication: It appears the Estrin method is less accurate than Horner's:  
  
<img width="1220" alt="Screenshot 2023-01-29 at 6 01 17 PM" src="https://user-images.githubusercontent.com/5459618/215372249-5dd48da8-8de1-454f-a1ad-9b452c2592f7.png">  
  
I think this is sufficient evidence that we should only backend `evaluate_polynomial` to `estrin` after some careful thought.

---

## Comment 15

> Username: NAThompson  
> Created_at: 2023-01-30 16:16:14 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1408918202  

Build failure is a timeout.

---

## Review 16 [Commented]

> Username: mborland  
> Created_at: 2023-01-30 18:19:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/932#pullrequestreview-1275698169  

📁 doc/internals/estrin.qbk

```diff
  10 |+  
  11 |+    // Advanced interface: Use if you can preallocate a scratch buffer of size (coeffs.size() +1)/2:
  12 |+    // The scratch buffer size is *unchecked*, so use at your own risk!
```

> Username: mborland  
> Created_at: 2023-01-30 18:16:29 UTC  
> Updated_at: 2023-01-30 18:19:03 UTC  
> Url: https://github.com/boostorg/math/pull/932#discussion_r1090983577  

Since we know what size the buffer is supposed to be can we `assert(buffer.size() >= (N+1)/2)`?


---

## Comment 17

> Username: thomasahle  
> Created_at: 2023-01-30 21:17:06 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1409352606  

> Another complication: It appears the Estrin method is less accurate than Horner's:  
>   
> <img alt="Screenshot 2023-01-29 at 6 01 17 PM" width="1220" src="https://user-images.githubusercontent.com/5459618/215372249-5dd48da8-8de1-454f-a1ad-9b452c2592f7.png">  
>   
> I think this is sufficient evidence that we should only backend `evaluate_polynomial` to `estrin` after some careful thought.  
  
This is for a degree 30 polynomial, right?  
I think the root of the instability might be that when we split into [0-15] and [16-30], and then multiply the high part by x^16, then we are multiplying something very small (if the polynomial has exponentially decreasing coefficients) with something very large (if |x|>1).  
  
How does it look for something like degree 8? If we work on blocks of size 8 and combine them with horner, then maybe we don't have to worry about the instability of raw estrin on such large polynomials.  
  
Another option might be to use a bottom up split instead, using the even-odd equation `P(x) = Q(x^2) + R(x^2)x`. That way we are always adding stuff of roughly similar magnitude.  
I will think about whether there's a good way to turn it into efficient non-recursive code.  
  
Were you ever able to find the analysis of the numerical error in Estrin? Maybe there's a literature about how to make it more stable.

---

## Comment 18

> Username: thomasahle  
> Created_at: 2023-01-30 23:13:56 UTC  
> Updated_at: 2023-01-30 23:15:02 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1409501918  

Hm, the "Opposite Estrin" method seems to not actually be more stable.  
Though using blocks (here blocks of size 8) does help:  
  
![estrin](https://user-images.githubusercontent.com/946355/215617660-e6a0b165-e1f0-4905-8c21-76d5c816a2e9.png)  
  
Here I computed the polynomial with horner and estrin, and plotted (estrin - horne)/|horner|.  
The difference is pretty small (1e-14)

---

## Comment 19

> Username: NAThompson  
> Created_at: 2023-01-31 00:15:18 UTC  
> Updated_at: 2023-01-31 00:17:19 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1409556172  

> This is for a degree 30 polynomial, right?  
  
Yes. I've added the polynomial degree to the title and tried n=8. There are fewer roundoffs and hence both are more accurate, but it still appears that Horner is winning:  
  
  
<img width="1094" alt="Screenshot 2023-01-30 at 16 12 46" src="https://user-images.githubusercontent.com/5459618/215625684-b17d00bb-f641-47c2-a6d4-168eebbe9bc5.png">  
  
> Were you ever able to find the analysis of the numerical error in Estrin? Maybe there's a literature about how to make it more stable.  
  
I googled around but found nothing. Someone should ask ChatGPT!  
  
> The difference is pretty small (1e-14)  
  
Yeah, I don't think in applied practice it would make much difference, but in general our "best goal" is half-ulp accuracy, because we really don't know what the user intends to do with it.

---

## Comment 20

> Username: NAThompson  
> Created_at: 2023-01-31 17:57:06 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1410825145  

@jzmaddock : This is probably good to go IMO.

---

## Comment 21

> Username: mborland  
> Created_at: 2023-01-31 21:26:03 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1411090591  

I merged dropping the language requirement for condition numbers from C++17 to C++14 which I think was the only thing stopping this from testing at C++14. Might be a worthwhile addition.

---

## Comment 22

> Username: thomasahle  
> Created_at: 2023-02-03 20:44:21 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1416384265  

I tried implementing Estrin with blocks:  
  
```  
template<typename Real, typename RealOrComplex>  
inline auto estrin(const Real* coeffs, long n, RealOrComplex* pows) {  
    if (n == 1)  
        return coeffs[0];  
  
    RealOrComplex ds[(n+1)/2];  
    for (int i = 0; i < n / 2; i++) {  
        ds[i] = coeffs[2*i] + coeffs[2*i+1] * pows[0];  
    }  
    if ((n & 1) == 1) ds[n / 2] = coeffs[n - 1];  
    n = (n + 1) / 2;  
  
    for (int level = 0; n != 1; level++) {  
        for (int i = 0; i < n / 2; i++) {  
            ds[i] = ds[2*i] + ds[2*i+1] * pows[level];  
        }  
        if ((n & 1) == 1) ds[n / 2] = ds[n - 1];  
        n = (n + 1) / 2;  
    }  
  
    return ds[0];  
}  
  
template<int lg_block_size, typename Real, typename RealOrComplex>  
inline auto estrin_block(const Real* coeffs, long n, RealOrComplex z) {  
    int block_size = 1 << lg_block_size;  
  
    if (n <= block_size)  
        return estrin(coeffs, n, z);  
  
    RealOrComplex pows[lg_block_size+1];  
    pows[0] = z;  
    for (int i = 1; i < lg_block_size+1; i++)  
        pows[i] = pows[i-1] * pows[i-1];  
  
    int blocks = n / block_size;  
    if (blocks * block_size == n)  
        blocks--;  
    int rem = n - block_size * blocks;  
  
    RealOrComplex result = estrin(coeffs + (n - rem), rem, pows);  
    for (int i = n - rem - block_size; i >= 0; i -= block_size) {  
        result = result * pows[lg_block_size] + estrin(coeffs + i, block_size, pows);  
    }  
  
    return result;  
}  
```  
  
These were the results using block size 4, 8 and 16:  
```  
EstrinsMethodRealCoeffsRealArg<float>/1                       2.85 ns         2.85 ns    247210931  
EstrinsMethodRealCoeffsRealArg<float>/8                       12.1 ns         12.1 ns     58874488  
EstrinsMethodRealCoeffsRealArg<float>/64                      27.2 ns         27.2 ns     25789053  
EstrinsMethodRealCoeffsRealArg<float>/512                      142 ns          142 ns      4849761  
EstrinsMethodRealCoeffsRealArg<float>/4096                    1087 ns         1087 ns       630767  
EstrinsMethodRealCoeffsRealArg<float>/32768                   8801 ns         8801 ns        77586  
EstrinsMethodRealCoeffsRealArg<float>_BigO                    0.27 N          0.27 N  
EstrinsMethodRealCoeffsRealArg<float>_RMS                        0 %             0 %  
EstrinsMethodRealCoeffsRealArg<double>/1                      2.87 ns         2.87 ns    240452325  
EstrinsMethodRealCoeffsRealArg<double>/8                      11.9 ns         11.9 ns     57543075  
EstrinsMethodRealCoeffsRealArg<double>/64                     32.1 ns         32.1 ns     24423518  
EstrinsMethodRealCoeffsRealArg<double>/512                     166 ns          166 ns      3975714  
EstrinsMethodRealCoeffsRealArg<double>/4096                   1299 ns         1299 ns       515240  
EstrinsMethodRealCoeffsRealArg<double>/32768                 13901 ns        13897 ns        48818  
EstrinsMethodRealCoeffsRealArg<double>_BigO                   0.03 NlgN       0.03 NlgN  
EstrinsMethodRealCoeffsRealArg<double>_RMS                       2 %             2 %  
EstrinBlockRealCoeffsRealArg4<float>/1                        3.07 ns         3.07 ns    230434468  
EstrinBlockRealCoeffsRealArg4<float>/8                        9.83 ns         9.83 ns     72398564  
EstrinBlockRealCoeffsRealArg4<float>/64                       29.6 ns         29.6 ns     23892008  
EstrinBlockRealCoeffsRealArg4<float>/512                       244 ns          244 ns      2878905  
EstrinBlockRealCoeffsRealArg4<float>/4096                     1931 ns         1930 ns       359624  
EstrinBlockRealCoeffsRealArg4<float>/32768                   15423 ns        15421 ns        43395  
EstrinBlockRealCoeffsRealArg4<float>_BigO                     0.47 N          0.47 N  
EstrinBlockRealCoeffsRealArg4<float>_RMS                         0 %             0 %  
EstrinBlockRealCoeffsRealArg4<double>/1                       2.92 ns         2.92 ns    235159741  
EstrinBlockRealCoeffsRealArg4<double>/8                       9.83 ns         9.82 ns     71799290  
EstrinBlockRealCoeffsRealArg4<double>/64                      32.5 ns         32.5 ns     23707035  
EstrinBlockRealCoeffsRealArg4<double>/512                      244 ns          244 ns      2895853  
EstrinBlockRealCoeffsRealArg4<double>/4096                    1933 ns         1933 ns       357489  
EstrinBlockRealCoeffsRealArg4<double>/32768                  15869 ns        15864 ns        45171  
EstrinBlockRealCoeffsRealArg4<double>_BigO                    0.48 N          0.48 N  
EstrinBlockRealCoeffsRealArg4<double>_RMS                        1 %             1 %  
EstrinBlockRealCoeffsRealArg8<float>/1                        4.29 ns         4.28 ns    164572288  
EstrinBlockRealCoeffsRealArg8<float>/8                        13.8 ns         13.8 ns     47452801  
EstrinBlockRealCoeffsRealArg8<float>/64                       31.4 ns         31.4 ns     18928588  
EstrinBlockRealCoeffsRealArg8<float>/512                       164 ns          164 ns      4156918  
EstrinBlockRealCoeffsRealArg8<float>/4096                     1260 ns         1260 ns       566783  
EstrinBlockRealCoeffsRealArg8<float>/32768                   10120 ns        10119 ns        65650  
EstrinBlockRealCoeffsRealArg8<float>_BigO                     0.31 N          0.31 N  
EstrinBlockRealCoeffsRealArg8<float>_RMS                         0 %             0 %  
EstrinBlockRealCoeffsRealArg8<double>/1                       4.25 ns         4.25 ns    166480367  
EstrinBlockRealCoeffsRealArg8<double>/8                       13.5 ns         13.5 ns     52690608  
EstrinBlockRealCoeffsRealArg8<double>/64                      36.6 ns         36.6 ns     21203640  
EstrinBlockRealCoeffsRealArg8<double>/512                      169 ns          169 ns      4209488  
EstrinBlockRealCoeffsRealArg8<double>/4096                    1306 ns         1306 ns       531596  
EstrinBlockRealCoeffsRealArg8<double>/32768                  12713 ns        12709 ns        54942  
EstrinBlockRealCoeffsRealArg8<double>_BigO                    0.03 NlgN       0.03 NlgN  
EstrinBlockRealCoeffsRealArg8<double>_RMS                        1 %             1 %  
EstrinBlockRealCoeffsRealArg16<float>/1                       4.73 ns         4.69 ns    148460465  
EstrinBlockRealCoeffsRealArg16<float>/8                       14.1 ns         14.0 ns     44644850  
EstrinBlockRealCoeffsRealArg16<float>/64                      42.8 ns         42.8 ns     15971525  
EstrinBlockRealCoeffsRealArg16<float>/512                      247 ns          247 ns      2736395  
EstrinBlockRealCoeffsRealArg16<float>/4096                    2012 ns         2011 ns       373692  
EstrinBlockRealCoeffsRealArg16<float>/32768                  16538 ns        16537 ns        40991  
EstrinBlockRealCoeffsRealArg16<float>_BigO                    0.50 N          0.50 N  
EstrinBlockRealCoeffsRealArg16<float>_RMS                        1 %             1 %  
EstrinBlockRealCoeffsRealArg16<double>/1                      4.50 ns         4.50 ns    161435391  
EstrinBlockRealCoeffsRealArg16<double>/8                      14.0 ns         14.0 ns     51456211  
EstrinBlockRealCoeffsRealArg16<double>/64                     39.1 ns         39.1 ns     18057386  
EstrinBlockRealCoeffsRealArg16<double>/512                     210 ns          210 ns      3352121  
EstrinBlockRealCoeffsRealArg16<double>/4096                   1576 ns         1574 ns       433013  
EstrinBlockRealCoeffsRealArg16<double>/32768                 12653 ns        12651 ns        55772  
EstrinBlockRealCoeffsRealArg16<double>_BigO                   0.39 N          0.39 N  
EstrinBlockRealCoeffsRealArg16<double>_RMS                       0 %             0 %  
```  
  
Clearly 8 is the best block size.  
The performance was only slightly worse than standard Estrin for floats, and better/same for doubles.  
  
One could also try using the even/odd trick of the second order Horner to try and reclaim the last speed difference.  
  
Not sure if the extra code is worth it for avoiding the need for "scratch space"?

---

## Comment 23

> Username: NAThompson  
> Created_at: 2023-02-04 16:19:49 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1416792598  

@jzmaddock : Mind me merging this? I wanna use it in the Daubechies MR and it's a bit of a pain to fork off forks.

---

## Comment 24

> Username: jzmaddock  
> Created_at: 2023-02-04 18:29:34 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1416819983  

@NAThompson Go for it!

---

## Comment 25

> Username: NAThompson  
> Created_at: 2023-02-04 18:35:01 UTC  
> Url: https://github.com/boostorg/math/pull/932#issuecomment-1416821111  

@thomasahle : Huge thanks for pushing on this; it's a really interesting algorithm and looks like it might be a major improvement to some of the rational function approximations (though we probably still need to check accuracy pretty carefully in those cases).  
  
As to your other ideas: I'm not ignoring them! I just felt like "done" was the correct action at this juncture; we can and should revisit you blocking ideas.  
  
@jzmaddock , @mborland : Thanks so much for spending your mental energies on this; it wouldn't have been very good without y'alls help.

---
