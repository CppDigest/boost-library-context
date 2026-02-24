# #255 Feature/disjoint: implement disjoint for multipoint/[multi]linestring [Merged]

> Username: mkaravel  
> Created at: 2015-03-10 00:47:44 UTC  
> Updated at: 2015-04-23 16:35:57 UTC  
> Merged at: 2015-04-23 15:23:05 UTC  
> Closed at: 2015-04-23 15:23:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/255  

In this PR I implement `bg::disjoint()` for the following two (still missing) geometry combinations:  
- multipoint/linestring  
- multipoint/multilinestring  
  
The implemented algorithm uses `bg::partition()`: `bg::partition()` is called on two collections: the points of the multipoint and the segments of the linear geometry. To extract the segments of the linear geometry the `bg::segment_iterator<>` is used.  
  
Unit tests have been added as part of this PR, and the documentation for `bg::disjoint()` is also updated (including release notes).  
  
This PR is targeted for boost 1.59 and depends on PR #244 (the version of `bg::partition()` in PR #244 can handle a range of segments created via the `bg::segment_iterator<>`, whereas this is not possible in the version of `bg::partition()` in the current version of BG's develop branch).  
  
As part of this PR, I have also slightly re-arranged and simplified the implementation of `bg::disjoint()` for the geometry combinations point/segment, point/linear.  
  
This PR can be merged independently of PR #244; the unit tests however will be failing until PR #244 is also merged.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-04-12 19:28:53 UTC  
> Updated_at: 2015-04-23 07:06:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/255#discussion_r28206445  

What was wrong with the previous version?  
Wasn't it the same as `reverse_covered_by` used below for Pt/Linear?  
Or is `covered_by()` not defined for Pt/Seg?  
  
Isn't Pt/Seg algorithm faster than Seg/Seg. AFAIU in Seg/Seg points must be checked for equality and then Pt/Seg version called. So there is additional condition and the algorithm contains unnecessary code (Seg/Seg even if only Pt/Seg is needed) which can potentialy harm the instructions cache. Or am I missing something?

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-04-12 21:33:26 UTC  
> Updated_at: 2015-04-23 07:06:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/255#discussion_r28207727  

I think this is a new combination - the diff seems a bit off here  
!geometry::covered_by is still used

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-04-22 16:59:38 UTC  
> Updated_at: 2015-04-23 07:06:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/255#discussion_r28892727  

The point-segment disjointness test was simply moved from one place of the file to another. The actual implementation was not changed.  
  
What I eliminated (but it is not visible easily in the diff) is `disjoint_point_linear`. Its implementation was the same as `reverse_covered_by`, so I simply use `reverse_covered_by` in the new version.  
  
The changes in this file are all about code refactoring and polishing. There is nothing new.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2015-04-22 17:05:29 UTC  
> Updated_at: 2015-04-23 07:06:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/255#discussion_r28893203  

What I had in mind is, that `reverse_covered_by` could also be used for Pt/Seg. AFAIU there is no need to have `disjoint_point_segment` becasue assuming that `covered_by()` works for Pt/Seg, `reverse_covered_by` should work out of the box. Or am I missing something?

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-04-22 17:08:44 UTC  
> Updated_at: 2015-04-23 07:06:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/255#discussion_r28893473  

It seems that `covered_by` is indeed implemented for the point-segment combination.  
I will double-check and modify the code as per your suggestion if `covered_by` does indeed work.

---

## Comment 6

> Username: mkaravel  
> Created_at: 2015-04-23 07:07:35 UTC  
> Updated_at: 2015-04-23 16:35:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/255#discussion_r28940627  

I have replaced `disjoint_point_segment` by `reverse_covered_by` as suggested.  
see commit 7bd759d.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2015-04-23 15:23:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/255#issuecomment-95623483  

Thanks!

---
