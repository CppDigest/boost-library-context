# #89 feature/projected_point [Closed]

> Username: awulkiew  
> Created at: 2014-07-04 17:13:28 UTC  
> Updated at: 2014-07-09 19:37:35 UTC  
> Closed at: 2014-07-09 12:40:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/89  

The initial commits of this PR adds a distance::detail::projected_point_ax strategy returning 2-component Point/Segment distance.  
  
The intention is to use it as a parameter of the Douglas-Peucked simplify() strategy in order to remove unneeded Points based on the parallel and perpendicular distances of the projected Point instead of the default Point/Segment distance.  
  
The ATD (along track distance) is parallel to the Segment and is a distance between Point projected into a line defined by a Segment and the nearest Segment's endpoint. If the projected Point intersects the Segment the ATD is equal to 0.  
  
The XTD (cross track distance) is perpendicular to the Segment and is a distance between input Point and its projection.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2014-07-09 09:09:11 UTC  
> Updated_at: 2014-07-09 12:00:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/89#discussion_r14699790  

T is not used - it does not compile like this.  
It should either be removed, or the operator should refer to T instead of Distance

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2014-07-09 09:10:47 UTC  
> Updated_at: 2014-07-09 12:00:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/89#discussion_r14699842  

This class does not fulfil the SimplifyConcept

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2014-07-09 09:11:11 UTC  
> Updated_at: 2014-07-09 12:00:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/89#discussion_r14699870  

Duplicated comment can be removed

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2014-07-09 09:20:14 UTC  
> Updated_at: 2014-07-09 09:44:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/89#issuecomment-48447484  

As in the mail, great, thanks a lot.  
  
For usage in simplify, it is apparently not tested and not yet ready.  
  
The lines           
  
```  
if (boost::size(range) <= 2)  || max_distance < 0)  
if (boost::size(range) <= int(Minimum)) || max_distance < 0.0)  
```  
  
in simplify.hpp do not compile for a class-distance measure. Probably the second part can just be removed.  
  
I also fixed (locally) template <typename T> and temporarily removed the concept check. And changed one double in simplify.hpp (will commit that last one).  
  
IGNORE THIS:  
~~Then it also does not work yet (I'm there now) because it complains about .apply~~

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2014-07-09 09:57:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/89#issuecomment-48450958  

For the rest it works and gives the expected result !

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2014-07-09 10:36:03 UTC  
> Updated_at: 2014-07-09 12:00:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/89#discussion_r14703079  

0 is not necessary, can be just T()

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2014-07-09 10:38:55 UTC  
> Updated_at: 2014-07-09 12:00:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/89#discussion_r14703158  

So m_max_distance.xdt is not used?

---

## Comment 8

> Username: awulkiew  
> Created_at: 2014-07-09 11:51:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/89#issuecomment-48460036  

Thanks for the review!  
  
Indeed this wasn't tested with simplify(). Now I added some tests (for the same input and expected geometries) and everything compiles. I see that you also added some tests so we should probably delete some of them.  
  
I also worked around an error related with the 0-length segments (e.g. for Rings which endpoints are equal). Now, the XTD is calculated as the distance between Points and ATD is always 0. So in some sense such segment is perpendicular the the projection vector (Pt, ProjPt).

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2014-07-09 11:54:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/89#issuecomment-48460287  

OK - I added a more complex test (hopefully does not give you merge-problems) but does not get the correct result yet...

---

## Comment 10

> Username: awulkiew  
> Created_at: 2014-07-09 11:56:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/89#issuecomment-48460484  

As for the comparison with scalar. I work around it by readding (it was there before the addition of less comparator) the `operator<` in `projected_point_ax`. So '0' is explicitly converted into the `projected_point_ax` and then compared with:  
`left.xtd < right.xtd || left.atd < right.atd`  
  
My intuition tells me that this may not work in all cases.  
  
Plus there is specialized less-comparator passed into the detail::douglas_peucker. So the same type may be compared using 2 different methods. This is rather confusing, so I'm not sure if this is a good solution.

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2014-07-09 12:04:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/89#discussion_r14705692  

Nice picture.   
  
I don't see how you can get this to work correctly in a simplify-strategy. IMO (intuitively but also see my drawing) there should be a distinction between p1 and p2, one can be considered as included, the other should not. If they have the same distance - it will not work.  
  
See also what I wrote in my original design:  
_I would like to see in that strategy that if b exceeds the line (a,c), so let's say, is lying left of the normal of the last segment of (a-c) (see below), it is either excluded or uses a larger distance to be included, or a distance can be specifyd for how far from the normal_

---

## Comment 12

> Username: awulkiew  
> Created_at: 2014-07-09 12:17:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/89#discussion_r14706089  

Yes, in the case of simple types, this would be value initialization, but for other ones this would be a default ctor which could not set the internals to 0. David some time ago reported that in some cases he was surprised that BG doesn't set the values explicitly. However AFAIR he had Points in mind. We could talk about it on the list since here is not a good place for it.

---

## Comment 13

> Username: barendgehrels  
> Created_at: 2014-07-09 12:21:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/89#discussion_r14706229  

Initialized the same way

---

## Comment 14

> Username: barendgehrels  
> Created_at: 2014-07-09 12:22:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/89#discussion_r14706256  

This requires complex types to have a constructor accepting (0).   
As far as I know, the recommended practice is to initialize with T() (see below where I just made another comment). We use this throughout.  
  
That point values are not initialized explicitly is on purpose, and indeed a different thing.

---

## Comment 15

> Username: awulkiew  
> Created_at: 2014-07-09 12:27:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/89#discussion_r14706450  

Everything compiles after doing the fixes in other parts of the code, also concept checks, so I think it's ok...

---

## Comment 16

> Username: barendgehrels  
> Created_at: 2014-07-09 12:31:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/89#issuecomment-48463359  

Oops - now 83 files changed and 83 commits - cannot really view this anymore. This is probably by merging, of course.  
It is OK for me if you merge this, we can work the details out afterwards

---

## Comment 17

> Username: awulkiew  
> Created_at: 2014-07-09 12:34:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/89#discussion_r14706696  

Ok, I think we should talk about it more, with some additional pictures.

---

## Comment 18

> Username: awulkiew  
> Created_at: 2014-07-09 12:37:42 UTC  
> Updated_at: 2014-07-09 12:40:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/89#issuecomment-48463998  

Ok, indeed because of merging I have to do it manually.

---
