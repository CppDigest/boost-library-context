# #1008 fix compilation error (svg), warnings (unused), style (return), examp… [Merged]

> Username: barendgehrels  
> Created at: 2022-05-22 11:05:44 UTC  
> Updated at: 2022-06-01 09:49:50 UTC  
> Merged at: 2022-06-01 09:42:20 UTC  
> Closed at: 2022-06-01 09:42:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1008  

…les and cmake (C++14)

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2022-05-23 09:45:57 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1008#pullrequestreview-981465441  

Thanks! OK by me.

📁 example/c10_custom_cs_example.cpp

```diff
  53 | {
  70 |-     typedef boost::geometry::model::point
  54 |+     using mars_point = boost::geometry::model::point
```

> Username: vissarion  
> Created_at: 2022-05-23 09:44:17 UTC  
> Updated_at: 2022-05-23 09:45:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1008#discussion_r879242716  

Should we make this consistently in all examples of the library?

> Username: barendgehrels  
> Created_at: 2022-05-23 18:16:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1008#discussion_r879750917  

In the end, yes. I usually update them now if they are at or close to touched code.


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2022-05-24 10:56:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1008#pullrequestreview-983005979  

📁 example/05_b_overlay_linestring_polygon_example.cpp

```diff
  75 |-     bg::strategy::intersection::services::default_strategy<typename bg::cs_tag<point_2d>::type>::type intersection_strategy;
  76 |-     bg::get_turns<false, false, bg::detail::overlay::assign_null_policy>(ls, p, intersection_strategy, rescale_policy, turns, policy);
  75 |+     typename bg::strategies::relate::services::default_strategy<bg::model::linestring<point_2d>, bg::model::polygon<point_2d>>::type strategy;
```

> Username: awulkiew  
> Created_at: 2022-05-24 10:56:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1008#discussion_r880359801  

None of the types are dependant so `typename` shouldn't be used. AFAIR some compilers may report error here. Or am I missing something?

> Username: barendgehrels  
> Created_at: 2022-05-29 19:28:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1008#discussion_r884314516  

Indeed, will fix.

> Username: barendgehrels  
> Created_at: 2022-06-01 08:46:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1008#discussion_r886550619  

:heavy_check_mark:


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2022-05-24 10:57:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1008#pullrequestreview-983007190  

📁 example/c10_custom_cs_example.cpp

```diff
  72 |     std::cout << "Distance between Viking1 and Pathfinder landing sites: "
  87 |-         << d * 3389.5 << " km" << std::endl;
  73 |+         << d * 1.0  << " km" << std::endl;
```

> Username: awulkiew  
> Created_at: 2022-05-24 10:57:15 UTC  
> Updated_at: 2022-05-24 10:57:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1008#discussion_r880360789  

So this `1.0` is not needed anymore. :)

> Username: barendgehrels  
> Created_at: 2022-05-29 19:26:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1008#discussion_r884314236  

Ah, yes, thanks, will fix.

> Username: barendgehrels  
> Created_at: 2022-06-01 08:46:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/1008#discussion_r886550493  

:heavy_check_mark:


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2022-05-24 10:59:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1008#pullrequestreview-983009456  

📁 example/c10_custom_cs_example.cpp

```diff
  66 |+     // To calculate distance, declare and construct a strategy with Mars mean radius, in KM
  67 |     // (Source: http://nssdc.gsfc.nasa.gov/planetary/factsheet/marsfact.html)
  68 |+     boost::geometry::strategy::distance::haversine<double> const haversine(3389.5);
```

> Username: awulkiew  
> Created_at: 2022-05-24 10:59:07 UTC  
> Updated_at: 2022-05-24 10:59:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1008#discussion_r880362362  

We have umbrella strategies now. Shouldn't we use them in the example? In this case `strategies::distance::spherical<>` ?

> Username: barendgehrels  
> Created_at: 2022-05-29 19:27:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1008#discussion_r884314494  

OK I'll try.  
The "problem" was that these original examples didn't work anymore. So somewhere we were not backwards compatible, the strategy converters did not work for these cases.  
Anyway, I was a bit short on time and fixed it with the least amount of time at that moment. I'll look at it again, but anyway, you're welcome to update them again if you think it's better or better illustrates the possibilities.

> Username: awulkiew  
> Created_at: 2022-05-29 20:10:05 UTC  
> Updated_at: 2022-05-29 20:10:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1008#discussion_r884318808  

Yes, we should probably find out why this is not backward compatible.

> Username: barendgehrels  
> Created_at: 2022-06-01 08:46:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1008#discussion_r886550152  

> We have umbrella strategies now. Shouldn't we use them in the example? In this case `strategies::distance::spherical<>` ?  
  
That works


---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2022-05-24 10:59:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1008#pullrequestreview-983009784  

📁 example/c10_custom_cs_example.cpp

```diff
  80 |+     using spheroid_type = boost::geometry::srs::spheroid<double>;
  81 |+     spheroid_type spheroid(3396.2, 3376.2);
  82 |+     boost::geometry::strategy::distance::andoyer<spheroid_type> const andoyer(spheroid);
```

> Username: awulkiew  
> Created_at: 2022-05-24 10:59:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1008#discussion_r880362594  

And here `strategies::distance::geographic` ?

> Username: barendgehrels  
> Created_at: 2022-06-01 08:46:25 UTC  
> Updated_at: 2022-06-01 08:46:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/1008#discussion_r886550254  

:heavy_check_mark:


---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2022-05-28 22:50:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1008#pullrequestreview-988559872  

📁 test/to_svg.hpp

```diff
 192 |         <
 193 |-             G1, G2, point_type, turn_policy
 193 |+             G1, G2, turn_policy
```

> Username: awulkiew  
> Created_at: 2022-05-28 22:50:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1008#discussion_r884186385  

This is the fix for error similar to this one, correct?  
```  
In file included from libs/geometry/test/algorithms/set_operations/sym_difference/test_sym_difference_linear_linear.hpp:21:0,  
                 from libs/geometry/test/algorithms/set_operations/sym_difference/sym_difference_linear_linear.cpp:23:  
libs/geometry/test/to_svg.hpp: In function ‘void to_svg(const G1&, const G2&, const G3&, const string&)’:  
libs/geometry/test/to_svg.hpp:194:9: error: wrong number of template arguments (4, should be at least 2)  
         >::apply(turns, g1, g2, interrupt_policy, strategy);  
```  
  
Should `to_svg.hpp` be included if `TEST_WITH_SVG` is not defined?  
It is in the following files:  
https://github.com/boostorg/geometry/blob/develop/test/algorithms/set_operations/intersection/test_intersection_linear_linear.hpp#L22  
https://github.com/boostorg/geometry/blob/develop/test/algorithms/set_operations/difference/test_difference_linear_linear.hpp#L22  
https://github.com/boostorg/geometry/blob/develop/test/algorithms/set_operations/sym_difference/test_sym_difference_linear_linear.hpp#L21  
https://github.com/boostorg/geometry/blob/develop/test/algorithms/set_operations/union/test_union_linear_linear.hpp#L21

> Username: barendgehrels  
> Created_at: 2022-05-29 19:25:59 UTC  
> Updated_at: 2022-05-29 19:26:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1008#discussion_r884314217  

That makes sense, I will change it.

> Username: barendgehrels  
> Created_at: 2022-06-01 08:47:12 UTC  
> Updated_at: 2022-06-01 09:10:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1008#discussion_r886551057  

:heavy_check_mark: fixed and checked (with that define), there was actually one other error too which is now fixed as well


---
