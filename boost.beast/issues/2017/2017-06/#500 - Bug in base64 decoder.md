# #500 - Bug in base64 decoder [Closed]

> Username: hoditohod  
> Created at: 2017-06-16 08:22:16 UTC  
> Updated at: 2017-06-16 16:19:20 UTC  
> Closed at: 2017-06-16 16:19:20 UTC  
> Url: https://github.com/boostorg/beast/issues/500  

Base64 inverse table for entry for 65 'A' is -1, whereas it should be 0.  
  
base64.hpp:69  
  
### Version of Beast  
54  
  
### Steps necessary to reproduce the problem  
N/A  
  
### All relevant compiler information  
N/A

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-16 11:30:36 UTC  
> Url: https://github.com/boostorg/beast/issues/500#issuecomment-309002879  

willfix

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-16 11:45:38 UTC  
> Url: https://github.com/boostorg/beast/issues/500#issuecomment-309005332  

How did you find that? Good job by the way :)

---

## Comment 3

> Username: hoditohod  
> Created at: 2017-06-16 11:57:57 UTC  
> Url: https://github.com/boostorg/beast/issues/500#issuecomment-309008338  

Well, uhm... I use bits and pieces from the beast::detail namespace... don't tell anyone!

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-06-16 12:05:48 UTC  
> Url: https://github.com/boostorg/beast/issues/500#issuecomment-309009649  

I added a much stronger test, it should make sure this never happens again
