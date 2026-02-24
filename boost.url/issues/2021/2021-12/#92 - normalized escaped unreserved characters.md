# #92 - normalized escaped unreserved characters [Closed]

> Username: vinniefalco  
> Created at: 2021-12-27 03:19:39 UTC  
> Updated at: 2022-03-17 18:25:54 UTC  
> Closed at: 2022-03-17 18:25:54 UTC  
> Url: https://github.com/boostorg/url/issues/92  

Characters from the unreserved set which are escaped in a URL should have percent-decoding applied during normalization: https://datatracker.ietf.org/doc/html/rfc3986#section-2.3

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-03-15 17:07:39 UTC  
> Url: https://github.com/boostorg/url/issues/92#issuecomment-1068236086  

Unless I'm misunderstanding this issue, I think this is already implemented as part of normalization.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-03-16 17:22:42 UTC  
> Url: https://github.com/boostorg/url/issues/92#issuecomment-1069378791  

Is it?

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-03-16 17:47:04 UTC  
> Url: https://github.com/boostorg/url/issues/92#issuecomment-1069393735  

Yes. 100%.
