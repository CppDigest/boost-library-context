# #436 - Review: url::persist docs [Closed]

> Username: alandefreitas  
> Created at: 2022-08-22 04:42:40 UTC  
> Updated at: 2022-10-14 16:47:03 UTC  
> Closed at: 2022-10-14 16:47:02 UTC  
> Url: https://github.com/boostorg/url/issues/436  

> This function returns a read-only copy of the URL, with shared  
   lifetime. The returned value owns (persists) the underlying string.  
   The algorithm used to create the value minimizes the number of  
   individual memory allocations, making it more efficient than when  
   using direct standard library functions.  
> Hmm, so url_view doesn't own the underlying storage, except when it  
does as a result of this mysterious method. What's the rationale for  
this? (Have I missed some documentation?)  
  
We have already discussed the issue here https://github.com/CPPAlliance/url/pull/217 but there's probably room to improve the docs.  
  
I've been thinking this shouldn't be in the "quick look" or any other introductory sections. It's probably confusing at this point.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-08-22 21:29:17 UTC  
> Url: https://github.com/boostorg/url/issues/436#issuecomment-1223097982  

> I found the solution in function url_view::persist() very innovative: you  
> have one allocation instead of three.  
>

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-08-22 21:29:30 UTC  
> Url: https://github.com/boostorg/url/issues/436#issuecomment-1223098551  

#217

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-10-14 16:47:02 UTC  
> Url: https://github.com/boostorg/url/issues/436#issuecomment-1279239962  

> I've been thinking this shouldn't be in the "quick look" or any other introductory sections. It's probably confusing at this point.  
  
This already fixed itself
