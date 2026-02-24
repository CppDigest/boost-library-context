# #39 [expand] Add variant support [Merged]

> Username: sdebionne  
> Created at: 2014-05-23 08:57:24 UTC  
> Updated at: 2014-05-26 08:27:25 UTC  
> Merged at: 2014-05-23 22:03:55 UTC  
> Closed at: 2014-05-23 22:03:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/39  

This PR adds variant support to the expand algorithm.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-05-23 10:42:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/39#issuecomment-43994080  

I think the 'box' should also be resolved. The user might store various boxes in some Variant. Furthermore there may be more Envelopes/BoundingGeometries types (there is nsphere in the extensions).

---

## Comment 2

> Username: brunolalande  
> Created_at: 2014-05-23 12:58:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/39#issuecomment-44005469  

Hmm yes but for now it's only a box-oriented algo, we can make it variant  
aware when more than box will be supported, at which point plenty of  
box/Box parameters will need renaming. I'd stick to that for now (the use  
case to pass variants with several types of boxes is quite a particular  
one, and far from the primary rationale behind variant-aware algos).  
  
Bruno

---

## Comment 3

> Username: sdebionne  
> Created_at: 2014-05-23 13:12:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/39#issuecomment-44006719  

Ouch, shall I revert my last commit then ?

---

## Comment 4

> Username: brunolalande  
> Created_at: 2014-05-23 14:14:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/39#issuecomment-44013686  

Yeah I would prefer, the way it is right now doesn't really match what I've  
done so far (i.e. varianting only those things we really dispatch on, not  
those which we expect to be of a specific type). Apparently it's one  
specific commit so shouldn't be too hard to revert.  
  
Bruno

---
