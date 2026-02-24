# #1442 Clarify polygon orientation requirement for buffer algorithm [Closed]

> Username: kashish2710  
> Created at: 2025-12-13 15:09:38 UTC  
> Updated at: 2025-12-14 12:33:10 UTC  
> Closed at: 2025-12-14 12:33:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1442  

This PR adds a note in the buffer documentation (under [heading Strategies])  
to clarify that the buffer algorithm expects polygons to be counter-clockwise.  
If a polygon is clockwise, it should be reversed using:  
  
    boost::geometry::reverse(poly);  
  
A link to the Polygon Concept is also provided for more details.  
  
This helps users avoid confusion when using buffer with custom polygons.  
Related issue: #1375

---

## Comment 1

> Username: kashish2710  
> Created_at: 2025-12-13 15:10:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1442#issuecomment-3649525562  

Hi @ Boost Geometry team,  
  
This PR adds a note under the [heading Strategies] section of the buffer documentation  
to clarify that the buffer algorithm expects polygons to be counter-clockwise.  
If a polygon is clockwise, it should be reversed using:  
  
    boost::geometry::reverse(poly);  
  
A link to the Polygon Concept is also provided for more details.  
  
This addresses issue #1375 and should help new users avoid confusion  
when using the buffer function with custom polygons.  
  
Thank you for reviewing!

---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2025-12-14 10:09:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1442#pullrequestreview-3574947736  

📁 doc/reference/algorithms/buffer_with_strategies.qbk

```diff
  20 | * the sides can be controlled (currently there is only one option provided)
  21 |+ [NOTE]
  22 |+ The buffer algorithm expects polygons to be counter-clockwise.
```

> Username: barendgehrels  
> Created_at: 2025-12-14 10:09:47 UTC  
> Updated_at: 2025-12-14 10:09:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1442#discussion_r2616893887  

This is not true. If the polygon definition is clockwise, it expects it clockwise. If it is counter-clockwise, then it expects it counter-clockwise. Like the rest in Boost.Geometry  
  
See for example here:  
https://github.com/boostorg/geometry/blob/develop/test/algorithms/buffer/buffer_polygon.cpp#L269  
  
Same for closed.


---

## Review 3 [Changes requested]

> Username: barendgehrels  
> Created_at: 2025-12-14 10:10:10 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/1442#pullrequestreview-3574947897  

As mentioned in the comment

---

## Comment 4

> Username: kashish2710  
> Created_at: 2025-12-14 12:33:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1442#issuecomment-3650884911  

Thank you for the clarification and the reference.  
  
Since the note I added is incorrect, I will close this PR.  
Thanks again for pointing this out.

---
