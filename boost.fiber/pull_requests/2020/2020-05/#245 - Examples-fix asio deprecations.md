# #245 Examples/fix asio deprecations [Merged]

> Username: martinitus  
> Created at: 2020-05-07 08:46:17 UTC  
> Updated at: 2020-05-07 13:47:42 UTC  
> Merged at: 2020-05-07 10:23:51 UTC  
> Closed at: 2020-05-07 10:23:51 UTC  
> Url: https://github.com/boostorg/fiber/pull/245  

Resolves #244   
  
I currently don't have any build environment for fibre, so the changes are untested. Do you have a CI that can confirm that it works?  
  
Essentially its just find/replace:  
type: `io_service -> io_context`  
variable name: `io_svc -> io_ctx`  
  
I also replaced usage of `io_context::post` method to use the free `asio::post` function instead.  
  
Regards, Martin

---

## Comment 1

> Username: olk  
> Created_at: 2020-05-07 10:23:56 UTC  
> Url: https://github.com/boostorg/fiber/pull/245#issuecomment-625167460  

ty

---

## Comment 2

> Username: martinitus  
> Created_at: 2020-05-07 13:47:42 UTC  
> Url: https://github.com/boostorg/fiber/pull/245#issuecomment-625266495  

You are welcome! Keep up the nice work :+1:

---
