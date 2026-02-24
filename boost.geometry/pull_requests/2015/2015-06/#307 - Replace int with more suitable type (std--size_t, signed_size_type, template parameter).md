# #307 Replace int with more suitable type (std::size_t, signed_size_type, template parameter). [Merged]

> Username: awulkiew  
> Created at: 2015-06-09 10:03:22 UTC  
> Updated at: 2015-06-11 16:26:06 UTC  
> Merged at: 2015-06-11 16:21:17 UTC  
> Closed at: 2015-06-11 16:21:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/307  

This affects buffer, overlay, relate and sectionalize.  
  
Please for now treat `signed_index_type` and `signed_size_type` as one type. I still plan to replace the former with the latter consistently in the whole library. This is not the intention of this PR.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-06-09 20:36:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/307#discussion_r32060766  

Be aware that this makes the type probably larger. This might influence our maximum number of intersections (memory limited)

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-06-09 20:37:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/307#discussion_r32060899  

I thought we should rename signed_index_type to signed_size_type and that it was already done (at least in buffer),  
so I don't understand why signed_index_type is now popping up here again...

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2015-06-09 20:38:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/307#discussion_r32060975  

Please don't.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2015-06-09 20:38:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/307#discussion_r32061007  

Yes, this might be right.

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2015-06-09 20:40:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/307#issuecomment-110498297  

Thanks, I'm basically OK with merging

---

## Comment 6

> Username: awulkiew  
> Created_at: 2015-06-10 00:11:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/307#discussion_r32078322  

Indeed, was there a reason why for the `travels_to_vertex_index` a different type was used? Are they all absolute indexes? Or are the other two e.g. offsets?

---

## Comment 7

> Username: awulkiew  
> Created_at: 2015-06-10 00:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/307#discussion_r32078403  

Yes, they will be renamed consistently in the whole library. For now in other places in this file the old name is used so I used it here as well to not mix them.

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2015-06-10 07:48:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/307#discussion_r32094883  

Okidoki

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2015-06-10 07:49:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/307#discussion_r32094915  

It is and stays sometimes unassigned. So -1 means unassigned.

---

## Comment 10

> Username: awulkiew  
> Created_at: 2015-06-10 11:18:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/307#discussion_r32107589  

Yes I understand that. But is it an absolute index or is it a difference between some relativaly close indexes (so an offset)? Because if the latter was true, then I suppose a smaller type could be used.  
  
Btw, AFAIR source_index in segment_iterator and ring_iterator could be a smaller type if there was a problem, because only 0 or 1 is assigned.

---
