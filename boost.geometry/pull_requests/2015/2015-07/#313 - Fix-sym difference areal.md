# #313 Fix/sym difference areal [Merged]

> Username: mkaravel  
> Created at: 2015-07-01 21:49:39 UTC  
> Updated at: 2015-10-14 08:16:12 UTC  
> Merged at: 2015-07-07 04:41:27 UTC  
> Closed at: 2015-07-07 04:41:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/313  

This PR fixes the symmetric difference algorithm for areal geometries.  
  
The problem with the previous algorithm is that it could generate invalid multipolygons (this could happen when the input to the symmetric difference algorithm was two areal geometries with disjoint interiors and touching boundaries).  
  
The fix proposed in this PR is to compute the differences of the first and second, and second and first geometries, and then return their union (instead of concatenating the two results).  
  
For non-areal geometries the behavior of the algorithm remains unchanged.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-07-01 21:52:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/313#issuecomment-117834721  

This PR addresses (and fixes) Boost Trac ticket [11436](https://svn.boost.org/trac/boost/ticket/11436).

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-07-01 22:01:44 UTC  
> Updated_at: 2015-07-02 17:53:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/313#discussion_r33729654  

The section calling `intersection_insert` two times can be found in both `sym_difference_generic` and `sym_difference_areal_areal`. This common code could be placed in one function or `generic` version could be called from `areal_areal` version or the `sym_difference_areal_areal` could be removed and this function calling `intersection_insert` could be called directly inside `dispatch::sym_difference_insert` below.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-07-01 22:06:05 UTC  
> Updated_at: 2015-07-02 17:53:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/313#discussion_r33730003  

The code is not entirely the same (not the semantics) hence the "duplication".  
As you can easily see in the generic version both calls output to the same output iterator.  
In the A/A version they output to a different iterator.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2015-07-01 22:18:47 UTC  
> Updated_at: 2015-07-02 17:53:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/313#discussion_r33730823  

The sym-difference was already unit-tested by test_difference (which did both, a-b, b-a, and sym). Anyway, having this problem tackled individually is also OK

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2015-07-01 22:22:08 UTC  
> Updated_at: 2015-07-02 17:53:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/313#discussion_r33731085  

Looks good.

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2015-07-01 22:23:09 UTC  
> Updated_at: 2015-07-02 17:53:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/313#discussion_r33731158  

OK - this is probably the first we have a multi_polygon as helper-geometry. But we have to. I'm OK.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2015-07-01 22:23:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/313#issuecomment-117840774  

If all tests pass I'm OK with merging.  
Thanks!

---

## Comment 8

> Username: awulkiew  
> Created_at: 2015-07-01 22:35:49 UTC  
> Updated_at: 2015-07-02 17:53:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/313#discussion_r33732077  

Ah yes, I see. Still we should somehow try to avoid the duplication. Do you think that the `detail::intersection::insert()` could be called instead? Probably not since the `reverse_dispatch` is used there internally. What do you think about closing the `intersection_insert` inside a function or altering `detail::intersection::insert()` to take a template parameter indicating if the `reverse_dispatch` should be used. Or just an overload taking an additional Reverse parameter. Then a call would take 1 or 2 lines, not 15.  
  
Btw, the names of those `intersection`... tools may be confusing, that's a side note.

---

## Comment 9

> Username: mkaravel  
> Created_at: 2015-07-01 23:07:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/313#issuecomment-117846601  

@barendgehrels I need to check possible unit test failures. I will report once I am done.

---

## Comment 10

> Username: mkaravel  
> Created_at: 2015-07-02 17:54:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/313#discussion_r33804277  

I tried to re-factor common code. Please see commit 4bd9607.

---

## Comment 11

> Username: mkaravel  
> Created_at: 2015-07-02 17:55:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/313#issuecomment-118110094  

I have checked and updated existing symmetric difference test cases. See commit 921d666.

---

## Comment 12

> Username: mkaravel  
> Created_at: 2015-07-02 18:36:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/313#issuecomment-118118839  

All algorithm unit tests pass.  
May I merge this PR?

---

## Comment 13

> Username: awulkiew  
> Created_at: 2015-07-02 21:17:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/313#discussion_r33823891  

Yes, this does look a lot better. Thanks!  
  
It could also be implemented as a function instead of a struct with apply() function. It'd then be even shorter. However then the GeometryOut would still have to be passed as a function template parameter and I guess someone could think that it's the type of the return. So I'm ok with the current implementation.  
  
Though this brings another question, why is the GeometryOut type needed in the first place in the `intersection_insert`. But that's a different story.

---

## Comment 14

> Username: awulkiew  
> Created_at: 2015-07-02 21:18:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/313#issuecomment-118171349  

Yes, I'm ok with merging. Thanks!

---

## Comment 15

> Username: mkaravel  
> Created_at: 2015-07-06 20:45:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/313#issuecomment-118993294  

Barend, are you okay with merging?

---

## Comment 16

> Username: barendgehrels  
> Created_at: 2015-07-06 21:24:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/313#issuecomment-119002299  

Sure I am, as I already mentioned. Code was enhanced after that, and I'm also OK with that.

---

## Comment 17

> Username: mkaravel  
> Created_at: 2015-07-06 21:27:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/313#issuecomment-119002858  

@barendgehrels Thanks for the immediate feedback. Will merge shortly.

---
