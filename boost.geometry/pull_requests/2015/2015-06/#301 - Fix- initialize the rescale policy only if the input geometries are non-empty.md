# #301 Fix: initialize the rescale policy only if the input geometries are non-empty [Merged]

> Username: mkaravel  
> Created at: 2015-06-01 10:13:59 UTC  
> Updated at: 2015-06-15 12:59:35 UTC  
> Merged at: 2015-06-15 12:32:00 UTC  
> Closed at: 2015-06-15 12:32:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/301  

This fixes unit test failures (union, intersection, difference) when `BOOST_GEOMETRY_ENABLE_ACCESS_DEBUGGING is enabled`.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-06-01 13:03:50 UTC  
> Updated_at: 2015-06-03 14:32:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#discussion_r31422789  

Emptiness is checked two times for the same geometry.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-06-01 13:12:11 UTC  
> Updated_at: 2015-06-03 14:32:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#discussion_r31423354  

Good point thanks!  
I will fix this.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2015-06-01 13:15:52 UTC  
> Updated_at: 2015-06-03 14:32:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#discussion_r31423630  

Will in this else-branch the optimal envelope be generated in the spherical nor geographical CS? AFAIR the envelope should be calculated at once for all of the involved geometries.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-06-01 13:23:50 UTC  
> Updated_at: 2015-06-03 14:32:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#discussion_r31424201  

Fixed. See commit 87052dc.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-06-01 13:28:33 UTC  
> Updated_at: 2015-06-03 14:32:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#discussion_r31424613  

This is absolutely true: the final envelope we get this way for spherical/geographic CS may not be optimal.  
  
What we need to do to address this issue is to overload `bg::envelope()` to take two geometries as input, and properly compute their combined envelope. This requires a lot more work though, and although I agree, I consider it as outside the scope of this PR.  
  
We should do it though, that is be able to compute the envelope of two (or possibly more geometries), and we should put it in our TODO list as part of the on-going work for supporting spherical/geographic CS.

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2015-06-01 16:55:32 UTC  
> Updated_at: 2015-06-03 14:32:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#discussion_r31445350  

I think you mean just "is_empty1" here, not == 0

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2015-06-01 16:56:07 UTC  
> Updated_at: 2015-06-03 14:32:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#discussion_r31445415  

brackets can be omitted and bool can be const

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2015-06-01 16:57:09 UTC  
> Updated_at: 2015-06-03 14:32:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#discussion_r31445542  

Agreed writing it into the TODO - this PR is for fixing the rescale, not supporting spherical. But good point of course.

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2015-06-01 16:57:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#issuecomment-107637097  

I'm OK with this PR, if comments are addressed

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2015-06-01 16:58:36 UTC  
> Updated_at: 2015-06-03 14:32:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#discussion_r31445685  

I think this declaration can go to below (but don't see enough context here to be sure)

---

## Comment 11

> Username: mkaravel  
> Created_at: 2015-06-01 20:49:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#issuecomment-107703010  

@barendgehrels I believe I have addressed all your comments. In particular, regarding where to place the declaration of `env`, I have moved it a bit down.  
  
@awulkiew @barendgehrels Please let me know if it okay to merge this PR.

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2015-06-01 21:20:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#issuecomment-107716480  

OK for me! Thanks for fixing.

---

## Comment 13

> Username: mkaravel  
> Created_at: 2015-06-01 21:25:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#issuecomment-107717402  

@barendgehrels You are welcome. Thank you for the comments.

---

## Comment 14

> Username: awulkiew  
> Created_at: 2015-06-02 07:46:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#issuecomment-107844251  

I have nothing against merging this PR. However I think that in general it should be handled differently.  
IMO the `envelope()` should return bool indicating if the result is correct. And instead of the `num_points()` calls those boolean values should be checked. This could be done instead of this PR or later after merging it.

---

## Comment 15

> Username: mkaravel  
> Created_at: 2015-06-02 07:52:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#issuecomment-107846069  

We have already discussed the possibility of returning a `bool` for envelope. It is at the top of my to-do-list. Once envelope is changed to return a `bool` I will update the code related to this PR.  
Regarding the call to `num_points`, I realized that we need a new algorithm `is_empty()` that will determine if a geometry is empty (it should be more efficient than `num_points() == 0`). I am in the process of implementing it, and then check places in BG where calls to `num_points`  should be replaced by `is_empty()`.

---

## Comment 16

> Username: barendgehrels  
> Created_at: 2015-06-02 19:29:06 UTC  
> Updated_at: 2015-06-02 19:29:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#issuecomment-108067090  

About is_empty, I'm OK with that new function.  
  
About envelope returning a bool, I would like to see this in a broader context and not do it ad hoc. Compare it with centroid. It is void. Both take a (possibly empty) geometry and fill another geometry as a result. That should be the same.  
Same for point_on_surface and point_on_border (detail)  
Similar but not exactly the same with convex_hull (currently leaves output just empty)

---

## Comment 17

> Username: mkaravel  
> Created_at: 2015-06-05 20:16:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#issuecomment-109428489  

If everybody is okay with this PR, I would like to merge it.

---

## Comment 18

> Username: awulkiew  
> Created_at: 2015-06-11 15:26:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#issuecomment-111172893  

I'd be ok with merging if `envelope()` was made consistent with `is_empty()`.  
Btw, above the modified `init_rescale_policy()` overload there is one taking only one geometry. It should also be fixed.

---

## Comment 19

> Username: mkaravel  
> Created_at: 2015-06-12 07:09:47 UTC  
> Updated_at: 2015-06-12 12:07:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#issuecomment-111388366  

`envelope()` is made ~~inconsistent~~ consistent with `is_empty()` in PR #302. PR #302 has not been merged yet, so I will wait until that PR is merged first, before merging this PR.  
  
Regarding the `init_rescale_policy()` that takes one geometry, there is already a call to `is_empty()` in place. I do not understand what needs to be fixed.

---

## Comment 20

> Username: awulkiew  
> Created_at: 2015-06-12 09:58:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#issuecomment-111434241  

Did you mean "made consistent"? If that's the case I'm ok with merging. The other PR should be merged soon anyway.  
  
Ah yes, sorry. I was reading the original sources.

---

## Comment 21

> Username: mkaravel  
> Created_at: 2015-06-12 12:07:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#issuecomment-111470478  

Yes, I meant "made consistent". Sorry for the typo and the misunderstanding.

---

## Comment 22

> Username: barendgehrels  
> Created_at: 2015-06-13 09:50:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#issuecomment-111693766  

I'm OK with merging

---

## Comment 23

> Username: mkaravel  
> Created_at: 2015-06-15 12:59:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/301#issuecomment-112060106  

Although I intended to wait to merge this PR until after PR #302 is done, I merged it in order to (hopefully) get rid of some failures in the regression matrix for the develop branch.

---
