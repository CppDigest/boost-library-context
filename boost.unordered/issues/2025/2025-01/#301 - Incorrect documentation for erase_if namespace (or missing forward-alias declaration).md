# #301 - Incorrect documentation for erase_if namespace (or missing forward/alias declaration) [Closed]

> Username: hirokiht  
> Created at: 2025-01-20 11:32:52 UTC  
> Updated at: 2025-01-23 19:34:20 UTC  
> Closed at: 2025-01-23 19:34:18 UTC  
> Url: https://github.com/boostorg/unordered/issues/301  

Looking at documentation, one might think erase_if works in the namespace directly, i.e. `boost::erase_if(...)`, however the current implementation will only compile inside unordered namespace currently `boost::unordered::erase_if(...)`

---

## Comment 1

> Username: joaquintides  
> Created at: 2025-01-23 19:34:18 UTC  
> Url: https://github.com/boostorg/unordered/issues/301#issuecomment-2610853308  

Solved in #303.
