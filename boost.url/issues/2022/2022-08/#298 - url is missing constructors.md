# #298 - url is missing constructors [Closed]

> Username: vinniefalco  
> Created at: 2022-08-02 17:30:19 UTC  
> Updated at: 2022-08-03 23:45:35 UTC  
> Closed at: 2022-08-03 23:45:34 UTC  
> Url: https://github.com/boostorg/url/issues/298  

It seems `url` is missing a constructor from string (throwing overload that parses as a URI or relative-ref). `url_view` has the constructor, we can copy from there.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-08-03 21:51:08 UTC  
> Updated at: 2022-08-03 22:04:29 UTC  
> Url: https://github.com/boostorg/url/issues/298#issuecomment-1204522661  

This?  
  
https://github.com/CPPAlliance/url/blob/ac3b4e784ed391f55f050042b2b9fc147e0673c0/include/boost/url/url.hpp#L257-L258  
  
and  
  
https://github.com/CPPAlliance/url/blob/ac3b4e784ed391f55f050042b2b9fc147e0673c0/include/boost/url/static_url.hpp#L188

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-08-03 22:21:26 UTC  
> Url: https://github.com/boostorg/url/issues/298#issuecomment-1204543503  

oh, I missed it? Weird..

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-08-03 22:21:32 UTC  
> Url: https://github.com/boostorg/url/issues/298#issuecomment-1204543553  

Please close this if it is not relevant

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-08-03 23:45:34 UTC  
> Url: https://github.com/boostorg/url/issues/298#issuecomment-1204588517  

32 issues
