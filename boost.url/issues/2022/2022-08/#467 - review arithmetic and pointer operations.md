# #467 - review arithmetic and pointer operations [Closed]

> Username: vinniefalco  
> Created at: 2022-08-28 00:21:56 UTC  
> Updated at: 2022-10-07 20:23:22 UTC  
> Closed at: 2022-10-07 20:23:22 UTC  
> Url: https://github.com/boostorg/url/issues/467  

Every line that performs arithmetic or pointer calculation has to be reviewed and possibly edited for overflow and undefined behavior (in particular comparing two pointers that may not be part of the same allocated array).

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-09-27 20:42:51 UTC  
> Url: https://github.com/boostorg/url/issues/467#issuecomment-1260026488  

https://github.com/boostorg/url/pull/571#issuecomment-1260026194
