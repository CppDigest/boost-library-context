# #65 - [IntegralConstant] Should `times` be tag-dispatched? [Closed]

> Username: ldionne  
> Created at: 2015-05-04 16:47:15 UTC  
> Updated at: 2015-05-21 20:51:57 UTC  
> Closed at: 2015-05-21 20:51:57 UTC  
> Url: https://github.com/boostorg/hana/issues/65  

Anything that can be incremented, decremented and compared to some "zero" value can implement a `times` method with the same semantics. Hence, it might be worthwhile to abstract this into a concept. Maybe the added complexity isn't worth it, though.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-05-21 20:51:57 UTC  
> Url: https://github.com/boostorg/hana/issues/65#issuecomment-104417039  

Not adding another concept just for this. I think the added complexity isn't worth the conceptual completeness in this case.
