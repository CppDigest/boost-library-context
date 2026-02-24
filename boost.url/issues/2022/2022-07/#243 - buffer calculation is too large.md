# #243 - buffer calculation is too large [Closed]

> Username: vinniefalco  
> Created at: 2022-07-24 14:07:39 UTC  
> Updated at: 2022-09-11 23:56:39 UTC  
> Closed at: 2022-09-11 23:56:39 UTC  
> Url: https://github.com/boostorg/url/issues/243  

The calculation for the needed buffer size includes table entries which we aren't using yet, and can overflow:  
https://github.com/CPPAlliance/url/blob/aecf3a621805c764017dfb92a79008cb74f1229c/include/boost/url/impl/url.ipp#L2360

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-23 22:22:43 UTC  
> Url: https://github.com/boostorg/url/issues/243#issuecomment-1224949877  

This might have been fixed already but it needs an audit

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-09-11 23:56:39 UTC  
> Url: https://github.com/boostorg/url/issues/243#issuecomment-1243072389  

Fixed
