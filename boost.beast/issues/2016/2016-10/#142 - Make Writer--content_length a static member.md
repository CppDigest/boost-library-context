# #142 - Make Writer::content_length a static member [Closed]

> Username: vinniefalco  
> Created at: 2016-10-15 22:10:05 UTC  
> Updated at: 2016-11-09 21:52:19 UTC  
> Closed at: 2016-11-09 21:52:19 UTC  
> Url: https://github.com/boostorg/beast/issues/142  

This elegantly solves the problem of having to construct the Writer twice, once to set the content length and again to actually serialize it. It creates a new problem which is that `prepare` will be generally less useful for certain body types but that's okay, `prepare` likely will only be applicable to the simplest of use cases. For example, sending an error response with some canned text in the body.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-11-09 21:52:18 UTC  
> Url: https://github.com/boostorg/beast/issues/142#issuecomment-259536968  

Or get rid of `content_length` entirely per #178
