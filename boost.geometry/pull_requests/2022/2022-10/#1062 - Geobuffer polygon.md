# #1062 Geobuffer polygon [Merged]

> Username: barendgehrels  
> Created at: 2022-10-05 15:15:04 UTC  
> Updated at: 2022-11-17 15:09:42 UTC  
> Merged at: 2022-10-13 07:40:11 UTC  
> Closed at: 2022-10-13 07:40:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/1062  

~~Build on top of geobuffer_linestring.~~ (now build on develop)  
  
This is testing only, the strategies already supported polygons.  
  
Result of the multipolygon test:  
  
![image](https://user-images.githubusercontent.com/334849/194096924-a903fba0-626c-4df1-9284-5bf309f856b5.png)

---

## Comment 1

> Username: vissarion  
> Created_at: 2022-10-12 09:29:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1062#issuecomment-1275866794  

Since this is on top of https://github.com/boostorg/geometry/pull/1058 it would be simpler to review it after https://github.com/boostorg/geometry/pull/1058 is merged.   
  
That is a really cool feature by the way!

---

## Review 2 [Commented]

> Username: vissarion  
> Created_at: 2022-10-12 09:32:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1062#pullrequestreview-1138761947  

Tests and data look good to me!

📁 test/algorithms/buffer/Jamfile

```diff
  38 |- #    [ run buffer_countries.cpp        : : : <define>BOOST_GEOMETRY_TEST_ONLY_ONE_TYPE : algorithms_buffer_countries ]
  39 |- #    [ run buffer_countries.cpp        : : : <define>BOOST_GEOMETRY_TEST_ONLY_ONE_TYPE <define>BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE : algorithms_buffer_countries_alternative ]
  42 |+     [ run buffer_countries.cpp            : : : <define>BOOST_GEOMETRY_TEST_ONLY_ONE_TYPE : algorithms_buffer_countries ]
```

> Username: vissarion  
> Created_at: 2022-10-12 09:31:41 UTC  
> Updated_at: 2022-10-12 09:32:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/1062#discussion_r993227163  

I didn't test it, is it OK wrt to time? Since this is now added to the test suite.

> Username: barendgehrels  
> Created_at: 2022-10-12 10:00:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1062#discussion_r993256576  

> I didn't test it, is it OK wrt to time? Since this is now added to the test suite.  
  
Oh thanks! No, this was not intentional, I'll revert this line.

> Username: barendgehrels  
> Created_at: 2022-10-12 10:06:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1062#discussion_r993261798  

:heavy_check_mark:


---

## Comment 3

> Username: barendgehrels  
> Created_at: 2022-10-12 10:07:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/1062#issuecomment-1275918325  

> Since this is on top of #1058 it would be simpler to review it after #1058 is merged.  
>   
> That is a really cool feature by the way!  
  
:+1: thanks. #1058 is now merged.

---
