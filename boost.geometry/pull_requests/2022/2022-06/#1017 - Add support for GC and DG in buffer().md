# #1017 Add support for GC and DG in buffer(). [Merged]

> Username: awulkiew  
> Created at: 2022-06-14 21:05:23 UTC  
> Updated at: 2022-06-29 20:53:28 UTC  
> Merged at: 2022-06-29 20:53:28 UTC  
> Closed at: 2022-06-29 20:53:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1017  

The algorithm calculates partial results for each element of a GC and merges them iteratively.  
  
I was forced to move parts of the code around and create the "proper" structure for the implementation with parts in `resolve_dynamic` and `dispatch`. I omitted `resolve_strategy`.  
  
One of the changes was dropping the support for vector of polygons as the output geometry since this is not a geometry. It is not tested anywhere. All of the tests pass. If you think we should keep the support for this I could bring it back.  
  
Side effect of the above is that now multipolygons are properly handled because before they were cleared with `clear()` member function which is not in line with the concept. I replaced it with `bg::clear()` so it works for GC too.  
  
This PR is built on top of https://github.com/boostorg/geometry/pull/1016 so I think we should merge the other one before reviewing this one.  
  
The result:  
  
![obraz](https://user-images.githubusercontent.com/1226951/173688572-c6e7e0d0-f573-467a-bc16-c31033532278.png)

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2022-06-29 10:29:35 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1017#pullrequestreview-1023105424  

OK, let's go for it!  
Thanks for all these efforts!

📁 include/boost/geometry/algorithms/detail/buffer/implementation.hpp

```diff
 179 |+     {
 180 |+         // NOTE: We could also allow returning GC containing only polygons.
 181 |+         //   We'd have to wrapp them in model::multi_polygon and then
```

> Username: barendgehrels  
> Created_at: 2022-06-29 10:25:33 UTC  
> Updated_at: 2022-06-29 10:29:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1017#discussion_r909463990  

`wrap`

---

📁 include/boost/geometry/algorithms/detail/buffer/implementation.hpp

```diff
 135 |+         // NOTE: This algorithm merges partial results iteratively.
 136 |+         //   We could first gather all of the results and after that
 137 |+         //   use some more optimal method like merge_elements().
```

> Username: barendgehrels  
> Created_at: 2022-06-29 10:28:25 UTC  
> Updated_at: 2022-06-29 10:29:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1017#discussion_r909466472  

The buffer normally calculates everything at once (by pieces) and traverses all of them to apply the union operation. Not even by merging elements.  
But that is complex and has led to issues as well. So I understand that you now do it like this. Maybe forever indeed. OK for me,  
but maybe you can include it in the comment.

> Username: awulkiew  
> Created_at: 2022-06-29 20:53:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1017#discussion_r910403784  

I included your comment.


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2022-06-29 12:08:03 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1017#pullrequestreview-1023233984  

I am OK with buffer gc too. Thanks!

---
