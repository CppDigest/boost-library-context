# #1346 fix: add condition to handle_imperfect_touch [Merged]

> Username: barendgehrels  
> Created at: 2024-11-20 16:01:53 UTC  
> Updated at: 2024-12-14 11:12:39 UTC  
> Merged at: 2024-11-25 13:12:16 UTC  
> Closed at: 2024-11-25 13:12:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1346  

Fixes #1345  
**Edit** also fixes #1288 which is added because it was similar.  
  
**About #1345**  
Difference was wrong for both cases, caused by nearly opposite segments. The condition causing this had to be fine-tuned with an extra check.  
  
Fixed behaviour:  
*a* (`p`=big polygon containing all, `q`=small rectangle at top which subtly overlaps `p`)  
  
Difference a-b (in image, this should be a rectangle, correct)  
![image](https://github.com/user-attachments/assets/1204895f-d9b3-4991-9244-b70f5d337356)  
  
Difference b-a -> 0 (correct)  
  
*b* (`p` = u-shape, `q` = the lower bar of it, subtly overlapping `p`)  
  
Difference `a-b` (in image, this should be the two vertical bars of the u, correct)  
Difference `b-a` -> 0 (correct)  
  
![image](https://github.com/user-attachments/assets/1dde0503-4322-4289-98a0-3cda27a94496)  
  
  
**About #1288**  
It handled a situation as a "touch" but both the middle points were not collinear. In those cases it should not be handled as a touch and use the "original" code path.  
  
The "mystery" why it happened only for polygon and not for multi-polygon, and this affects also the validity check, is not yet solved - but the case itself is solved.  
The multi/poly combination is not yet unit tested, I have another test for that in preparation but it's better to not have large chuncks in the beta-time for the release.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-11-20 16:02:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1346#pullrequestreview-2449067871  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_helpers.hpp

```diff
  74 |     inline int qk_wrt_p2() const { return m_side_strategy.apply(get_pj(), get_pk(), get_qk()); }
  75 | 
  69 |-     // Necessary when rescaling turns off:
```

> Username: barendgehrels  
> Created_at: 2024-11-20 16:02:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/1346#discussion_r1850589962  

Moved above to order `i, j, k`


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2024-11-20 16:03:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1346#pullrequestreview-2449069390  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
 683 |                 if (side_qk_p1 == 0 && side_pk_q1 == 0
 684 |                     && has_pk && has_qk
 685 |+                     && opposite(side.pi_wrt_q1(), side.qk_wrt_p2())
```

> Username: barendgehrels  
> Created_at: 2024-11-20 16:03:12 UTC  
> Updated_at: 2024-11-24 09:46:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/1346#discussion_r1850590879  

This is the fix for #1345


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2024-11-20 16:22:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1346#pullrequestreview-2449122797  

📁 test/algorithms/overlay/overlay_cases.hpp

```diff
1235 |+ {
1236 |+     // Needs check for opposite in handle_imperfect_touch
1237 |+     R""""(
```

> Username: barendgehrels  
> Created_at: 2024-11-20 16:22:26 UTC  
> Updated_at: 2024-11-20 16:22:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1346#discussion_r1850622412  

@vissarion this is (AFAIK) the first case written in this new multi-line format.  
It doesn't have all those quotes and reflects the reported case.  
Like it?

> Username: vissarion  
> Created_at: 2024-11-25 12:41:32 UTC  
> Updated_at: 2024-11-25 12:41:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1346#discussion_r1856551337  

Looks good, thanks.


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2024-11-24 09:47:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1346#pullrequestreview-2456667464  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
 364 |+                 // If that is not the case (both left or both right), it should not be handled as a touch,
 365 |+                 // (though the intersection point might be close to the end),
 366 |+                 // because segments might cross each other or touch the other in the middle.
```

> Username: barendgehrels  
> Created_at: 2024-11-24 09:47:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/1346#discussion_r1855407945  

This is the fix for #1288


---

## Review 5 [Approved]

> Username: vissarion  
> Created_at: 2024-11-25 12:41:52 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1346#pullrequestreview-2458330868  

Thanks!

---
