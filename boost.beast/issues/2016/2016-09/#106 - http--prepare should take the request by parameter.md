# #106 - http::prepare should take the request by parameter [Closed]

> Username: vinniefalco  
> Created at: 2016-09-30 16:37:23 UTC  
> Updated at: 2017-06-08 05:25:41 UTC  
> Closed at: 2017-06-08 05:25:41 UTC  
> Url: https://github.com/boostorg/beast/issues/106  

`http::prepare` needs the request in order to fully satisfy the requirements in rfc7230. This would be an API change.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-08 05:25:41 UTC  
> Url: https://github.com/boostorg/beast/issues/106#issuecomment-307001917  

Prepare free function is now a member of `message` and greatly reduced in function. This issue is no longer applicable.
