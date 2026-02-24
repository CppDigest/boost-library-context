# #250 Better gcd: Lehmers algorithm [Merged]

> Username: jzmaddock  
> Created at: 2020-06-19 19:10:30 UTC  
> Updated at: 2021-01-30 16:10:25 UTC  
> Merged at: 2020-07-19 09:05:10 UTC  
> Closed at: 2020-07-19 09:05:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/250  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2020-06-20 12:45:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/250#issuecomment-646990097  

@jzmaddock : Commit the benchmarks too!

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-06-20 17:31:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/250#issuecomment-647024579  

> Commit the benchmarks too!  
  
:)  Getting there, done now.  
  
Still a work in progress, but here's the current results for msvc, scores are relative to GMP assigned a score of 1 on each row.  
  
  
Bits | Previous | PR  
-- | -- | --  
  |   |    
1024 | 3.9398084234 | 2.4983388815  
2048 | 5.161766933 | 2.695700359  
4096 | 6.8414634574 | 2.7500000172  
8192 | 8.380952381 | 2.9047619048  
16384 | 10.5396825397 | 2.9841269841  
32678 | 11.5585585586 | 3

---

## Comment 3

> Username: NAThompson  
> Created_at: 2020-06-21 17:15:17 UTC  
> Updated_at: 2020-06-21 17:44:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/250#issuecomment-647156010  

@jzmaddock : I changed the units on your benchmarks to make it not print so many insignificant digits. BTW, is there a reason why you are running the `BM_gcd_old` twice?  
  
https://github.com/boostorg/multiprecision/blob/86b00c2f48a17854dd4f8b11e73a46b9789e1e9e/performance/gcd_bench.cpp#L207-L209  
  
Note: I'm getting a very nice improvement here:  
  
```  
$  ./performance/gcd_bench.x --benchmark_min_time=3  
------------------------------------------------------------------------  
Benchmark                              Time             CPU   Iterations  
------------------------------------------------------------------------  
BM_gcd_old<cpp_int>/256             3.99 ms         3.99 ms         1054  
BM_gcd_old<cpp_int>/512             10.2 ms         10.2 ms          415  
BM_gcd_old<cpp_int>/1024            25.0 ms         25.0 ms          168  
BM_gcd_old<cpp_int>/2048            69.1 ms         69.1 ms           61  
BM_gcd_old<cpp_int>/4096             211 ms          211 ms           20  
BM_gcd_old<cpp_int>/8192             701 ms          701 ms            6  
BM_gcd_old<cpp_int>/16384           2594 ms         2594 ms            2  
BM_gcd_old<cpp_int>/32768           9451 ms         9449 ms            1  
BM_gcd_old<cpp_int>/65536          36127 ms        36126 ms            1  
BM_gcd_old<cpp_int>_BigO            8.44 N^2        8.44 N^2    
BM_gcd_old<cpp_int>_RMS                3 %             3 %      
BM_gcd_current<cpp_int>/256         2.87 ms         2.87 ms         1463  
BM_gcd_current<cpp_int>/512         6.05 ms         6.05 ms          688  
BM_gcd_current<cpp_int>/1024        13.5 ms         13.5 ms          310  
BM_gcd_current<cpp_int>/2048        31.7 ms         31.7 ms          132  
BM_gcd_current<cpp_int>/4096        76.6 ms         76.6 ms           54  
BM_gcd_current<cpp_int>/8192         208 ms          208 ms           20  
BM_gcd_current<cpp_int>/16384        633 ms          633 ms            7  
BM_gcd_current<cpp_int>/32768       2143 ms         2143 ms            2  
BM_gcd_current<cpp_int>/65536       7698 ms         7698 ms            1  
BM_gcd_current<cpp_int>_BigO        1.81 N^2        1.81 N^2    
BM_gcd_current<cpp_int>_RMS            8 %             8 %      
BM_gcd_current<mpz_int>/256         1.57 ms         1.57 ms         2658  
BM_gcd_current<mpz_int>/512         3.94 ms         3.94 ms         1067  
BM_gcd_current<mpz_int>/1024        7.57 ms         7.57 ms          554  
BM_gcd_current<mpz_int>/2048        17.0 ms         17.0 ms          250  
BM_gcd_current<mpz_int>/4096        36.5 ms         36.5 ms          111  
BM_gcd_current<mpz_int>/8192        86.8 ms         86.8 ms           49  
BM_gcd_current<mpz_int>/16384        211 ms          211 ms           20  
BM_gcd_current<mpz_int>/32768        600 ms          600 ms            7  
BM_gcd_current<mpz_int>/65536       1604 ms         1604 ms            3  
BM_gcd_current<mpz_int>_BigO     1446.32 NlgN    1446.27 NlgN   
BM_gcd_current<mpz_int>_RMS           23 %            23 %      
```  
  
One thing I noticed is that the `mpz_int` version doesn't have any divisions in its asm. However, the division in the `cpp_int` version is the slowest operation:  
  
![cpp_int_gcd](https://user-images.githubusercontent.com/5459618/85231484-5a1cfd80-b3c5-11ea-822c-deaba4dcf4b0.png)

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2020-06-21 18:45:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/250#issuecomment-647165913  

> @NAThompson :BTW, is there a reason why you are running the BM_gcd_old twice?  
  
I was getting screwy/slow results from whatever run went first, so it was just a safeguard against deluding myself ;)  
  
BTW I have a double-limb version working which makes a huge difference on msvc, but is marginally slower for gcc since it involves `__int128` arithmetic.  The next job is start looking at which arithmetic operations can be streamlined.  
  
With regard to the division: In Jebelean's paper on the GMP version, he notes that since the quotient is usually small, stepwise subtraction to find the quotient is often faster, falling back to division only when it's clear that the quotient isn't so small after all.  It's another thing to experiment with - I hoped that since that paper is now fairly old (In computer hardware terms) that these kinds of tricks would no longer be required but we'll see!

---

## Comment 5

> Username: NAThompson  
> Created_at: 2020-06-21 20:35:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/250#issuecomment-647178416  

> I was getting screwy/slow results from whatever run went first, so it was just a safeguard against deluding myself ;)  
  
I've seen that too; the CPU heats up and then downclocks in the middle of the run. At least on Linux you can stop this with  
  
```  
$ sudo cpupower frequency-set --governor performance  
```

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2020-07-15 18:11:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/250#issuecomment-658921389  

I think this is about ready now: fixed a few bugs, added much stricter assertions and tests, and implemented the double-digit version of the algorithm.  
  
Performance relative to GMP first:  
  
  
  
GCC-10: |   |    
-- | -- | --  
  |   |    
Bits | Old | New  
512 | 3.00 | 1.72  
1024 | 4.06 | 1.71  
2048 | 5.39 | 1.71  
4096 | 7.84 | 1.69  
8192 | 11.51 | 1.65  
16384 | 16.00 | 1.64  
32768 | 22.30 | 1.73  
  
  
MSVC-14.2: |   |    
-- | -- | --  
  |   |    
Bits | Old | New  
512 | 3.05 | 1.34  
1024 | 3.87 | 1.40  
2048 | 5.07 | 1.60  
4096 | 6.95 | 1.43  
8192 | 8.64 | 1.46  
16384 | 10.49 | 1.36  
32768 | 11.85 | 1.33  
  
Raw performance data:  
  
```  
GCC-10:  
Run on (8 X 1992 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x4)  
  L1 Instruction 32K (x4)  
  L2 Unified 262K (x4)  
  L3 Unified 8388K (x1)  
------------------------------------------------------------------------  
Benchmark                              Time             CPU   Iterations  
------------------------------------------------------------------------  
BM_gcd_current<cpp_int>/512         5.19 ms         5.19 ms         1318  
BM_gcd_current<cpp_int>/1024        11.1 ms         11.1 ms          631  
BM_gcd_current<cpp_int>/2048        24.3 ms         24.3 ms          289  
BM_gcd_current<cpp_int>/4096        53.9 ms         54.0 ms          128  
BM_gcd_current<cpp_int>/8192         127 ms          127 ms           55  
BM_gcd_current<cpp_int>/16384        339 ms          339 ms           21  
BM_gcd_current<cpp_int>/32768       1012 ms         1011 ms            7  
BM_gcd_current<cpp_int>_BigO        0.97 N^2        0.96 N^2  
BM_gcd_current<cpp_int>_RMS           19 %            19 %  
BM_gcd_old<cpp_int>/512             9.63 ms         9.62 ms          723  
BM_gcd_old<cpp_int>/1024            26.3 ms         26.4 ms          268  
BM_gcd_old<cpp_int>/2048            76.6 ms         76.6 ms           91  
BM_gcd_old<cpp_int>/4096             250 ms          250 ms           28  
BM_gcd_old<cpp_int>/8192             886 ms          885 ms            8  
BM_gcd_old<cpp_int>/16384           3317 ms         3312 ms            2  
BM_gcd_old<cpp_int>/32768          12990 ms        13000 ms            1  
BM_gcd_old<cpp_int>_BigO           12.12 N^2       12.13 N^2  
BM_gcd_old<cpp_int>_RMS                2 %             2 %  
BM_gcd_current<mpz_int>/512         3.02 ms         3.02 ms         2333  
BM_gcd_current<mpz_int>/1024        6.47 ms         6.48 ms         1093  
BM_gcd_current<mpz_int>/2048        14.2 ms         14.2 ms          498  
BM_gcd_current<mpz_int>/4096        31.9 ms         31.9 ms          220  
BM_gcd_current<mpz_int>/8192        76.9 ms         76.9 ms           91  
BM_gcd_current<mpz_int>/16384        207 ms          207 ms           35  
BM_gcd_current<mpz_int>/32768        583 ms          583 ms           12  
BM_gcd_current<mpz_int>_BigO        0.56 N^2        0.56 N^2  
BM_gcd_current<mpz_int>_RMS           22 %            22 %  
BM_gcd_current<mpz_int>/512         3.01 ms         3.01 ms         2321  
  
MSVC-14.2:  
Run on (8 X 1992 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x4)  
  L1 Instruction 32K (x4)  
  L2 Unified 262K (x4)  
  L3 Unified 8388K (x1)  
------------------------------------------------------------------------  
Benchmark                              Time             CPU   Iterations  
------------------------------------------------------------------------  
BM_gcd_old<cpp_int>/512             14.0 ms         14.0 ms          503  
BM_gcd_old<cpp_int>/1024            38.5 ms         38.5 ms          181  
BM_gcd_old<cpp_int>/2048             115 ms          115 ms           61  
BM_gcd_old<cpp_int>/4096             375 ms          376 ms           19  
BM_gcd_old<cpp_int>/8192            1314 ms         1316 ms            5  
BM_gcd_old<cpp_int>/16384           4915 ms         4914 ms            2  
BM_gcd_old<cpp_int>/32768          18787 ms        18781 ms            1  
BM_gcd_old<cpp_int>_BigO           17.55 N^2       17.55 N^2  
BM_gcd_old<cpp_int>_RMS                3 %             3 %  
BM_gcd_current<cpp_int>/512         6.11 ms         6.11 ms         1120  
BM_gcd_current<cpp_int>/1024        13.9 ms         13.9 ms          503  
BM_gcd_current<cpp_int>/2048        36.3 ms         36.3 ms          211  
BM_gcd_current<cpp_int>/4096        79.5 ms         79.4 ms           76  
BM_gcd_current<cpp_int>/8192         224 ms          223 ms           33  
BM_gcd_current<cpp_int>/16384        635 ms          633 ms           10  
BM_gcd_current<cpp_int>/32768       2114 ms         2109 ms            3  
BM_gcd_current<cpp_int>_BigO        2.00 N^2        1.99 N^2  
BM_gcd_current<cpp_int>_RMS           13 %            13 %  
BM_gcd_old<cpp_int>/512             13.9 ms         13.9 ms          503  
BM_gcd_old<cpp_int>/1024            38.5 ms         38.5 ms          182  
BM_gcd_old<cpp_int>/2048             116 ms          115 ms           61  
BM_gcd_old<cpp_int>/4096             387 ms          387 ms           19  
BM_gcd_old<cpp_int>/8192            1320 ms         1322 ms            5  
BM_gcd_old<cpp_int>/16384           4902 ms         4898 ms            2  
BM_gcd_old<cpp_int>/32768          18798 ms        18797 ms            1  
BM_gcd_old<cpp_int>_BigO           17.56 N^2       17.56 N^2  
BM_gcd_old<cpp_int>_RMS                3 %             3 %  
BM_gcd_current<mpz_int>/512         4.55 ms         4.55 ms         1508  
BM_gcd_current<mpz_int>/1024        9.95 ms         9.96 ms          700  
BM_gcd_current<mpz_int>/2048        22.7 ms         22.7 ms          309  
BM_gcd_current<mpz_int>/4096        55.7 ms         55.7 ms          124  
BM_gcd_current<mpz_int>/8192         152 ms          153 ms           46  
BM_gcd_current<mpz_int>/16384        467 ms          467 ms           15  
BM_gcd_current<mpz_int>/32768       1587 ms         1586 ms            4  
BM_gcd_current<mpz_int>_BigO        1.50 N^2        1.50 N^2  
BM_gcd_current<mpz_int>_RMS           11 %            11 %  
```

---

## Review 7 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-15 18:25:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/250#pullrequestreview-449220949  

📁 include/boost/multiprecision/cpp_int/misc.hpp

```diff
 518 |+ //
 519 |+ // The complexity of the algorithm given by Sorenson is roughly O(ln^2(N)) for
 520 |+ // two N bit numbers.
```

> Username: NAThompson  
> Created_at: 2020-07-15 18:25:08 UTC  
> Updated_at: 2020-07-19 09:04:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/250#discussion_r455254629  

Wait, isn't the benchmark showing O(N^2) complexity for two N-bit numbers?

> Username: jzmaddock  
> Created_at: 2020-07-15 18:44:18 UTC  
> Updated_at: 2020-07-19 09:04:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/250#discussion_r455265805  

It alternates from run to run between O(n^2) and O(NlnN), this is true of GMP also.  I don't think that either complexity is actually correct.


---

## Review 8 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-15 18:27:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/250#pullrequestreview-449222277  

📁 performance/gcd_bench.cpp

```diff
 211 |+ BENCHMARK_TEMPLATE(BM_gcd_old, cpp_int)->RangeMultiplier(2)->Range(lower_range, upper_range)->Unit(benchmark::kMillisecond)->Complexity();
 212 |+ BENCHMARK_TEMPLATE(BM_gcd_current, mpz_int)->RangeMultiplier(2)->Range(lower_range, upper_range)->Unit(benchmark::kMillisecond)->Complexity();
 213 |+ BENCHMARK_TEMPLATE(BM_gcd_current, mpz_int)->RangeMultiplier(2)->Range(lower_range, upper_range)->Unit(benchmark::kMillisecond)->Complexity();
```

> Username: NAThompson  
> Created_at: 2020-07-15 18:27:01 UTC  
> Updated_at: 2020-07-19 09:04:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/250#discussion_r455255891  

I think now that this is finished, we can remove the duplicated performance tests since you'd demonstrated an order of magnitude speedup. It's a pretty unambiguous win now!

> Username: jzmaddock  
> Created_at: 2020-07-15 18:44:32 UTC  
> Updated_at: 2020-07-19 09:04:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/250#discussion_r455265926  

Nod.


---

## Review 9 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-15 18:29:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/250#pullrequestreview-449224221  

📁 performance/gcd_bench.cpp

```diff
   1 |+ 
```

> Username: NAThompson  
> Created_at: 2020-07-15 18:29:50 UTC  
> Updated_at: 2020-07-19 09:04:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/250#discussion_r455257458  

Do we need the copyright notice here?

> Username: jzmaddock  
> Created_at: 2020-07-15 18:45:12 UTC  
> Updated_at: 2020-07-19 09:04:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/250#discussion_r455266346  

Yes, just seen that in the failed inspection report, I'll wait for the other CI tests to cycle before making a few cosmetic changes like this.


---

## Review 10 [Commented]

> Username: NAThompson  
> Created_at: 2020-07-15 18:31:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/250#pullrequestreview-449225237  

📁 performance/gcd_bench.cpp

```diff
 199 |+    {
 200 |+       for (unsigned i = 0; i < a.size(); ++i)
 201 |+          eval_gcd(c[i].backend(), a[i].backend(), b[i].backend());
```

> Username: NAThompson  
> Created_at: 2020-07-15 18:31:10 UTC  
> Updated_at: 2020-07-19 09:04:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/250#discussion_r455258348  

Wait, why are you doing an eval_gcd on the entire vector? Why not just do it on two single numbers?

> Username: jzmaddock  
> Created_at: 2020-07-15 18:47:54 UTC  
> Updated_at: 2020-07-19 09:04:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/250#discussion_r455267903  

GCD performance is highly dependent on the inputs, you want to try and get a decent average.  To pick a trivial example, if one of the numbers has a lot of consecutive zero bits, then binary GCD would look way better than it actually is.


---

## Comment 11

> Username: NAThompson  
> Created_at: 2020-07-15 18:32:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/250#issuecomment-658931869  

@jzmaddock : I wasn't able to give a super meaningful review, but this is awesome. I also ran it under `-fsanitize=address -fsanitize=undefined` and found nothing.

---
