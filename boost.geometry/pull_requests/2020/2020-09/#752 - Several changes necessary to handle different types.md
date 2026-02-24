# #752 [buffer] Several changes necessary to handle different types [Merged]

> Username: barendgehrels  
> Created at: 2020-09-09 12:15:53 UTC  
> Updated at: 2020-09-13 20:06:16 UTC  
> Merged at: 2020-09-13 20:06:10 UTC  
> Closed at: 2020-09-13 20:06:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/752  

This should (at least partly) fix the recent observation of @awulkiew , and contains parts of my work I did before to support types other than double (without rescaling)

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2020-09-09 12:20:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/752#pullrequestreview-484949710  

📁 include/boost/geometry/algorithms/detail/buffer/get_piece_turns.hpp

```diff
 187 |-         }
 188 |-     }
 189 |- 
```

> Username: barendgehrels  
> Created_at: 2020-09-09 12:20:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/752#discussion_r485566806  

@awulkiew : I don't remember why this was there, indeed. It is around 7 years old. But I disabled it, and it (now) runs all tests without problems, so I removed it. I have to come back to it, because a similar thing is also in the non-buffered turns.  
  
This most probably fixes the type conflict for you. I can compile with Boost MultiPrecision, and it runs but still gives many errors. So I will continue later with this. The Union (for example) runs fine with Boost MP, so indeed it has to be something with buffer.

> Username: vissarion  
> Created_at: 2020-09-10 15:42:13 UTC  
> Updated_at: 2020-09-10 15:42:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/752#discussion_r486445007  

I think in general it could be very useful if we add minimal tests for all algorithms with some other type than `double` or `float`. Boost MP is a good choice I think.

> Username: barendgehrels  
> Created_at: 2020-09-10 21:04:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/752#discussion_r486632357  

Yes, I think we can clean up old code (ttmath, GMP and some other types which were all not part of boost, but we used to verify that behaviour) and add MP there instead.  
For union it works quite well (I think perfect, but some expectations need a small update). For buffer there is still a problem.

> Username: awulkiew  
> Created_at: 2020-09-11 10:55:45 UTC  
> Updated_at: 2020-09-11 10:55:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/752#discussion_r486969020  

It would make the testing more complete for sure. Though I had a different scenario in mind when I wrote the comment for the commit. In my branch I use MP types instead of robust/rescaled/integral coordinates to avoid overflow. So the original coordinate type is e.g. `double`. Still in both cases less-comparison would fail to compile. The original MP coordinate type would also cause error in rescaling function because AFAIR there is no implicit conversion from MP to fundamental types.

> Username: awulkiew  
> Created_at: 2020-09-11 11:09:57 UTC  
> Updated_at: 2020-09-11 11:10:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/752#discussion_r486975223  

@barendgehrels It looks like some kind of optimization searching for subranges in both rings corresponding to bounding boxes of sections in order to ignore the rest of the rings. So my guess is that you could notice a difference in performance in some cases. Still this was probably a premature optimization so I'm ok with removing it since it makes the code less complex.

> Username: barendgehrels  
> Created_at: 2020-09-13 20:05:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/752#discussion_r487571496  

I'll test more thoroughly coming week.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2020-09-09 12:21:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/752#pullrequestreview-484950176  

📁 include/boost/geometry/algorithms/detail/buffer/line_line_intersection.hpp

```diff
  46 |- 
  47 |-         return ip;
  43 |+         return arithmetic::intersection_point(p, q, ip);
```

> Username: barendgehrels  
> Created_at: 2020-09-09 12:21:26 UTC  
> Updated_at: 2020-09-09 12:21:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/752#discussion_r485567151  

For some types, with extreme coordinates, they don't intersect.


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2020-09-09 12:21:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/752#pullrequestreview-484950505  

📁 include/boost/geometry/algorithms/detail/sections/section_functions.hpp

```diff
 138 |+                              typename geometry::coordinate_type<robust_point_type>::type,
 139 |+                              typename geometry::coordinate_type<RobustBox>::type
 140 |+                          >::value));
```

> Username: barendgehrels  
> Created_at: 2020-09-09 12:21:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/752#discussion_r485567425  

This should make applying wrong types impossible

> Username: awulkiew  
> Created_at: 2020-09-11 11:00:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/752#discussion_r486971246  

We can use `std::is_same` now. ;) But none of the included files probably include `<type_traits>`, so let's leave it as it is until my PR is merged and I'll change this afterwards.


---

## Review 4 [Approved]

> Username: awulkiew  
> Created_at: 2020-09-11 11:10:31 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/752#pullrequestreview-486714506  

Thanks for the fix!

---
