# #95 Fix for within() - ticket https://svn.boost.org/trac/boost/ticket/9628 [Merged]

> Username: awulkiew  
> Created at: 2014-07-14 14:12:00 UTC  
> Updated at: 2014-07-14 21:21:06 UTC  
> Merged at: 2014-07-14 21:20:16 UTC  
> Closed at: 2014-07-14 21:20:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/95  

The side is calculated always WRT the vertical segment if the p<1> is "equal" to one of the segment endpoint's <1>.  
For more information see the comment in the code.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2014-07-14 19:37:10 UTC  
> Updated_at: 2014-07-14 21:19:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/95#discussion_r14898611  

Means PointOfSegment needs to support a copy constructor.  
Maybe just use set<> to set all of them - it is overriden anyway

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2014-07-14 19:38:21 UTC  
> Updated_at: 2014-07-14 21:19:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/95#discussion_r14898671  

Nice trick. Please add a comment e.g. "Create a vertical segment to check the side"

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2014-07-14 19:42:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/95#issuecomment-48949602  

Thanks, Looks good - I agree. Good you added the unit tests.  
Can you process the 2 comments and merge it?

---

## Comment 4

> Username: awulkiew  
> Created_at: 2014-07-14 20:47:53 UTC  
> Updated_at: 2014-07-14 21:19:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/95#discussion_r14902682  

I did it this way to copy other coordinates for dimension > 2. But since the currently implemented side-strategies only use the coordinates for D=0 and 1 indeed the Point may be created using default ctor and set() may be used to set them. However IMHO the default-ctor is more problematic than the copy-ctor. The most straightforward way I see is to use default-constructed model::point<>. Are you ok with this?  
  
EDIT: For now I'll just use the default-constructible PointOfSegment. AFAIU this is in-line with the rest of the code. We can change it later  
  
Regarding the Point concept/requirements, the default ctor as a requirement is more troublesome. It's more common that the default ctor is private/deleted than that the class is not copy-constructible. And I think that currently various algorithms requires both, copy-constructible AND default-constructuble Points. However I'm not sure about it. I thought about checking/handling this issue. The first step would be using in the tests classes closely following the Concepts, not our models which allows to do a lot more than it's required. But it's not related with this PR.

---
