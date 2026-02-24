# #9 - URL resolution [Closed]

> Username: vinniefalco  
> Created at: 2020-01-23 22:07:19 UTC  
> Updated at: 2021-10-29 23:33:33 UTC  
> Closed at: 2021-10-29 23:33:33 UTC  
> Url: https://github.com/boostorg/url/issues/9  

Algorithms such as resolving a relative-ref to a base URL

---

## Comment 1

> Username: benstadin  
> Created at: 2020-03-16 21:02:42 UTC  
> Url: https://github.com/boostorg/url/issues/9#issuecomment-599757095  

FWIW, I'm using a stripped version of the uri parser from Microsofts C++ REST SDK in my Beast based project which does resolve relative paths [1] and query decoding. Just as another reference.    
  
[1] https://github.com/microsoft/cpprestsdk/blob/cdae258bfb22f948c7b768b4dc56f5f4a2d9b2ce/Release/src/uri/uri.cpp#L437

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-03-17 04:19:51 UTC  
> Url: https://github.com/boostorg/url/issues/9#issuecomment-599869901  

Yep I am going to finish this off this year, I'm currently trying to finish up the string/double conversion in Boost.JSON https://github.com/vinniefalco/json

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-09-14 04:48:52 UTC  
> Url: https://github.com/boostorg/url/issues/9#issuecomment-918798597  

"This year" turned into "2021"

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-10-29 23:33:33 UTC  
> Url: https://github.com/boostorg/url/issues/9#issuecomment-955100681  

This is done, see `resolve`
