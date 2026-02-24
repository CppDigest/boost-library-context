# #487 Additional t-tests [Merged]

> Username: mborland  
> Created at: 2021-01-10 17:45:56 UTC  
> Updated at: 2021-01-13 05:35:36 UTC  
> Merged at: 2021-01-12 20:48:54 UTC  
> Closed at: 2021-01-12 20:48:54 UTC  
> Url: https://github.com/boostorg/math/pull/487  

Adds paired samples t-test, independent two sample t-test, and Welch's t-test. The latter two share an interface but are internally routed to an implementation based off the difference between the two set's variances.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-01-10 18:04:06 UTC  
> Url: https://github.com/boostorg/math/pull/487#issuecomment-757517681  

Very cool addition. Is it ready to be reviewed?

---

## Comment 2

> Username: mborland  
> Created_at: 2021-01-10 18:09:13 UTC  
> Url: https://github.com/boostorg/math/pull/487#issuecomment-757518470  

@NAThompson It is ready for review. The diff stat is deceptively large due to several svg files added into the docs.

---

## Review 3 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-10 21:00:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/487#pullrequestreview-564900197  

📁 test/test_t_test.cpp

```diff
 110 |+     CHECK_MOLLIFIED_CLOSE(expected_statistic, computed_statistic, 0.00001);
 111 |+     CHECK_MOLLIFIED_CLOSE(expected_pvalue, computed_pvalue, 0.00001);
 112 |+ }
```

> Username: NAThompson  
> Created_at: 2021-01-10 21:00:32 UTC  
> Updated_at: 2021-01-12 18:55:10 UTC  
> Url: https://github.com/boostorg/math/pull/487#discussion_r554622157  

This is a good test, but is there more symmetric data for which you can compute an exact solution by hand? I tend to find those more compelling.  
  
Also, the precision of the calculation should increase with the precision of the `Real` type, but yours does not. I would use `CHECK_ULP_CLOSE`.


---

## Review 4 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-10 21:01:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/487#pullrequestreview-564900257  

📁 test/test_t_test.cpp

```diff
 154 |     test_integer<uint32_t>();
 155 |+     test_two_sample_t<float>();
 156 |+     test_two_sample_t<double>();
```

> Username: NAThompson  
> Created_at: 2021-01-10 21:01:23 UTC  
> Updated_at: 2021-01-12 18:55:10 UTC  
> Url: https://github.com/boostorg/math/pull/487#discussion_r554622221  

Could you also test compatibility with `boost::multiprecision`? Quad should be fine.


---

## Comment 5

> Username: NAThompson  
> Created_at: 2021-01-10 21:07:39 UTC  
> Updated_at: 2021-01-10 21:10:40 UTC  
> Url: https://github.com/boostorg/math/pull/487#issuecomment-757544212  

I'm getting a new warning when building the docs:  
  
```  
xslt-xsltproc-dir html/standalone_HTML.manifest  
Unrecognized unit of measure: ex.  
Unrecognized unit of measure: .  
Unrecognized unit of measure: ex.  
Unrecognized unit of measure: .  
Unrecognized unit of measure: ex.  
Unrecognized unit of measure: .  
Unrecognized unit of measure: ex.  
Unrecognized unit of measure: .  
Unrecognized unit of measure: ex.  
Unrecognized unit of measure: .  
Unrecognized unit of measure: ex.  
Unrecognized unit of measure: .  
Unrecognized unit of measure: ex.  
Unrecognized unit of measure: .  
Unrecognized unit of measure: ex.  
Unrecognized unit of measure: .  
Unrecognized unit of measure: ex.  
Unrecognized unit of measure: .  
Unrecognized unit of measure: ex.  
Unrecognized unit of measure: .  
Unrecognized unit of measure: ex.  
Unrecognized unit of measure: .  
Unrecognized unit of measure: ex.  
Unrecognized unit of measure: .  
...updated 33 targets...  
```  
  
Is this new? I have no clue what's causing it.  
  
Other than that, the docs look good.

---

## Comment 6

> Username: mborland  
> Created_at: 2021-01-11 16:34:03 UTC  
> Url: https://github.com/boostorg/math/pull/487#issuecomment-758071379  

@NAThompson I am not sure where that is coming from. The only warning I see is:  
  
```overview/structure.qbk:62: warning: line breaks generate invalid boostbook (will only note first occurrence).```

---

## Comment 7

> Username: NAThompson  
> Created_at: 2021-01-11 17:09:59 UTC  
> Url: https://github.com/boostorg/math/pull/487#issuecomment-758093191  

@mborland : That one has been around forever. I wonder if it's from a new .svg version . . .

---

## Comment 8

> Username: mborland  
> Created_at: 2021-01-11 17:41:20 UTC  
> Url: https://github.com/boostorg/math/pull/487#issuecomment-758111106  

@NAThompson Github does have the `ex` from vertical-align in the .svg files highlighted in red. I am not sure how I would go about fixing that. Reformat them into jpegs and reupload?

---

## Comment 9

> Username: NAThompson  
> Created_at: 2021-01-11 18:22:16 UTC  
> Url: https://github.com/boostorg/math/pull/487#issuecomment-758135238  

> I am not sure how I would go about fixing that. Reformat them into jpegs and reupload?  
  
Nah; just remove them from the svg directly.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2021-01-11 18:38:03 UTC  
> Url: https://github.com/boostorg/math/pull/487#issuecomment-758143591  

Which svg's?

---

## Comment 11

> Username: mborland  
> Created_at: 2021-01-11 18:50:59 UTC  
> Url: https://github.com/boostorg/math/pull/487#issuecomment-758150575  

@jzmaddock All 6x svg's included on this PR show the vertical align unit `ex` in red on line 1.

---

## Comment 12

> Username: mborland  
> Created_at: 2021-01-11 19:15:38 UTC  
> Url: https://github.com/boostorg/math/pull/487#issuecomment-758164561  

@NAThompson I edited the svgs in commit 95f2f4b. Let me know if that fixed the warnings building your docs.

---

## Review 13 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-11 19:47:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/487#pullrequestreview-565684233  

📁 doc/graphs/paired_sample_t_statistic.svg

```diff
   1 |+ <svg xmlns:xlink="http://www.w3.org/1999/xlink" width="13.643ex" height="6.676ex" style="vertical-align: -2.838;" viewBox="0 -1652.5 5874.2 2874.4" role="img" focusable="false" xmlns="http://www.w3.org/2000/svg" aria-labelledby="MathJax-SVG-1-Title">
```

> Username: NAThompson  
> Created_at: 2021-01-11 19:47:40 UTC  
> Updated_at: 2021-01-12 18:55:10 UTC  
> Url: https://github.com/boostorg/math/pull/487#discussion_r555296813  

Looks like we still have the "ex" unit here.

> Username: mborland  
> Created_at: 2021-01-11 19:54:46 UTC  
> Updated_at: 2021-01-12 18:55:10 UTC  
> Url: https://github.com/boostorg/math/pull/487#discussion_r555300528  

I thought the problem may have just been vertical align. All instance of ex have been removed and replaced with pixel counts in 0ac42ae.

> Username: pabristow  
> Created_at: 2021-01-12 10:25:20 UTC  
> Updated_at: 2021-01-12 18:55:10 UTC  
> Url: https://github.com/boostorg/math/pull/487#discussion_r555664827  

https://drafts.csswg.org/css-values-4/#font-relative-length   says  
  
ex unit  
Equal to the used x-height of the first available font [CSS3-FONTS]. The x-height is so called because it is often equal to the height of the lowercase "x". However, an ex is defined even for fonts that do not contain an "x". The x-height of a font can be found in different ways. Some fonts contain reliable metrics for the x-height. If reliable font metrics are not available, UAs may determine the x-height from the height of a lowercase glyph. One possible heuristic is to look at how far the glyph for the lowercase "o" extends below the baseline, and subtract that value from the top of its bounding box. In the cases where it is impossible or impractical to determine the x-height, a value of 0.5em ...  
  
So it is like em units and should be a legal SVG unit.  I don't see why it should should cause trouble in generating the docs. Using mathJax has pulled in this new definition width="13.643ex".  
  
But the xsltproc version we are using is old and probably predates this newer measure.  When we tried to use a newer version, the build process went wrong, so we have stuck to the old one.  
  
But having said that, the default we have used elsewhere is pixels and that is the svg default, so it should work by just deleting the ex.  But it may cause some change in font size.  em might be closer?  Worth eyeballing the entire resulting docs - look at formulas?

> Username: mborland  
> Created_at: 2021-01-12 16:00:00 UTC  
> Updated_at: 2021-01-12 18:55:10 UTC  
> Url: https://github.com/boostorg/math/pull/487#discussion_r555884766  

The font size changes if you just delete the ex off the end. I pulled up the SVG files with the original dimensions in a photo viewer to get the dimensions in pixels and then plugged them in. No issues with rendering that I see.


---

## Comment 14

> Username: NAThompson  
> Created_at: 2021-01-11 20:59:07 UTC  
> Url: https://github.com/boostorg/math/pull/487#issuecomment-758219455  

Just validated your fix removes the warning.  
  
Do you have deterministic values that lead to a value you can compute by hand?

---

## Review 15 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-11 21:05:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/487#pullrequestreview-565738047  

📁 include/boost/math/statistics/t_test.hpp

```diff
 114 |+     using std::sqrt;
 115 |+     auto n1 = std::distance(begin_1, end_1);
 116 |+     auto n2 = std::distance(begin_2, end_2);
```

> Username: NAThompson  
> Created_at: 2021-01-11 21:05:33 UTC  
> Updated_at: 2021-01-12 18:55:10 UTC  
> Url: https://github.com/boostorg/math/pull/487#discussion_r555339335  

Note that if you call `std::distance`, then your expense is O(n) in the `std::list` case. Generally you want to restrict to RandomAccessIterator if you want to call `std::distance` so that it's O(1).

> Username: mborland  
> Created_at: 2021-01-12 16:16:46 UTC  
> Updated_at: 2021-01-12 18:55:10 UTC  
> Url: https://github.com/boostorg/math/pull/487#discussion_r555896863  

In this case I don't see anyway around using `std::distance`. Since the requirement for `mean_and_sample_variance` is also ForwardIterator requiring RandomAccessIterator seems overly restrictive. I can make a note in the docs saying `std::distance` is used.


---

## Review 16 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-11 21:06:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/487#pullrequestreview-565738503  

📁 include/boost/math/statistics/t_test.hpp

```diff
 156 |+     ForwardIterator it_2 = begin_2;
 157 |+     std::size_t pos = 0;
 158 |+     while(it_1 != end_1)
```

> Username: NAThompson  
> Created_at: 2021-01-11 21:06:15 UTC  
> Updated_at: 2021-01-12 18:55:10 UTC  
> Url: https://github.com/boostorg/math/pull/487#discussion_r555339741  

This loop goes through every element right here; so you can elide the `std::distance`.


---

## Comment 17

> Username: mborland  
> Created_at: 2021-01-12 16:22:11 UTC  
> Url: https://github.com/boostorg/math/pull/487#issuecomment-758770442  

@NAThompson tests with deterministic values and quadruple precision types were added in commit 95f2f4b and came back clean from CI.

---

## Comment 18

> Username: NAThompson  
> Created_at: 2021-01-12 16:44:07 UTC  
> Url: https://github.com/boostorg/math/pull/487#issuecomment-758784776  

@mborland : I think we're talking past each other a bit. That commit used a set of random values taken from a given distribution. I was hoping to see a set of very symmetric values for which we can compute an exact rational number for the p-value and test statistic.

---

## Comment 19

> Username: mborland  
> Created_at: 2021-01-12 17:04:00 UTC  
> Url: https://github.com/boostorg/math/pull/487#issuecomment-758801067  

@NAThompson The functions `test_exact_mean` and `test_multiprecision_exact_mean` are the only two that use a distribution and are used for one sample t-tests. The former was already in here, and the latter is nearly identical except it can work with quad-precision types. The only reason I added this in was quad-precision tests are available for two sample t-tests.   
  
The two sample t-tests do not have any random values but instead use the provided vectors. The first tests using `CHECK_MOLLIFIED_CLOSE` are checked against the result of the link in the comments. The second tests use `CHECK_ULP_CLOSE`. The difference is that here I pass in the same set for both `u` and `v`. The result for the computed statistic will be 0 and the p-value will be 1 in any case where both sets are identical.   
  
Does that clear up where I am coming from, or is there something else I am still missing?

---

## Comment 20

> Username: NAThompson  
> Created_at: 2021-01-12 17:27:55 UTC  
> Updated_at: 2021-01-12 17:28:40 UTC  
> Url: https://github.com/boostorg/math/pull/487#issuecomment-758815967  

> The two sample t-tests do not have any random values but instead use the provided vectors.  
  
Right, but the provided vectors do not have any special properties which make the test statistic and p-value amenable to hand calculation.  
  
So for instance this:  
  
```cpp  
std::vector<Z> set_1 {14, 15, 15, 15, 16, 18, 22, 23, 24, 25, 25};  
std::vector<Z> set_2 {10, 12, 14, 15, 18, 22, 24, 27, 31, 33, 34, 34, 34};  
  
// https://www.statology.org/welchs-t-test/  
const double expected_statistic = -1.5379;  
const double expected_pvalue = 0.1413;  
```  
  
So what we have now is agreement with statology.org, but the expected value is not even specified to float precision. So we have two sources of danger: One is that our code and statology are wrong in the same way. The next is that we have some constant parsed at the wrong precision in the code and hence no digits are correct to beyond that precision.  
  
If instead we used (say) constant data in both vectors, then we could find an explicit expression as a rational number for the test statistic and the p-value.

---

## Comment 21

> Username: NAThompson  
> Created_at: 2021-01-12 18:59:45 UTC  
> Url: https://github.com/boostorg/math/pull/487#issuecomment-758868527  

@mborland : I'm happy with this now; will merge once it goes green.

---

## Comment 22

> Username: NAThompson  
> Created_at: 2021-01-12 20:18:26 UTC  
> Url: https://github.com/boostorg/math/pull/487#issuecomment-758916312  

The errors are in automatic differentiation (from what I gather are some changes to floating point rounding modes on the CI system) and an internal compiler error:  
  
```  
makima_test.cpp:168:26:   required from here  
../../../boost/math/interpolators/makima.hpp:26:80: internal compiler error: in dependent_type_p, at cp/pt.c:23654  
            Real left_endpoint_derivative = std::numeric_limits<Real>::quiet_NaN(),  
                                            ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~  
Please submit a full bug report,  
with preprocessed source if appropriate.  
See <file:///usr/share/doc/gcc-7/README.Bugs> for instructions.  
```  
  
which is baffling, since this has been compiling successfully under gcc-7 for some time now.

---

## Comment 23

> Username: NAThompson  
> Created_at: 2021-01-12 20:49:12 UTC  
> Url: https://github.com/boostorg/math/pull/487#issuecomment-758965490  

Looks like the CI system needs a little TLC but this PR doesn't add to that task.

---
