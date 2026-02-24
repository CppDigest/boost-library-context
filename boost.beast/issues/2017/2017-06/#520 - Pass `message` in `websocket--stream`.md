# #520 - Pass `message` in `websocket::stream` [Closed]

> Username: vinniefalco  
> Created at: 2017-06-21 04:27:21 UTC  
> Updated at: 2017-08-16 00:21:20 UTC  
> Closed at: 2017-08-16 00:21:20 UTC  
> Url: https://github.com/boostorg/beast/issues/520  

Stream `accept` signatures should read:  
```  
template<class Allocator>  
void  
accept(http::request<http::empty_body, basic_fields<Allocator>> const&);  
```  
  
Currently they accept a `header`. This is misleading and raises the problem of slicing. Most HTTP servers, however, will not read requests into an empty body. They will have a `string_body` or a `dynamic_body`. The caller will need to construct a request with an empty body out of their existing request object with a body. This pushes the responsibility of validating the upgrade request onto the caller, which I think is the right thing to do.  
  
A test should be added that move constructs a request with `empty_body` out of a request from `string_body` to make sure users will have no trouble doing so.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-16 00:21:20 UTC  
> Url: https://github.com/boostorg/beast/issues/520#issuecomment-322624177  

Done
