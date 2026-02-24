# #507 - Divide by zero in sjoberg_intersection.hpp [Closed]

> Username: springmeyer  
> Created at: 2018-08-12 18:59:56 UTC  
> Updated at: 2018-08-28 21:57:21 UTC  
> Closed at: 2018-08-27 23:44:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/507  

Running the tests with after compiling with `-fsanitize=undefined` reveals a divide by zero bug:  
  
```  
../../boost/geometry/formulas/sjoberg_intersection.hpp:1200:41: runtime error: division by zero  
    #0 0x10f9fb342 in boost::geometry::formula::sjoberg_intersection<double, boost::geometry::strategy::vincenty::inverse, 4u>::calc_ts(double const&, double const&, boost::geometry::formula::sjoberg_geodesic<double, 4u> const&, boost::geometry::formula::sjoberg_geodesic<double, 4u> const&, double&, double&) (/Users/danespringmeyer/projects/boost/libs/geometry/../../bin.v2/libs/geometry/test/strategies/strategies_segment_intersection_geo.test/clang-darwin-5.0/debug/strategies_segment_intersection_geo:x86_64+0x1008b1342)  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../boost/geometry/formulas/sjoberg_intersection.hpp:1200:41 in   
```  
  
Note:  
  
 - `boost::geometry` is compiled like: https://gist.github.com/springmeyer/0039d329df5cb8e040c0ee0284267457  
 - I presume that this divide by zero could result in `SIGFPE` if the coordinate type were floating point

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-08-27 15:06:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/507#issuecomment-416258313  

Thanks. Is my understanding correct that it's not that the division occured during the tests but the UBSan detects a potential for it? AFAIR at this point it is guaranteed that the division by 0 will not occur due to the previous checks. I have to check this again though to be sure.

---

## Comment 2

> Username: springmeyer  
> Created at: 2018-08-27 16:01:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/507#issuecomment-416276187  

> Is my understanding correct that it's not that the division occured during the tests but the UBSan detects a potential for it?  
  
No, ubsan detects only actual occurrences. It does not, to my knowledge, have the ability to statically analyze. It is fully dependent on actual test data and actual code paths executed to detect problems.

---

## Comment 3

> Username: awulkiew  
> Created at: 2018-08-27 23:44:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/507#issuecomment-416404019  

Fixed. Thanks.

---

## Comment 4

> Username: springmeyer  
> Created at: 2018-08-28 20:49:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/507#issuecomment-416735639  

Thanks @awulkiew. I was curious what the actual fix was, so I looked back in the recent commits and found https://github.com/boostorg/geometry/commit/ece82259a166c3d9a501df4e5e6010fa7bfe32a5. That is it, correct?

---

## Comment 5

> Username: awulkiew  
> Created at: 2018-08-28 21:57:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/507#issuecomment-416754299  

Yes. There is a related issue, i.e. the algorithm shouldn't even go into this function because correct result should be calculated using newton method before. But this is another fix for another time.
