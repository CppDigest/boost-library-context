# #1364 Fix several strategies (add getters, fix compilation error and warning) [Merged]

> Username: awulkiew  
> Created at: 2025-01-10 23:05:40 UTC  
> Updated at: 2025-03-18 13:19:31 UTC  
> Merged at: 2025-01-16 14:05:01 UTC  
> Closed at: 2025-01-16 14:05:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/1364  

The getters are missing for `centroid` and `closest_points` in `index` and global umbrella strategies preventing them to be passed to these algorithms and rtree.  
  
I divided centroid into detail part and official part derived from base. It works for now because centroid doesn't have proper strategies for spherical and geographic. After they are added and e.g. geographic one uses `Formula` and `Spheroid` we'll probably have to implement this differently.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-11 12:40:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1364#pullrequestreview-2544726244  

📁 include/boost/geometry/strategies/spherical/distance_cross_track_box_box.hpp

```diff
  52 |+                                            Point2 topB,
  53 |+                                            Point1 bottomA,
  54 |+                                            Point2 bottomB,
```

> Username: barendgehrels  
> Created_at: 2025-01-11 12:40:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1364#discussion_r1912012755  

Thanks, good to see a PR from your side!  
  
So the point types can be different, I noticed. That's fine.  
But they can be made `Point1 const&` (it was already before this PR - but now it's touched...), and the strategies too.  
Also unusual is that `ReturnType` is specified before `static inline` but that was also already the case.  
  
Anyway, I'me fine with the changes.

> Username: awulkiew  
> Created_at: 2025-01-11 15:53:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/1364#discussion_r1912071117  

Done.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-11 12:41:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1364#pullrequestreview-2544728835  

📁 include/boost/geometry/strategies/geographic/distance_cross_track.hpp

```diff
 705 | 
 703 |-         geometry::strategy::distance::haversine<CT> str(earth_radius);
 706 |+         haversine_t str(earth_radius);
```

> Username: barendgehrels  
> Created_at: 2025-01-11 12:41:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1364#discussion_r1912013731  

They probably can be made `const`. But also that was already before.

> Username: awulkiew  
> Created_at: 2025-01-11 15:53:26 UTC  
> Updated_at: 2025-01-11 15:53:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1364#discussion_r1912071108  

Right, I automatically changed the types. I added const now, also in other parts of the file.


---

## Comment 3

> Username: awulkiew  
> Created_at: 2025-01-11 22:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/1364#issuecomment-2585466879  

I added one more fix in geo distance_cross_track_box_box strategy.

---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-12 08:21:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1364#pullrequestreview-2545373805  

📁 include/boost/geometry/strategies/spherical/distance_cross_track_box_box.hpp

```diff
  52 |+                                            Point2 const& topB,
  53 |+                                            Point1 const& bottomA,
  54 |+                                            Point2 const& bottomB,
```

> Username: barendgehrels  
> Created_at: 2025-01-12 08:21:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1364#discussion_r1912393310  

Thanks @awulkiew !


---

## Review 5 [Approved]

> Username: vissarion  
> Created_at: 2025-01-15 14:05:05 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1364#pullrequestreview-2552846170  

Thanks!

---

## Comment 6

> Username: vissarion  
> Created_at: 2025-01-15 14:12:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1364#issuecomment-2592963044  

I do not know if it makes sense to switch to commit messages as proposed in https://github.com/boostorg/geometry/discussions/1324, now we have a mix of commit messages.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2025-01-15 22:21:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1364#issuecomment-2594056997  

I missed that and used the old pattern. I altered the commit messages.  
  
It would probably be useful to have it written down in the gudelines. I can see there are some differences between your messages and the article as well, e.g. doc vs docs, ext vs extension, starting from small vs big letter, etc. Also kinds/categories of changes are mixed with library parts like fix/feat vs extension. Fix/feat should probably be used for extensions as well.

---
