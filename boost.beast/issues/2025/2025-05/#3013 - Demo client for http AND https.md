# #3013 - Demo client for http AND https [Closed]

> Username: octopus-prime  
> Created at: 2025-05-29 09:34:00 UTC  
> Updated at: 2025-05-31 06:54:11 UTC  
> Closed at: 2025-05-31 06:50:47 UTC  
> Url: https://github.com/boostorg/beast/issues/3013  

The demo client should have an easy to use method for sync-calls like  
```  
template <typename ResponseBody, typename RequestBody>  
http::response<ResponseBody> execute(http::request<RequestBody> request);  
```  
The method looks at the scheme of the target url of the request.  
If http then do http stuff.  
If https then do https stuff.  
  
Thanks :-)

---

## Comment 1

> Username: octopus-prime  
> Created at: 2025-05-31 06:50:47 UTC  
> Updated at: 2025-05-31 06:54:10 UTC  
> Url: https://github.com/boostorg/beast/issues/3013#issuecomment-2924523105  

There is something like 'origin-form' of target, e.g. '/books/123'.  
And frameworks like Quarkus do not accept the 'absolute-form' of target - they just return 404.  
So the target url can't be complete and so has no scheme.  
So the requested demo client can't be realized.
