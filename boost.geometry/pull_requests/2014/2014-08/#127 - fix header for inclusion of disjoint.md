# #127 [algorithms][buffer] fix header for inclusion of disjoint [Merged]

> Username: mkaravel  
> Created at: 2014-08-08 09:27:42 UTC  
> Updated at: 2014-08-08 21:50:32 UTC  
> Merged at: 2014-08-08 17:39:27 UTC  
> Closed at: 2014-08-08 17:39:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/127  

It is the point-box and not the box-box version that is needed; also the call to disjoint is not done via the free function but rather using the disjoint dispatch struct

---

## Comment 1

> Username: mkaravel  
> Created_at: 2014-08-08 09:30:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/127#discussion_r15984673  

Why not use `turn.get_robust_point()` instead of directly accessing the member variable?  
Looking at the buffer's turn info class it seems to me that this should be the correct thing to do.  
Same comment applies to the call to`geometry::expand` a few lines above.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2014-08-08 17:39:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/127#discussion_r16005483  

That get_robust_point is introduced for the mapped_robust_point/robust_point compatibility.  
But that will be removed at all, it did not work in all cases.  
So I probably will remove the get_robust_point() function too.  
In internal helper-structs we often access members directly.  
  
Thanks for the fix, BTW.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2014-08-08 17:41:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/127#discussion_r16005619  

@barendgehrels Okay, did not know the whole story. I saw the method in the sources and I wondered.  
I did not change it though, but wanted to ask about it.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2014-08-08 18:27:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/127#discussion_r16008574  

Sure, I noticed that. Fine.

---
