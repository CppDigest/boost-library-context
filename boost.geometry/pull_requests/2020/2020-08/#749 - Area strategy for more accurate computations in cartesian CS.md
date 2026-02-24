# #749 Area strategy for more accurate computations in cartesian CS [Merged]

> Username: vissarion  
> Created at: 2020-08-31 16:07:17 UTC  
> Updated at: 2020-12-21 10:20:37 UTC  
> Merged at: 2020-12-21 10:20:32 UTC  
> Closed at: 2020-12-21 10:20:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/749  

This PR uses precise mathematical formulas to compute more accurate areas in cartesian CS. The inaccuracy arise from errors in floating point summation. See also https://svn.boost.org/trac10/ticket/11928.  
  
A test case is added for illustration. This approach can be used in other places in the library where inaccurate summation can occur. A similar approach is used in `GeographicLib` cf. https://geographiclib.sourceforge.io/html/Accumulator_8hpp_source.html

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2020-08-31 23:54:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/749#pullrequestreview-479091338  

📁 include/boost/geometry/extensions/triangulation/strategies/cartesian/in_circle_robust.hpp

```diff
  14 | 
  15 |- #include<boost/geometry/extensions/triangulation/strategies/cartesian/detail/precise_math.hpp>
  15 |+ #include<boost/geometry/util/precise_math.hpp>
```

> Username: awulkiew  
> Created_at: 2020-08-31 23:54:42 UTC  
> Updated_at: 2020-11-06 10:18:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/749#discussion_r480479294  

Or would `formulas` be a better place?

> Username: awulkiew  
> Created_at: 2020-08-31 23:59:50 UTC  
> Updated_at: 2020-11-06 10:18:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/749#discussion_r480480845  

Together with `util/series_expansion.hpp` (probably in a different PR).

> Username: vissarion  
> Created_at: 2020-09-01 13:18:55 UTC  
> Updated_at: 2020-11-06 10:18:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/749#discussion_r481130625  

I put it `util` since it contains basic operations (summation, multiplication etc) just like `math.hpp`, formulas are more "geometric" but I am open to move it in a new PR together with other functions if we are convinced by the concept of formulas vs. util.


---

## Comment 2

> Username: awulkiew  
> Created_at: 2020-08-31 23:57:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/749#issuecomment-684109017  

Looks good, thanks!  
  
What is the difference in performance of both strategies?  
I'm asking because if it's close to the original strategy we could consider keeping only one.

---

## Review 3 [Commented]

> Username: tinko92  
> Created_at: 2020-09-01 05:17:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/749#pullrequestreview-479406210  

📁 include/boost/geometry/strategy/cartesian/precise_area.hpp

```diff
  30 |+ \brief Cartesian area calculation
  31 |+ \ingroup strategies
  32 |+ \details Calculates cartesian area using the trapezoidal rule and precise summation
```

> Username: tinko92  
> Created_at: 2020-09-01 05:17:19 UTC  
> Updated_at: 2020-11-06 10:18:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/749#discussion_r480824967  

A minor suggestion: The "precise summation" here kind of implies it already because summation precision is no issue with integral types, but maybe it would be helpful for some users who are less familiar with this to explicitly state that this is only intended to be used for floating-point CalculationType. Because, if I understand area result_type correctly, it will only promote to double if the user did not specify something.  
  
Or maybe there could be a static_assert of std::is_floating_point<return_type> in the strategy (it could also be argued to have it in the precise_math header, but I think from the users perspective, getting that error for the strategy might be more helpful).

> Username: awulkiew  
> Created_at: 2020-09-01 10:50:56 UTC  
> Updated_at: 2020-11-06 10:18:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/749#discussion_r481049132  

Yes, if the user passes CalculationType we believe in his intentions and use this type, no matter what it is. So I have no opinion regarding this suggestion.  
  
Have in mind that a user can pass his own, precise numeric type. If this method is good for such types then the test should rather be `(! std::is_integral<return_type>::value)`.

> Username: vissarion  
> Created_at: 2020-09-01 13:15:42 UTC  
> Updated_at: 2020-11-06 10:18:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/749#discussion_r481128444  

I clarify it more in the `\details` for the documentation. Since this is supposed to be for advanced users I do not think we need an assert or return an error. Are you OK with this @tinko92 ?

> Username: tinko92  
> Created_at: 2020-09-02 06:20:09 UTC  
> Updated_at: 2020-11-06 10:18:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/749#discussion_r481775346  

Yes, I think this is OK.


---

## Comment 4

> Username: vissarion  
> Created_at: 2020-09-01 13:27:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/749#issuecomment-684852855  

> Looks good, thanks!  
>   
> What is the difference in performance of both strategies?  
> I'm asking because if it's close to the original strategy we could consider keeping only one.  
  
The original strategy is performing 3 operations to compute the determinant and 1 sum for each segment, while the new adds 6 more operations (instead of one to compute a more accurate sum). A similar implementation in geographiclib https://geographiclib.sourceforge.io/html/classGeographicLib_1_1Accumulator.html is reported to be more than 10 times slower. To conclude I think both methods are useful. We have to understand how often this accurate strategy will be needed and then maybe make it default.

---

## Comment 5

> Username: vissarion  
> Created_at: 2020-11-03 15:01:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/749#issuecomment-721182250  

@barendgehrels do you agree to merge this to develop to go to 1.75 ?

---

## Review 6 [Approved]

> Username: barendgehrels  
> Created_at: 2020-11-04 08:51:01 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/749#pullrequestreview-523146815  

📁 include/boost/geometry/strategy/cartesian/precise_area.hpp

```diff
  93 |+                  * (return_type(get<1>(p1)) - return_type(get<1>(p2)));
  94 |+ 
  95 |+         auto res = boost::geometry::detail::precise_math::two_sum(st.sum1, det);
```

> Username: barendgehrels  
> Created_at: 2020-11-04 08:46:57 UTC  
> Updated_at: 2020-11-06 10:18:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/749#discussion_r517180247  

Minor, these two parts could be const.


📁 test/algorithms/area/area.cpp

```diff
 203 |+ 
 204 |+     BOOST_CHECK_CLOSE(inaccurate, 0, 0.0001);
 205 |+     BOOST_CHECK_CLOSE(accurate, -0.315, 0.0001);
```

> Username: barendgehrels  
> Created_at: 2020-11-04 08:49:14 UTC  
> Updated_at: 2020-11-06 10:18:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/749#discussion_r517181462  

Quite a difference. Is the polygon indeed anti clockwise?

> Username: vissarion  
> Created_at: 2020-11-06 10:11:24 UTC  
> Updated_at: 2020-11-06 10:18:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/749#discussion_r518650474  

It depends on how you perform the computation. With double arithmetic the area algorithm computes 4 determinants with the following values  
```  
-0.030000000000000012768  
-0.5999999999999999778  
-1606938044258989383512154151116236941093472087848332596084736  
1606938044258989383512154151116236941093472087848332596084736  
```  
which then has to sum up and divide by 2. The implementation before this PR computes `0` while after this PR  `-0.315` which is clearly more accurate.   
  
Now computing the determinants accurately is a different story, not related to this PR. Note that doing this computation with boost multi-precision the sign of the area is positive so the polygon is CW oriented. I added another example where both precise area an multi-precision returns positive sign while the old area strategy returns `0` to emphasize the usefulness of performing a sum more accurately.

> Username: barendgehrels  
> Created_at: 2020-11-07 09:49:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/749#discussion_r519159275  

I see. Good, thanks.

---

📁 test/algorithms/area/area.cpp

```diff
 186 |+     }
 187 |+ };
 188 |+ 
```

> Username: barendgehrels  
> Created_at: 2020-11-04 08:50:53 UTC  
> Updated_at: 2020-11-06 10:18:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/749#discussion_r517182503  

Maybe you can add one `simplex` case, just the area of a triangle or square, such that we can also judge the basic functionality.  
But besides that I'm OK, thanks.

> Username: vissarion  
> Created_at: 2020-11-06 10:11:52 UTC  
> Updated_at: 2020-11-06 10:18:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/749#discussion_r518650691  

good idea, done


---

## Review 7 [Commented]

> Username: barendgehrels  
> Created_at: 2020-11-07 09:47:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/749#pullrequestreview-525641601  

📁 test/algorithms/area/area.cpp

```diff
 204 |+ 
 205 |+     BOOST_CHECK_CLOSE(bg::area(poly0), 0.5, 0.0001);
 206 |+     BOOST_CHECK_CLOSE(bg::area(poly0, precise_cartesian()), 0.5, 0.0001);
```

> Username: barendgehrels  
> Created_at: 2020-11-07 09:47:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/749#discussion_r519159122  

Perfect, thank you


---
