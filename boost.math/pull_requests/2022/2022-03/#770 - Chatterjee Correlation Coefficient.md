# #770 Chatterjee Correlation Coefficient [Merged]

> Username: mborland  
> Created at: 2022-03-01 09:58:47 UTC  
> Updated at: 2022-05-25 15:37:12 UTC  
> Merged at: 2022-05-25 15:13:24 UTC  
> Closed at: 2022-05-25 15:13:24 UTC  
> Url: https://github.com/boostorg/math/pull/770  

See: https://arxiv.org/pdf/1909.10140.pdf

---

## Comment 1

> Username: NAThompson  
> Created_at: 2022-03-01 15:38:54 UTC  
> Url: https://github.com/boostorg/math/pull/770#issuecomment-1055574833  

@mborland : This might be one of the most cool and unique features we have in the library; thanks for taking this on.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2022-03-02 05:20:01 UTC  
> Url: https://github.com/boostorg/math/pull/770#issuecomment-1056253339  

@mborland : Do you know how to quickly recover the rank of Y_{(i)}? That's one part of the paper I didn't quite understand.

---

## Comment 3

> Username: mborland  
> Created_at: 2022-03-02 10:00:48 UTC  
> Url: https://github.com/boostorg/math/pull/770#issuecomment-1056732301  

> @mborland : Do you know how to quickly recover the rank of Y_{(i)}? That's one part of the paper I didn't quite understand.  
  
If you look at `rank.hpp` the function takes the y values and returns a `std::vector<std::size_t>` with the rank values in the same sort order as the y values. In the actual implementation I am going to have an assertion that the x values are sorted before this function is called.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2022-03-02 16:48:59 UTC  
> Url: https://github.com/boostorg/math/pull/770#issuecomment-1057145399  

> In the actual implementation I am going to have an assertion that the x values are sorted before this function is called.  
  
Ah, that's a good idea. Might want to have two:  
  
```  
sorted_chatterjee_coefficient  
```  
  
which asserts `std::is_sorted(X)` and `chatterjee_coefficient` which performs the sort.

---

## Comment 5

> Username: mborland  
> Created_at: 2022-03-03 16:01:53 UTC  
> Url: https://github.com/boostorg/math/pull/770#issuecomment-1058197788  

@NAThompson here is the performance data:  
  
```  
Running ./chatterjee_correlation_performance  
Run on (10 X 24.0565 MHz CPU s)  
CPU Caches:  
  L1 Data 64 KiB (x10)  
  L1 Instruction 128 KiB (x10)  
  L2 Unified 4096 KiB (x5)  
Load Average: 2.48, 2.44, 2.37  
-------------------------------------------------------------------------------------------  
Benchmark                                                 Time             CPU   Iterations  
-------------------------------------------------------------------------------------------  
chatterjee_correlation<float>/64/real_time              534 ns          534 ns      1268796  
chatterjee_correlation<float>/128/real_time            1097 ns         1097 ns       645575  
chatterjee_correlation<float>/256/real_time            2357 ns         2357 ns       290292  
chatterjee_correlation<float>/512/real_time            5099 ns         5099 ns       136816  
chatterjee_correlation<float>/1024/real_time          12173 ns        12173 ns        60000  
chatterjee_correlation<float>/2048/real_time          72584 ns        72584 ns        11677  
chatterjee_correlation<float>/4096/real_time         163897 ns       163895 ns         3731  
chatterjee_correlation<float>/8192/real_time         419733 ns       419733 ns         1737  
chatterjee_correlation<float>/16384/real_time        911279 ns       911278 ns          792  
chatterjee_correlation<float>/32768/real_time       1998998 ns      1998997 ns          359  
chatterjee_correlation<float>/65536/real_time       4250824 ns      4250776 ns          165  
chatterjee_correlation<float>/131072/real_time      9087735 ns      9087731 ns           78  
chatterjee_correlation<float>/262144/real_time     19238598 ns     19238622 ns           37  
chatterjee_correlation<float>/524288/real_time     41215804 ns     41215824 ns           17  
chatterjee_correlation<float>/1048576/real_time    84805198 ns     84805000 ns            8  
chatterjee_correlation<float>/real_time_BigO           4.06 NlgN       4.06 NlgN  
chatterjee_correlation<float>/real_time_RMS               2 %             2 %  
chatterjee_correlation<double>/64/real_time             529 ns          529 ns      1372474  
chatterjee_correlation<double>/128/real_time           1067 ns         1067 ns       649907  
chatterjee_correlation<double>/256/real_time           2248 ns         2248 ns       320911  
chatterjee_correlation<double>/512/real_time           4690 ns         4690 ns       145379  
chatterjee_correlation<double>/1024/real_time         10196 ns        10196 ns        58376  
chatterjee_correlation<double>/2048/real_time         67452 ns        67451 ns        10000  
chatterjee_correlation<double>/4096/real_time        139739 ns       139739 ns         3923  
chatterjee_correlation<double>/8192/real_time        396547 ns       396546 ns         1744  
chatterjee_correlation<double>/16384/real_time       866781 ns       866781 ns          744  
chatterjee_correlation<double>/32768/real_time      1965081 ns      1965079 ns          353  
chatterjee_correlation<double>/65536/real_time      4249051 ns      4249048 ns          166  
chatterjee_correlation<double>/131072/real_time     8951655 ns      8951662 ns           77  
chatterjee_correlation<double>/262144/real_time    19002963 ns     19003000 ns           36  
chatterjee_correlation<double>/524288/real_time    40619715 ns     40619778 ns           18  
chatterjee_correlation<double>/1048576/real_time   83150833 ns     83150875 ns            8  
chatterjee_correlation<double>/real_time_BigO          3.99 NlgN       3.99 NlgN  
chatterjee_correlation<double>/real_time_RMS              3 %             3 %  
```

---

## Comment 6

> Username: NAThompson  
> Created_at: 2022-03-03 20:26:19 UTC  
> Url: https://github.com/boostorg/math/pull/770#issuecomment-1058451632  

@mborland : Beautiful nlog(n) complexity just as expected.  
  
BTW looks like you accidently committed a binary file.

---

## Comment 7

> Username: mborland  
> Created_at: 2022-05-22 23:48:14 UTC  
> Url: https://github.com/boostorg/math/pull/770#issuecomment-1134023439  

@NAThompson This is good for review. The only failure in the previous run was fixing a non-ASCII character in a comment.

---

## Review 8 [Commented]

> Username: NAThompson  
> Created_at: 2022-05-23 00:33:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/770#pullrequestreview-981040943  

📁 test/test_chatterjee_correlation.cpp

```diff
  50 | 
  51 |-     // If there are no ties among the Yi’s, the maximum possible value of Xi(X, Y) is (n − 2)/(n + 1), which is attained if Yi = Xi for all i
  51 |+     // If there are no ties among the Yis, the maximum possible value of Xi(X, Y) is (n  2)/(n + 1), which is attained if Yi = Xi for all i
```

> Username: NAThompson  
> Created_at: 2022-05-23 00:33:02 UTC  
> Updated_at: 2022-05-23 00:33:03 UTC  
> Url: https://github.com/boostorg/math/pull/770#discussion_r878950719  

@mborland : Did the '-' sign get blown up there?


---

## Review 9 [Commented]

> Username: NAThompson  
> Created_at: 2022-05-23 00:34:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/770#pullrequestreview-981041248  

📁 doc/statistics/chatterjee_correlation.qbk

```diff
  38 |+ This is the problem Chatterjee's coefficient solves.
  39 |+ Let X and Y be random variables, where Y is not constant, and let (X_i, Y_i) be samples from this distribution.
  40 |+ Rearrange these samples so that X_(0) < X_{(1)} < ... X_{(n-1)} and create (X_{(i)}, Y_{(i)}).
```

> Username: NAThompson  
> Created_at: 2022-05-23 00:34:34 UTC  
> Url: https://github.com/boostorg/math/pull/770#discussion_r878950971  

@mborland : Does this render properly?  I wonder if we need to use (say) [this](https://viereck.ch/latex-to-svg/) to get the docs correct?


---

## Review 10 [Commented]

> Username: NAThompson  
> Created_at: 2022-05-23 00:35:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/770#pullrequestreview-981041392  

📁 doc/statistics/chatterjee_correlation.qbk

```diff
  46 |+ However, if the data is not infinite, the statistic may be negative.
  47 |+ If X and Y are independent, the value is zero, and if Y is a measurable function of X, then the statistic is unity.
  48 |+ The complexity is O(n log n).
```

> Username: NAThompson  
> Created_at: 2022-05-23 00:35:16 UTC  
> Updated_at: 2022-05-23 00:35:17 UTC  
> Url: https://github.com/boostorg/math/pull/770#discussion_r878951096  

Is there a way to get O(n log n) nicely rendered?


---

## Review 11 [Commented]

> Username: NAThompson  
> Created_at: 2022-05-23 00:35:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/770#pullrequestreview-981041471  

📁 doc/statistics/chatterjee_correlation.qbk

```diff
  63 |+ The function expects at least two samples, a non-constant vector Y, and the same number of X's as Y's.
  64 |+ If Y is constant, the result is a quiet NaN.
  65 |+ The data set must be sorted by x values.
```

> Username: NAThompson  
> Created_at: 2022-05-23 00:35:41 UTC  
> Url: https://github.com/boostorg/math/pull/770#discussion_r878951166  

X values.


---

## Review 12 [Commented]

> Username: NAThompson  
> Created_at: 2022-05-23 00:36:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/770#pullrequestreview-981041593  

📁 doc/statistics/chatterjee_correlation.qbk

```diff
  65 |+ The data set must be sorted by x values.
  66 |+ If there are ties in the values of X, then the statistic is random due to the random breaking of ties.
  67 |+ Of course, there is not random numbers used internally, but the result is not guaranteed to be identical on different systems.
```

> Username: NAThompson  
> Created_at: 2022-05-23 00:36:15 UTC  
> Url: https://github.com/boostorg/math/pull/770#discussion_r878951278  

"Of course, random numbers are not used internally"


---

## Comment 13

> Username: NAThompson  
> Created_at: 2022-05-23 00:43:00 UTC  
> Url: https://github.com/boostorg/math/pull/770#issuecomment-1134039006  

@mborland : Just put some trivial comments in-I think this is basically good to go.  
  
One last thing: Is there another really clean unit test we could add? I wonder if figure 2 of the attached could be used "morally" to simply ensure that we haven't done any silly scaling errors, i.e., just let Y = X, and attempt to show ξ ≈ 0.970, let Y = X^2 and show ξ ≈ 0.941, and Y = sin(X) and show ξ≈0.885.

---

## Review 14 [Commented]

> Username: NAThompson  
> Created_at: 2022-05-23 16:15:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/770#pullrequestreview-982045082  

📁 test/test_chatterjee_correlation.cpp

```diff
 102 |+ void test_paper()
 103 |+ {
 104 |+     std::vector<Real> x = boost::math::generate_random_vector<Real>(1024, 0);
```

> Username: NAThompson  
> Created_at: 2022-05-23 16:15:36 UTC  
> Url: https://github.com/boostorg/math/pull/770#discussion_r879646902  

On thing about the paper that might get us closer to the published value: The xs were spaced uniformly, not randomly.


---

## Comment 15

> Username: NAThompson  
> Created_at: 2022-05-24 04:18:47 UTC  
> Url: https://github.com/boostorg/math/pull/770#issuecomment-1135386871  

@mborland : Looks good to me; I sign off!  
  
@jzmaddock : Want to do a final sign off?

---

## Comment 16

> Username: mborland  
> Created_at: 2022-05-25 15:10:18 UTC  
> Url: https://github.com/boostorg/math/pull/770#issuecomment-1137413302  

This is good to go now. Autodiff has been consistently hanging in the drone run under USAN.

---
