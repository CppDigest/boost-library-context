# #1062 Add field enum entry for "Access-Control-Expose-Headers" [Closed]

> Username: ivilata  
> Created at: 2018-03-09 13:23:49 UTC  
> Updated at: 2018-03-11 20:49:15 UTC  
> Closed at: 2018-03-11 20:49:15 UTC  
> Url: https://github.com/boostorg/beast/pull/1062  

This response header is described both in [CORS](https://www.w3.org/TR/cors/) and [Fetch](https://fetch.spec.whatwg.org/) specifications.

---

## Comment 1

> Username: ivilata  
> Created_at: 2018-03-09 13:26:40 UTC  
> Url: https://github.com/boostorg/beast/pull/1062#issuecomment-371811378  

This was previously PR #1021, I added the header to the file used as a source list of headers plus another file where I had forgotten to add the header.  
  
There are probably other headers missing from various HTTP RFCs, but AFAIK this should complete the set of headers used for CORS.

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2018-03-11 20:49:15 UTC  
> Url: https://github.com/boostorg/beast/pull/1062#issuecomment-372147893  

Merged to 164

---
