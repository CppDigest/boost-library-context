# #1120 Fix buffer tests by using more accurate area computation [Merged]

> Username: vissarion  
> Created at: 2023-03-14 15:22:56 UTC  
> Updated at: 2023-03-31 07:51:28 UTC  
> Merged at: 2023-03-31 07:51:24 UTC  
> Closed at: 2023-03-31 07:51:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/1120  

This PR changes the way area is computed in the unit tests of buffered geographic geometries. The problem is that for small geometries all strategies (andoyer, thomas, vincenty) but the series expansion one (karney) return inaccurate and/or unstable results. That is with a small change in the input or the number type the changes in the output are large.  
  
For example consider [this](https://github.com/boostorg/geometry/commit/6a7224e369b958765daa719435371978863efe15#diff-4a770cf757384c3154247660df0dde42a4bc692d1b1e470e5c2b81a50c0640b9R71) unit test; We can get the following values for different strategies and number types  
  
|   | andoyer  | thomas  | vincenty  |   
|---|---|---|---|  
| float  |  31638 | 31633  | 31406  |     
| double  | 31450  | 31414  | 31343  |     
|  long double |  31469 | 31414  | 31403  |     
  
For floats the results of geographic buffer are not correct (e.g. creation of not valid geometries and assertions of zero denominators).   
  
*Note:* Buffer tests are now passing on [circleCI](https://app.circleci.com/pipelines/github/vissarion/geometry/258/workflows/74f7489b-2b6e-4eb2-8904-131f9335bd42/jobs/1817)

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2023-03-14 16:57:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1120#pullrequestreview-1339802796  

📁 test/algorithms/buffer/test_buffer.hpp

```diff
 360 |     {
 360 |-         auto const area = bg::area(buffered, strategy);
 361 |+         bg::strategies::buffer::geographic<bg::strategy::karney, bg::srs::spheroid<double>> kstrategy;
```

> Username: awulkiew  
> Created_at: 2023-03-14 16:57:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1120#discussion_r1135905134  

Is `test_buffer` function only used for geographic CS?  
  
Since different spheroids are used above, shouldn't they be passed here, i.e. extracted from `strategy`? This probably is not a problem since the issue is not correctness but consitency across compilers but still.

> Username: vissarion  
> Created_at: 2023-03-14 19:10:49 UTC  
> Updated_at: 2023-03-14 19:10:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1120#discussion_r1136066417  

oups, sure, this is just for testing... not ready for review (I couldn't trigger circleci non minimal testing without a PR)


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2023-03-15 10:10:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1120#pullrequestreview-1341069333  

📁 test/algorithms/buffer/buffer_geo_spheroid.cpp

```diff
  71 |-     test_point<bg::strategy::andoyer, true, point_t>("point_def", def_spheroid, 31450.0);
  67 |+     test_linestring<bg::strategy::andoyer, true, point_t>("line_def", def_spheroid, 8046.0, 8143.0);
  68 |+     test_point<bg::strategy::andoyer, true, point_t>("point_def", def_spheroid, 31414.0);
```

> Username: barendgehrels  
> Created_at: 2023-03-15 10:10:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/1120#discussion_r1136817261  

I don't see what really changed, apart from the expectations.  
  
But you mentioned it's a personal test, so that's fine.


---

## Comment 3

> Username: vissarion  
> Created_at: 2023-03-17 14:11:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/1120#issuecomment-1473905495  

@barendgehrels @awulkiew now it is ready for review

---

## Comment 4

> Username: vissarion  
> Created_at: 2023-03-17 14:20:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1120#issuecomment-1473918815  

If you agree with the changes of this PR I can also apply the change (always compute area with karney strategy) to other geographic buffer unit tests as well.

---

## Review 5 [Approved]

> Username: barendgehrels  
> Created_at: 2023-03-22 10:01:09 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1120#pullrequestreview-1352154846  

📁 test/algorithms/buffer/buffer_geo_spheroid.cpp

```diff
  49 |+             >(base_t::m_spheroid);
  50 |+     }
  51 |+ };
```

> Username: barendgehrels  
> Created_at: 2023-03-22 10:00:57 UTC  
> Updated_at: 2023-03-22 10:01:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/1120#discussion_r1144523230  

Thanks. So it uses karney, whatever the other formula is. Fine for me.  
Maybe this option should be documented, it's good to know for users.  
But that can be done in another PR.

> Username: vissarion  
> Created_at: 2023-03-22 10:20:58 UTC  
> Updated_at: 2023-03-22 10:20:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1120#discussion_r1144557672  

Yes, it needs some further investigation. I will work on it. We know that those formulas (andoyer, thomas, vincenty) are unstable in same corner cases (antipodal points, points very close to each other, meridian/equatorial cases). Those results can be OK/expected for distance or azimuth but if they are used inside other algorithms like area or buffer the results can be unexpected. In general the Geographic part of the library is not documented.


---
