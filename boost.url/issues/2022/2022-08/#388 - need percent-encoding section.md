# #388 - need percent-encoding section [Closed]

> Username: vinniefalco  
> Created at: 2022-08-12 03:44:06 UTC  
> Updated at: 2022-10-17 21:30:21 UTC  
> Closed at: 2022-10-17 21:30:21 UTC  
> Url: https://github.com/boostorg/url/issues/388  

We need a section, fairly early on which explains how the library treats percent encoding, with the various string types and views. And the algorithms provided as free functions.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-08-12 06:35:32 UTC  
> Url: https://github.com/boostorg/url/issues/388#issuecomment-1212777654  

We have something in the quick look about `pct_encoded_view`. Should we interleave this content with "parsing"? Or a section before containers?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-08-12 16:33:04 UTC  
> Updated at: 2022-08-12 16:33:17 UTC  
> Url: https://github.com/boostorg/url/issues/388#issuecomment-1213306515  

I have to think about how we will integrate this and what will be in it. In any case it has to wait until after review.
