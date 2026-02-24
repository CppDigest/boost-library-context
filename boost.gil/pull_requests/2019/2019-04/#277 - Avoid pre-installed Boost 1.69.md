# #277 [azp] Avoid pre-installed Boost 1.69 [Merged]

> Username: mloskot  
> Created at: 2019-04-10 19:57:17 UTC  
> Updated at: 2019-09-26 10:42:01 UTC  
> Merged at: 2019-04-10 21:14:33 UTC  
> Closed at: 2019-04-10 21:14:33 UTC  
> Url: https://github.com/boostorg/gil/pull/277  

Linux and Windows images now come with pre-installed Boost 1.69.  
Since it is not possible to remove GIL headers from it, we should  
prefer our deployment of Boost.  
  
### References  
  
* #276  
* #279  
* C/C++: Add the latest 4 versions of Boost 1.66.0 - 1.69.0 (Linux, Windows)  
  Microsoft/azure-pipelines-image-generation#732  
* Document available versions of Boost 1.66.0 - 1.69.0 following #732 update  
  Microsoft/azure-pipelines-image-generation#845  
  
### Tasklist  
  
- [x] All CI builds and checks have passed (https://dev.azure.com/boostorg/gil/_build/results?buildId=332)

---
