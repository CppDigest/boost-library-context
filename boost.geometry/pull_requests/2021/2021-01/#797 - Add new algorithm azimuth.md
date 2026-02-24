# #797 Add new algorithm azimuth. [Merged]

> Username: awulkiew  
> Created at: 2021-01-20 01:44:30 UTC  
> Updated at: 2021-02-16 14:38:45 UTC  
> Merged at: 2021-02-16 14:38:45 UTC  
> Closed at: 2021-02-16 14:38:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/797  

This PR addresses the issue: https://github.com/boostorg/geometry/issues/771  
  
Usage:  
```  
auto azimuth = boost::geometry::azimuth(p1, p2);  
```  
or  
```  
bg::srs::spheroid<double> spheroid(6378137.0, 6356752.3142451793);  
bg::strategies::azimuth::geographic<> strategy(spheroid);  
auto azimuth = boost::geometry::azimuth(p1, p2, strategy);  
```  
  
After:  
https://postgis.net/docs/ST_Azimuth.html  
  
Note:  
It also adds implementation of cartesian azimuth strategy and azimuth umbrella strategies.

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2021-02-03 11:22:25 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/797#pullrequestreview-582256454  

Thanks a lot @awulkiew , looks really good.  
I had only a few really minor remarks.

📁 doc/src/examples/algorithms/azimuth.cpp

```diff
  19 |+ int main()
  20 |+ {
  21 |+     typedef boost::geometry::model::d2::point_xy<double> point_type;
```

> Username: barendgehrels  
> Created_at: 2021-02-03 11:17:17 UTC  
> Updated_at: 2021-02-16 14:35:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/797#discussion_r569331620  

We could use `using` - but it's fine anyway. Minor.

> Username: awulkiew  
> Created_at: 2021-02-16 13:26:05 UTC  
> Updated_at: 2021-02-16 14:35:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/797#discussion_r576820296  

If `using` is prefered we should probably do this consistently in all examples at once.


📁 include/boost/geometry/algorithms/azimuth.hpp

```diff
  74 |+         calc_t y1 = geometry::get_as_radian<1>(p1);
  75 |+         calc_t x2 = geometry::get_as_radian<0>(p2);
  76 |+         calc_t y2 = geometry::get_as_radian<1>(p2);
```

> Username: barendgehrels  
> Created_at: 2021-02-03 11:17:59 UTC  
> Updated_at: 2021-02-16 14:35:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/797#discussion_r569332075  

Can you make them `const` ?

> Username: awulkiew  
> Created_at: 2021-02-16 13:24:40 UTC  
> Updated_at: 2021-02-16 14:35:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/797#discussion_r576819346  

Done

---

📁 include/boost/geometry/algorithms/azimuth.hpp

```diff
 179 |+ */
 180 |+ template <typename Point1, typename Point2>
 181 |+ inline auto azimuth(Point1 const& point1, Point2 const& point2)
```

> Username: barendgehrels  
> Created_at: 2021-02-03 11:19:35 UTC  
> Updated_at: 2021-02-16 14:35:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/797#discussion_r569332965  

Cool, this `auto` here!  
:smiley:

> Username: awulkiew  
> Created_at: 2021-02-16 13:26:31 UTC  
> Updated_at: 2021-02-16 14:35:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/797#discussion_r576820570  

Yes, the same could be done in `distance()` and `area()`!


📁 include/boost/geometry/strategies/spherical.hpp

```diff
  57 |     }
  58 | 
  59 |+     // azimuth
```

> Username: barendgehrels  
> Created_at: 2021-02-03 11:21:03 UTC  
> Updated_at: 2021-02-16 14:35:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/797#discussion_r569333779  

This comment is probably redundant

> Username: awulkiew  
> Created_at: 2021-02-16 14:37:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/797#discussion_r576870470  

Done.


📁 test/algorithms/azimuth.cpp

```diff
  57 |+     test_car< bg::model::point<double, 2, bg::cs::cartesian> >();
  58 |+     test_sph< bg::model::point<double, 2, bg::cs::spherical_equatorial<bg::degree> > >();
  59 |+     test_geo< bg::model::point<double, 2, bg::cs::geographic<bg::degree> > >();
```

> Username: barendgehrels  
> Created_at: 2021-02-03 11:21:50 UTC  
> Updated_at: 2021-02-16 14:35:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/797#discussion_r569334232  

I like it.

> Username: vissarion  
> Created_at: 2021-02-12 10:51:53 UTC  
> Updated_at: 2021-02-16 14:35:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/797#discussion_r575138808  

nice, it could contain a call with the non default formula for azimuth (i.e. andoyer).

> Username: awulkiew  
> Created_at: 2021-02-16 14:36:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/797#discussion_r576870247  

Done, I added cases with vincenty.


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2021-02-12 10:52:10 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/797#pullrequestreview-589345867  

Thanks! OK with merging.

📁 include/boost/geometry/algorithms/azimuth.hpp

```diff
  64 |+     static auto apply(Point1 const& p1, Point2 const& p2, Strategy const& strategy)
  65 |+     {
  66 |+         typedef typename decltype(strategy.azimuth())::template result_type
```

> Username: vissarion  
> Created_at: 2021-02-12 10:40:09 UTC  
> Updated_at: 2021-02-16 14:35:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/797#discussion_r575131975  

you can avoid it by using `auto` below, right?

> Username: awulkiew  
> Created_at: 2021-02-12 21:54:44 UTC  
> Updated_at: 2021-02-16 14:35:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/797#discussion_r575539462  

Not without changes in azimuth strategies. Azimuth strategy `CalculationType`, Point1's coordinate type and Point2's coordinate type can be different types and right now this strategy `apply()` requires all arguments of the same type.  
  
It would be doable if azimuth strategy `apply()` allowed to pass arguments of arbitrary type, was doing the conversion to the calculation type inside and returning the result from the function instead of using output argument for that. Then there would be a problem with the overloads of `apply()` allowing to calculate both forward and reverse azimuths at the same time. These functions would have to have different name than `apply()` which would be ok I guess since there already is `apply_reverse()`.  
  
So yes, it could be avoided but would require more changes. I think this is a good idea in general to remove all of these utilities like `result_type`, `state_type`, etc. and replace them with a mechanism using `auto`. But this would mean changes in the interfaces of strategies. If we agreed to do this I'd rather do this in a different PR.

> Username: vissarion  
> Created_at: 2021-02-15 10:18:27 UTC  
> Updated_at: 2021-02-16 14:35:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/797#discussion_r576082908  

sure, better doing this in separate PR


---

## Comment 3

> Username: awulkiew  
> Created_at: 2021-02-16 14:38:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/797#issuecomment-779877839  

Thanks for the reviews!  
I addressed all of the raised issues and am going to merge now.

---
