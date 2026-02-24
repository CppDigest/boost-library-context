# #763 Implement logaddexp [Merged]

> Username: mborland  
> Created at: 2022-02-16 12:33:00 UTC  
> Updated at: 2022-02-24 16:01:48 UTC  
> Merged at: 2022-02-24 15:55:26 UTC  
> Closed at: 2022-02-24 15:55:26 UTC  
> Url: https://github.com/boostorg/math/pull/763  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2022-02-16 17:24:42 UTC  
> Url: https://github.com/boostorg/math/pull/763#issuecomment-1041906471  

@mborland : Could you quickly google ngram for logsumexp vs logaddexp? I think logsumexp is more commonly used . . .

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-02-16 17:52:48 UTC  
> Url: https://github.com/boostorg/math/pull/763#issuecomment-1041933266  

logsumexp is the multi-argument version, in the 2-arg case it degenerates to Matt's code I believe?  But yes, I imagine that the multi-arg case is much more common.  
  
I was thinking that we might require 3 interfaces, differentiated by suitable use of enable_if:  
  
1) A variable-arg version of Matt's existing code for 1...N arguments.  
2) A pair of iterators.  
3) A range-like object with begin() and end().

---

## Comment 3

> Username: NAThompson  
> Created_at: 2022-02-17 02:31:26 UTC  
> Updated_at: 2022-02-17 06:22:33 UTC  
> Url: https://github.com/boostorg/math/pull/763#issuecomment-1042507117  

> I was thinking that we might require 3 interfaces:  
  
Looks like we're gonna need 3 interface to `max`, as the [stable calculation](https://en.wikipedia.org/wiki/LogSumExp) is  
  
x: = [x0,x1,...xn]  
m := max(x)  
LSE(x0, x1, ..., xn) := m + log(exp(x0-m) + exp(x1-m) + ... exp(xn-m))  
  
Since the max requires O(n) ops, can we do a partial sort in O(n) ops which would (partially) allow us to add the exp terms from smallest to largest?  
  
That wikipedia page also indicates that the derivative of LSE is useful as well; the bounding invariant in the first equation might be very useful for unit testing.  
  
[This](https://nhigham.com/2021/01/05/what-is-the-log-sum-exp-function/) is also a nice treatment; looks like equation (#) from that document is the best way I've seen to do it.

---

## Review 4 [Commented]

> Username: NAThompson  
> Created_at: 2022-02-17 16:37:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/763#pullrequestreview-886240688  

📁 test/logaddexp_test.cpp

```diff
  46 |+     // See: https://drone.cpp.al/boostorg/math/607/1/2 line 1508
  47 |+     #ifndef __SANITIZE_ADDRESS__
  48 |+     CHECK_ULP_CLOSE(Real(3.5e-50l), exp(spot12), 1);
```

> Username: NAThompson  
> Created_at: 2022-02-17 16:37:08 UTC  
> Updated_at: 2022-02-17 16:37:09 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r809247567  

Wait, what does Asan complain about?

> Username: mborland  
> Created_at: 2022-02-17 16:40:41 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r809251148  

The test failed by a pretty wide margin. I could not repeat with clangs ASAN and nothing came up on the other 510 tests.

> Username: NAThompson  
> Created_at: 2022-02-17 17:35:42 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r809303519  

Oh it's not a sanitizer issue, it's a "asan changed the rounding mode" issue?

> Username: mborland  
> Created_at: 2022-02-17 17:44:38 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r809311419  

Correct

> Username: NAThompson  
> Created_at: 2022-02-18 02:23:35 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r809624716  

@mborland : I just ran it on my system and it's only correct to 12 ulps, not 1 ulp like the test requires. Might not be a sanitizer issue . . .

> Username: NAThompson  
> Created_at: 2022-02-18 02:26:26 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r809625763  

```  
~/math ❯❯❯ make                                                                                                                            logaddexp ✭  
g++ -O3 -g --std=c++20 -I./include test/logaddexp_test.cpp  
~/math ❯❯❯ ./a.out                                                                                                                       logaddexp ✭ ◼  
Error at test/logaddexp_test.cpp:test:48:  
  ULP distance in double precision is 12, which exceeds 1, error/ulps  = 12.  
  Expected: +0.00000000000000000 = +0x1.a30ab017a211bp-165  
  Computed: +0.00000000000000000 = +0x1.a30ab017a2127p-165  
  Mollified relative error: +5.6973406474558792e-65  
Error at test/logaddexp_test.cpp:test:48:  
  ULP distance in long double precision is 12, which exceeds 1, error/ulps  = 12.  
  Expected: +0.00000000000000000 = +0x1.a30ab017a211bp-165  
  Computed: +0.00000000000000000 = +0x1.a30ab017a2127p-165  
  Mollified relative error: +5.6973406474558792e-65  
```


---

## Review 5 [Commented]

> Username: NAThompson  
> Created_at: 2022-02-17 17:40:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/763#pullrequestreview-886327494  

📁 doc/sf/logaddexp.qbk

```diff
 133 |+ logsumexp_performance<long double>/524288/real_time     7205777 ns      7201394 ns           99
 134 |+ logsumexp_performance<long double>/1048576/real_time   14511913 ns     14482980 ns           49
 135 |+ logsumexp_performance<long double>/real_time_BigO         13.82 N         13.80 N
```

> Username: NAThompson  
> Created_at: 2022-02-17 17:40:33 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r809307879  

Note that on an M1 Pro, the long double is just a double. So you can remove some text here by not reporting the long double numbers.

> Username: mborland  
> Created_at: 2022-02-17 17:45:15 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r809311970  

Do you want to run the performance test and paste in your results? I presume I'm in the minority running that platform.

> Username: NAThompson  
> Created_at: 2022-02-17 18:49:38 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r809364497  

I'm on the same platform.

> Username: NAThompson  
> Created_at: 2022-02-18 02:50:01 UTC  
> Updated_at: 2022-02-18 02:50:02 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r809634197  

Just ran it: I get way better performance numbers even though I'm also on M1 pro:  
  
```  
~/math ❯❯❯ ./logsumexp                                                                                                                 logaddexp ✭ ✱ ◼  
Unable to determine clock rate from sysctl: hw.cpufrequency: No such file or directory  
2022-02-17T18:48:34-08:00  
Running ./logsumexp  
Run on (10 X 24.1259 MHz CPU s)  
CPU Caches:  
  L1 Data 64 KiB (x10)  
  L1 Instruction 128 KiB (x10)  
  L2 Unified 4096 KiB (x5)  
Load Average: 2.11, 2.98, 2.97  
-----------------------------------------------------------------------------------------------  
Benchmark                                                     Time             CPU   Iterations  
-----------------------------------------------------------------------------------------------  
logsumexp_performance<float>/64/real_time                   381 ns          381 ns      1876902  
logsumexp_performance<float>/128/real_time                  758 ns          758 ns       919176  
logsumexp_performance<float>/256/real_time                 1512 ns         1512 ns       461658  
logsumexp_performance<float>/512/real_time                 3108 ns         3107 ns       231660  
logsumexp_performance<float>/1024/real_time                6039 ns         6039 ns       113364  
logsumexp_performance<float>/2048/real_time               12068 ns        12068 ns        57951  
logsumexp_performance<float>/4096/real_time               24141 ns        24141 ns        28999  
logsumexp_performance<float>/8192/real_time               48300 ns        48296 ns        14502  
logsumexp_performance<float>/16384/real_time              96602 ns        96597 ns         7124  
logsumexp_performance<float>/32768/real_time             193835 ns       193716 ns         3624  
logsumexp_performance<float>/65536/real_time             391079 ns       390490 ns         1809  
logsumexp_performance<float>/131072/real_time            788897 ns       786846 ns          881  
logsumexp_performance<float>/262144/real_time           1576679 ns      1572358 ns          439  
logsumexp_performance<float>/524288/real_time           3096897 ns      3096596 ns          223  
logsumexp_performance<float>/1048576/real_time          6230387 ns      6226829 ns          111  
logsumexp_performance<float>/real_time_BigO                5.94 N          5.94 N  
logsumexp_performance<float>/real_time_RMS                    1 %             1 %  
logsumexp_performance<double>/64/real_time                  381 ns          381 ns      1860810  
logsumexp_performance<double>/128/real_time                 766 ns          766 ns       912914  
logsumexp_performance<double>/256/real_time                1528 ns         1527 ns       453501  
logsumexp_performance<double>/512/real_time                3047 ns         3045 ns       228287  
logsumexp_performance<double>/1024/real_time               6048 ns         6047 ns       113350  
logsumexp_performance<double>/2048/real_time              12079 ns        12079 ns        57846  
logsumexp_performance<double>/4096/real_time              24146 ns        24146 ns        28979  
logsumexp_performance<double>/8192/real_time              48430 ns        48400 ns        14498  
logsumexp_performance<double>/16384/real_time             97772 ns        97628 ns         7238  
logsumexp_performance<double>/32768/real_time            194687 ns       194544 ns         3580  
logsumexp_performance<double>/65536/real_time            389308 ns       389047 ns         1788  
logsumexp_performance<double>/131072/real_time           775900 ns       775851 ns          894  
logsumexp_performance<double>/262144/real_time          1550739 ns      1550722 ns          450  
logsumexp_performance<double>/524288/real_time          3098859 ns      3098655 ns          226  
logsumexp_performance<double>/1048576/real_time         6203986 ns      6203777 ns          112  
logsumexp_performance<double>/real_time_BigO               5.92 N          5.92 N  
logsumexp_performance<double>/real_time_RMS                   0 %             0 %  
```  
  
Did you compile at `-O3`?

> Username: NAThompson  
> Created_at: 2022-02-18 02:54:40 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r809635817  

I also get just a *bit* of performance out of:  
  
```diff  
diff --git a/include/boost/math/special_functions/logsumexp.hpp b/include/boost/math/special_functions/logsumexp.hpp  
index 1a9fe38bc..69a5d21e6 100644  
--- a/include/boost/math/special_functions/logsumexp.hpp  
+++ b/include/boost/math/special_functions/logsumexp.hpp  
@@ -21,25 +21,15 @@ Real logsumexp(ForwardIterator first, ForwardIterator last)  
     using std::exp;  
     using std::log1p;  
  
-    const Real max_val = *std::max_element(first, last);  
+    auto elem = std::max_element(first, last);  
+    const Real max_val = *elem;  
  
     Real result = 0;  
-    bool skip_k = true; // Flag in case there are multiple of the same max value  
     while (first != last)  
     {  
-        if (*first != max_val)  
-        {  
-            result += exp(*first - max_val);  
-        }  
-        else if (!skip_k)  
-        {  
-            ++result;  
-        }  
-        else  
-        {  
-            skip_k = false;  
-        }  
-  
+       if (first != elem) {  
+           result += exp(*first - max_val);  
+       }  
         ++first;  
     }  
```


---

## Review 6 [Commented]

> Username: NAThompson  
> Created_at: 2022-02-18 02:24:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/763#pullrequestreview-886766313  

📁 test/logaddexp_test.cpp

```diff
  41 |+     Real spot12 = logaddexp(spot1, spot2);
  42 |+ 
  43 |+     CHECK_ULP_CLOSE(Real(-113.87649168120691620521145211223320721849348983622l), spot12, 1);
```

> Username: NAThompson  
> Created_at: 2022-02-18 02:24:50 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r809625197  

Wouldn't `CHECK_ULP_CLOSE(spot1 + spot2, spot12, 1)` be more clear?


---

## Review 7 [Commented]

> Username: NAThompson  
> Created_at: 2022-02-18 02:32:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/763#pullrequestreview-886769705  

📁 test/logsumexp_test.cpp

```diff
  24 |+     Real spot12 = logsumexp(spot1, spot2);
  25 |+ 
  26 |+     CHECK_ULP_CLOSE(Real(-113.87649168120691620521145211223320721849348983622l), spot12, 1);
```

> Username: NAThompson  
> Created_at: 2022-02-18 02:32:19 UTC  
> Updated_at: 2022-02-18 02:32:36 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r809627810  

Would do:  
  
```cpp  
Real x1 = 1e-50l;  
Real x2 =2.5e-50l;  
Real spot1 = static_cast<Real>(log(x1));  
Real spot2 = static_cast<Real>(log(x2));  
Real spot12 = logsumexp(spot1, spot2);  
CHECK_ULP_CLOSE(log(x1+x2), spot12, 1);  
```  
  
just to make the tested property obvious to future readers.


---

## Review 8 [Commented]

> Username: NAThompson  
> Created_at: 2022-02-18 02:33:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/763#pullrequestreview-886770211  

📁 test/logsumexp_test.cpp

```diff
  46 |+ 
  47 |+     CHECK_EQUAL(spot1234, spot1234_container);
  48 |+     CHECK_ULP_CLOSE(Real(-112.52656496425790043613106914490880983418810289233l), spot1234, 1);
```

> Username: NAThompson  
> Created_at: 2022-02-18 02:33:27 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r809628218  

Important test we're missing: x1 and x2 on *vastly* different orders of magnitude.


---

## Review 9 [Commented]

> Username: NAThompson  
> Created_at: 2022-02-18 02:34:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/763#pullrequestreview-886770780  

📁 include/boost/math/special_functions/logsumexp.hpp

```diff
  25 |+ 
  26 |+     Real result = 0;
  27 |+     bool skip_k = true; // Flag in case there are multiple of the same max value
```

> Username: NAThompson  
> Created_at: 2022-02-18 02:34:49 UTC  
> Updated_at: 2022-02-18 02:37:37 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r809628643  

Is this buying us anything? I feel like if we just ignore this test we simplify the code and still get the same answer; moreover a statistician would say this has "probability zero".


---

## Review 10 [Commented]

> Username: NAThompson  
> Created_at: 2022-02-18 02:35:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/763#pullrequestreview-886771117  

📁 include/boost/math/special_functions/logsumexp.hpp

```diff
  24 |+     const Real max_val = *std::max_element(first, last);
  25 |+ 
  26 |+     Real result = 0;
```

> Username: NAThompson  
> Created_at: 2022-02-18 02:35:36 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r809628907  

I might name this variable `arg`, as we generally `return result`.


---

## Review 11 [Commented]

> Username: NAThompson  
> Created_at: 2022-02-18 02:38:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/763#pullrequestreview-886772458  

📁 doc/sf/logaddexp.qbk

```diff
  35 |+ 
  36 |+ 
  37 |+ The function `logaddexp` computes log(exp(x1) + exp(x2)).
```

> Username: NAThompson  
> Created_at: 2022-02-18 02:38:30 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r809629876  

`logaddexp(x1, x2)`


---

## Review 12 [Commented]

> Username: NAThompson  
> Created_at: 2022-02-18 02:50:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/763#pullrequestreview-886777882  

📁 doc/sf/logaddexp.qbk

```diff
  38 |+ The function `logsumexp` is generalized to compute log(exp(x1) + exp(x2) + ...).
  39 |+ This function is useful in statistics where the calculated probabilities of events may be so small as to exceed the range of normal floating point numbers. 
  40 |+ In such cases the logarithm of the calculated probability is stored.
```

> Username: NAThompson  
> Created_at: 2022-02-18 02:50:49 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r809634459  

Might want to cite that Higham blog post because that's the formula we use to compute `logsumexp`.


---

## Comment 13

> Username: NAThompson  
> Created_at: 2022-02-18 02:59:11 UTC  
> Updated_at: 2022-02-18 03:37:33 UTC  
> Url: https://github.com/boostorg/math/pull/763#issuecomment-1043793083  

@mborland : This is excellent work. My only nitpicks:  
  
- Is there any value in the 2-arg `logaddexp`? It feels like it is somewhat unnecessary.  
- We could use some unit tests that test arguments on a *vastly* different order of magnitude; that's when the stable `max + log1p` algorithm should really shine.  
- More generally, why not generate a random vector of positive real numbers and then use log(x1 + x2 + ... xn) = lse(log(x1), log(x2), ..., log(xn))?  
- It might also be good to generate a test case which clearly fails if someone tries to change it to the naive algorithm.  
  
Here's a test I hacked up:  
  
```  
// The naive method of computation should overflow:  
template<typename Real>  
void test_overflow() {  
   using std::log;  
   Real x = log(std::numeric_limits<Real>::max()/2);  
   CHECK_ULP_CLOSE(log(std::numeric_limits<Real>::max()), logsumexp(x,x), 1);  
  
   x = std::numeric_limits<Real>::max()/4;  
   CHECK_ULP_CLOSE(x, logsumexp(x,x,x,x), 1);  
  
   Real y = std::sqrt(std::numeric_limits<Real>::epsilon())*x;  
   CHECK_ULP_CLOSE(log(x+y), logsumexp(log(x),log(y)), 1);  
   x = log(std::numeric_limits<Real>::max()/2);  
   CHECK_ULP_CLOSE(std::log1p(x), logsumexp(log(x), 0.0), 1);  
   x = std::numeric_limits<Real>::epsilon()/4;  
   CHECK_ULP_CLOSE(std::log1p(x), logsumexp(log(x), 0.0), 1);  
}  
```  
  
The last one fails for reasons I don't understand . . .

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2022-02-18 13:42:20 UTC  
> Url: https://github.com/boostorg/math/pull/763#issuecomment-1044544881  

Nick may have mentioned this already, but the test:  
  
```  
    // GCCs ASAN does not like this test so suppress when active  
    // See: https://drone.cpp.al/boostorg/math/607/1/2 line 1508  
    #ifndef __SANITIZE_ADDRESS__  
    CHECK_ULP_CLOSE(Real(3.5e-50l), exp(spot12), 1);  
    #endif  
```  
  
Fails for me with msvc:  
  
```  
Error at D:\data\boost\boost\libs\math\test\logaddexp_test.cpp:test:48:  
  ULP distance in double precision is 12, which exceeds 1, error/ulps  = 12.  
  Expected: +0.00000000000000000 = +0x1.a30ab017a211bp-165  
  Computed: +0.00000000000000000 = +0x1.a30ab017a2127p-165  
  Mollified relative error: +5.6973406474558792e-65  
```  
  
I think the issue here, is that the effect of calling `exp` is generally to make an absolute error relative, so if the result held in spot12 is out by 1ulp, then the preceding test will still pass, but the exp will magnify the error to be up to `|spot12| * epsilon` in magnitude.

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2022-02-18 16:58:44 UTC  
> Url: https://github.com/boostorg/math/pull/763#issuecomment-1044840746  

MSVC performance results:  
  
```  
Run on (8 X 1992 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x4)  
  L1 Instruction 32K (x4)  
  L2 Unified 262K (x4)  
  L3 Unified 8388K (x1)  
-----------------------------------------------------------------------------  
Benchmark                                   Time             CPU   Iterations  
-----------------------------------------------------------------------------  
logaddexp_performance<float>             1.58 ns         1.57 ns    448000000  
logaddexp_performance<double>            1.51 ns         1.51 ns    497777778  
logaddexp_performance<long double>       1.56 ns         1.57 ns    497777778  
```  
  
And:  
  
```  
Run on (8 X 1992 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x4)  
  L1 Instruction 32K (x4)  
  L2 Unified 262K (x4)  
  L3 Unified 8388K (x1)  
-----------------------------------------------------------------------------------------------  
Benchmark                                                     Time             CPU   Iterations  
-----------------------------------------------------------------------------------------------  
logsumexp_performance<float>/64/real_time                   512 ns          516 ns      1000000  
logsumexp_performance<float>/128/real_time                 1003 ns          986 ns       728594  
logsumexp_performance<float>/256/real_time                 1963 ns         2001 ns       359132  
logsumexp_performance<float>/512/real_time                 3918 ns         3919 ns       179395  
logsumexp_performance<float>/1024/real_time                7703 ns         7723 ns        84969  
logsumexp_performance<float>/2048/real_time               15997 ns        15790 ns        44530  
logsumexp_performance<float>/4096/real_time               30915 ns        31473 ns        22837  
logsumexp_performance<float>/8192/real_time               63001 ns        62234 ns        10796  
logsumexp_performance<float>/16384/real_time             128639 ns       130672 ns         5620  
logsumexp_performance<float>/32768/real_time             268432 ns       269599 ns         2666  
logsumexp_performance<float>/65536/real_time             550086 ns       550365 ns         1164  
logsumexp_performance<float>/131072/real_time           1007520 ns      1021984 ns          688  
logsumexp_performance<float>/262144/real_time           1976225 ns      1958689 ns          351  
logsumexp_performance<float>/524288/real_time           4062535 ns      4083807 ns          176  
logsumexp_performance<float>/1048576/real_time          8363232 ns      8333333 ns           90  
logsumexp_performance<float>/real_time_BigO                7.91 N          7.90 N  
logsumexp_performance<float>/real_time_RMS                    4 %             3 %  
logsumexp_performance<double>/64/real_time                  582 ns          581 ns      1183702  
logsumexp_performance<double>/128/real_time                1200 ns         1191 ns       603378  
logsumexp_performance<double>/256/real_time                2547 ns         2550 ns       281847  
logsumexp_performance<double>/512/real_time                5312 ns         5307 ns       135435  
logsumexp_performance<double>/1024/real_time               9647 ns         9695 ns        69300  
logsumexp_performance<double>/2048/real_time              18521 ns        18386 ns        36542  
logsumexp_performance<double>/4096/real_time              37065 ns        37082 ns        18540  
logsumexp_performance<double>/8192/real_time              75715 ns        74801 ns         9400  
logsumexp_performance<double>/16384/real_time            156009 ns       154599 ns         4447  
logsumexp_performance<double>/32768/real_time            292937 ns       295307 ns         2381  
logsumexp_performance<double>/65536/real_time            584194 ns       582539 ns         1207  
logsumexp_performance<double>/131072/real_time          1175421 ns      1174428 ns          612  
logsumexp_performance<double>/262144/real_time          2382460 ns      2375422 ns          296  
logsumexp_performance<double>/524288/real_time          4720312 ns      4783163 ns          147  
logsumexp_performance<double>/1048576/real_time         9570338 ns      9457237 ns           76  
logsumexp_performance<double>/real_time_BigO               9.10 N          9.04 N  
logsumexp_performance<double>/real_time_RMS                   1 %             1 %  
logsumexp_performance<long double>/64/real_time             599 ns          599 ns      1147319  
logsumexp_performance<long double>/128/real_time           1163 ns         1166 ns       602863  
logsumexp_performance<long double>/256/real_time           2270 ns         2287 ns       307495  
logsumexp_performance<long double>/512/real_time           4523 ns         4491 ns       153067  
logsumexp_performance<long double>/1024/real_time          8833 ns         8845 ns        77731  
logsumexp_performance<long double>/2048/real_time         17855 ns        17582 ns        38214  
logsumexp_performance<long double>/4096/real_time         35755 ns        35488 ns        19813  
logsumexp_performance<long double>/8192/real_time         86561 ns        86875 ns         8813  
logsumexp_performance<long double>/16384/real_time       159225 ns       161993 ns         4244  
logsumexp_performance<long double>/32768/real_time       309235 ns       307948 ns         2334  
logsumexp_performance<long double>/65536/real_time       607855 ns       597465 ns         1203  
logsumexp_performance<long double>/131072/real_time     1164873 ns      1147023 ns          613  
logsumexp_performance<long double>/262144/real_time     2297293 ns      2297794 ns          306  
logsumexp_performance<long double>/524288/real_time     4664338 ns      4614094 ns          149  
logsumexp_performance<long double>/1048576/real_time    9226792 ns      9417808 ns           73  
logsumexp_performance<long double>/real_time_BigO          8.82 N          8.94 N  
logsumexp_performance<long double>/real_time_RMS              1 %             2 %  
```

---

## Comment 16

> Username: jzmaddock  
> Created_at: 2022-02-18 17:34:38 UTC  
> Url: https://github.com/boostorg/math/pull/763#issuecomment-1044889365  

Mingw GCC-11:  
  
```  
02/18/22 17:31:59  
Running D:\data\boost\boost\libs\math\reporting\performance\a.exe  
Run on (8 X 1992 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x4)  
  L1 Instruction 32K (x4)  
  L2 Unified 262K (x4)  
  L3 Unified 8388K (x1)  
-----------------------------------------------------------------------------------------------  
Benchmark                                                     Time             CPU   Iterations  
-----------------------------------------------------------------------------------------------  
logsumexp_performance<float>/64/real_time                  2372 ns         2377 ns       302416  
logsumexp_performance<float>/128/real_time                 4766 ns         4692 ns       139875  
logsumexp_performance<float>/256/real_time                 9679 ns         9635 ns        72973  
logsumexp_performance<float>/512/real_time                19068 ns        18950 ns        36279  
logsumexp_performance<float>/1024/real_time               37528 ns        37382 ns        18809  
logsumexp_performance<float>/2048/real_time               75306 ns        76360 ns         9208  
logsumexp_performance<float>/4096/real_time              149899 ns       148328 ns         4635  
logsumexp_performance<float>/8192/real_time              301610 ns       301770 ns         2330  
logsumexp_performance<float>/16384/real_time             599152 ns       595754 ns         1154  
logsumexp_performance<float>/32768/real_time            1204501 ns      1214378 ns          579  
logsumexp_performance<float>/65536/real_time            2403916 ns      2416237 ns          291  
logsumexp_performance<float>/131072/real_time           4783452 ns      4815925 ns          146  
logsumexp_performance<float>/262144/real_time           9599055 ns      9417808 ns           73  
logsumexp_performance<float>/524288/real_time          19165814 ns     19097222 ns           36  
logsumexp_performance<float>/1048576/real_time         38638420 ns     38194444 ns           18  
logsumexp_performance<float>/real_time_BigO               36.78 N         36.41 N  
logsumexp_performance<float>/real_time_RMS                    1 %             1 %  
logsumexp_performance<double>/64/real_time                 2519 ns         2531 ns       296327  
logsumexp_performance<double>/128/real_time                4532 ns         4610 ns       149134  
logsumexp_performance<double>/256/real_time                9163 ns         9152 ns        73409  
logsumexp_performance<double>/512/real_time               18297 ns        18056 ns        38075  
logsumexp_performance<double>/1024/real_time              36737 ns        36915 ns        19047  
logsumexp_performance<double>/2048/real_time              73209 ns        73263 ns         9384  
logsumexp_performance<double>/4096/real_time             147057 ns       146913 ns         4786  
logsumexp_performance<double>/8192/real_time             291382 ns       293213 ns         2398  
logsumexp_performance<double>/16384/real_time            586158 ns       589376 ns         1193  
logsumexp_performance<double>/32768/real_time           1178344 ns      1195791 ns          588  
logsumexp_performance<double>/65536/real_time           2348488 ns      2367424 ns          297  
logsumexp_performance<double>/131072/real_time          4719443 ns      4750845 ns          148  
logsumexp_performance<double>/262144/real_time          9421503 ns      9501689 ns           74  
logsumexp_performance<double>/524288/real_time         18747703 ns     18581081 ns           37  
logsumexp_performance<double>/1048576/real_time        37765139 ns     37828947 ns           19  
logsumexp_performance<double>/real_time_BigO              35.96 N         35.97 N  
logsumexp_performance<double>/real_time_RMS                   1 %             2 %  
logsumexp_performance<long double>/64/real_time            2964 ns         2977 ns       230944  
logsumexp_performance<long double>/128/real_time           5850 ns         5914 ns       118895  
logsumexp_performance<long double>/256/real_time          11782 ns        11789 ns        58316  
logsumexp_performance<long double>/512/real_time          23635 ns        23711 ns        29654  
logsumexp_performance<long double>/1024/real_time         47045 ns        46359 ns        14830  
logsumexp_performance<long double>/2048/real_time         94129 ns        94735 ns         7422  
logsumexp_performance<long double>/4096/real_time        188474 ns       189368 ns         3713  
logsumexp_performance<long double>/8192/real_time        377218 ns       377618 ns         1862  
logsumexp_performance<long double>/16384/real_time       755470 ns       759669 ns          905  
logsumexp_performance<long double>/32768/real_time      1502798 ns      1515356 ns          464  
logsumexp_performance<long double>/65536/real_time      3009315 ns      3030711 ns          232  
logsumexp_performance<long double>/131072/real_time     6021373 ns      6009615 ns          117  
logsumexp_performance<long double>/262144/real_time    12138284 ns     12122845 ns           58  
logsumexp_performance<long double>/524288/real_time    24863843 ns     24784483 ns           29  
logsumexp_performance<long double>/1048576/real_time   48965846 ns     49107143 ns           14  
logsumexp_performance<long double>/real_time_BigO         46.80 N         46.87 N  
logsumexp_performance<long double>/real_time_RMS              2 %             1 %  
```  
  
And:  
  
```  
02/18/22 17:34:18  
Running D:\data\boost\boost\libs\math\reporting\performance\a.exe  
Run on (8 X 1992 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x4)  
  L1 Instruction 32K (x4)  
  L2 Unified 262K (x4)  
  L3 Unified 8388K (x1)  
-----------------------------------------------------------------------------  
Benchmark                                   Time             CPU   Iterations  
-----------------------------------------------------------------------------  
logaddexp_performance<float>             1.43 ns         1.44 ns    497777778  
logaddexp_performance<double>            1.56 ns         1.57 ns    448000000  
logaddexp_performance<long double>       7.30 ns         7.32 ns     89600000  
```

---

## Review 17 [Commented]

> Username: NAThompson  
> Created_at: 2022-02-22 16:42:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/763#pullrequestreview-890068959  

📁 include/boost/math/tools/random_vector.hpp

```diff
  37 | 
  38 |+ template<typename T, typename std::enable_if<std::is_floating_point<T>::value, bool>::type = true>
  39 |+ std::vector<T> generate_random_vector(std::size_t size, std::size_t seed, T min_val, T max_val)
```

> Username: NAThompson  
> Created_at: 2022-02-22 16:42:06 UTC  
> Updated_at: 2022-02-22 16:42:07 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r812144565  

Note that the `min_val, max_val` is misleading here: It's passed into a normal distribution where min_val = mean and max_val = std_dev.


---

## Comment 18

> Username: NAThompson  
> Created_at: 2022-02-22 16:43:58 UTC  
> Url: https://github.com/boostorg/math/pull/763#issuecomment-1047995018  

@mborland : Looks like you accidently committed two binary files.

---

## Review 19 [Commented]

> Username: NAThompson  
> Created_at: 2022-02-22 16:53:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/763#pullrequestreview-890084941  

📁 doc/sf/logaddexp.qbk

```diff
 135 |+ ```
 136 |+ 
 137 |+ x86_64 Performance (Mingw GCC-11)
```

> Username: NAThompson  
> Created_at: 2022-02-22 16:53:48 UTC  
> Updated_at: 2022-02-22 17:25:03 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r812155941  

Generally I only report one platform just to give people a general idea of how fast the function is and the computational complexity-the reporting can be rather verbose and I think it may make people's eyes lose focus!  
  
Also, these documents have been being built and read since 2006; we probably won't make any attempt to keep these performance numbers up to date and the CPUs will get better over time . . .


---

## Review 20 [Commented]

> Username: NAThompson  
> Created_at: 2022-02-22 17:26:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/763#pullrequestreview-890127992  

📁 include/boost/math/special_functions/logaddexp.hpp

```diff
  31 |+     const Real temp = x1 - x2;
  32 |+     
  33 |+     if (abs(temp) < std::numeric_limits<Real>::epsilon())
```

> Username: NAThompson  
> Created_at: 2022-02-22 17:26:04 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r812186294  

Could you add a unit test that fails if this branch is not here?

> Username: mborland  
> Created_at: 2022-02-23 10:26:17 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r812748022  

Removed the branch because everything still works fine without.


---

## Review 21 [Commented]

> Username: NAThompson  
> Created_at: 2022-02-23 15:46:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/763#pullrequestreview-891282362  

📁 include/boost/math/special_functions/logsumexp.hpp

```diff
  15 |+ 
  16 |+ // https://nhigham.com/2021/01/05/what-is-the-log-sum-exp-function/
  17 |+ // See equation #
```

> Username: NAThompson  
> Created_at: 2022-02-23 15:46:10 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r813034534  

"See equation (#)"


---

## Review 22 [Commented]

> Username: NAThompson  
> Created_at: 2022-02-23 15:47:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/763#pullrequestreview-891284097  

📁 doc/sf/logaddexp.qbk

```diff
 132 |+ logsumexp_performance<long double>/1048576/real_time    9226792 ns      9417808 ns           73
 133 |+ logsumexp_performance<long double>/real_time_BigO          8.82 N          8.94 N
 134 |+ logsumexp_performance<long double>/real_time_RMS              1 %             2 %
```

> Username: NAThompson  
> Created_at: 2022-02-23 15:47:22 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r813035867  

Definitely would remove the `long double` performance; it's super confusing to see that it's faster than the double but in reality on your system, `long double` is a `double` so this duplication just should the error in the measurement.

> Username: mborland  
> Created_at: 2022-02-23 16:01:36 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r813051499  

This is John's performance data since his system has long double support.

> Username: NAThompson  
> Created_at: 2022-02-23 16:04:55 UTC  
> Updated_at: 2022-02-23 16:04:56 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r813055168  

@jzmaddock : How is your long double faster than double?

> Username: jzmaddock  
> Created_at: 2022-02-23 16:09:03 UTC  
> Updated_at: 2022-02-23 16:09:04 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r813059629  

It's not.  Has Matt mixed up data from two different machines in the .qbk file?

> Username: mborland  
> Created_at: 2022-02-23 16:16:02 UTC  
> Url: https://github.com/boostorg/math/pull/763#discussion_r813066900  

I just copied and pasted the data above. I'll revert to mine.


---

## Comment 23

> Username: NAThompson  
> Created_at: 2022-02-23 15:50:54 UTC  
> Url: https://github.com/boostorg/math/pull/763#issuecomment-1048927120  

@mborland : Modulo the two nitpicky comments, I think this is ready to go. I might try to break it tonight, but assuming that I fail to find additional bugs then I'll squash merge. Any objections?

---

## Comment 24

> Username: mborland  
> Created_at: 2022-02-23 16:01:46 UTC  
> Url: https://github.com/boostorg/math/pull/763#issuecomment-1048938549  

> @mborland : Modulo the two nitpicky comments, I think this is ready to go. I might try to break it tonight, but assuming that I fail to find additional bugs then I'll squash merge. Any objections?  
  
Sounds good to me.

---

## Comment 25

> Username: NAThompson  
> Created_at: 2022-02-23 16:06:36 UTC  
> Updated_at: 2022-02-23 16:06:57 UTC  
> Url: https://github.com/boostorg/math/pull/763#issuecomment-1048943657  

@mborland : I expect this to be an extremely important feature and people will start pounding on it almost immediately after release; so I've been more OCD about this one-don't let it detract you from the fact that I think this is absolutely great work.

---

## Comment 26

> Username: NAThompson  
> Created_at: 2022-02-24 05:17:26 UTC  
> Url: https://github.com/boostorg/math/pull/763#issuecomment-1049503628  

@mborland : Just added a diff to ensure the test returns an exit status. Will merge when pipeline succeeds.

---
