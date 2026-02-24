# #875 - Explore a function to calculate the URL "service" [Open]

> Username: alandefreitas  
> Created at: 2024-10-30 19:57:35 UTC  
> Updated at: 2024-10-30 20:01:36 UTC  
> Url: https://github.com/boostorg/url/issues/875  

This is often calculated as something like:  
  
```cpp  
auto service = url.has_port() ? url.port() : url.scheme();  
```  
  
The term is described in [rfc6335](https://www.rfc-editor.org/rfc/rfc6335#section-5) and mentioned in [rfc8615](https://www.rfc-editor.org/rfc/rfc8615) and [rfc7595](https://www.rfc-editor.org/rfc/rfc7595).  
  
In Asio, the [`async_resolve`](https://www.boost.org/doc/libs/develop/doc/html/boost_asio/reference/ip__basic_resolver/async_resolve/overload1.html) function returns endpoints that can be directly used for connect operations. The description of the `service` parameter is:  
  
> service  
> A string identifying the requested service. This may be a descriptive name or a numeric string corresponding to a port number. It may be an empty string, in which case all resolved endpoints will have a port number of 0.  
  
There's no concept of ports in a DNS registry, so there is a good chance it is there for users' convenience. Otherwise, users would need to transform the results one more time. It probably uses an OS service to identify the service. It might complete with an error if the service is unknown.  
  
`auto service  = url.has_port() ? url.port() : url.scheme();` might be good enough for an application with an expectation about what would already be in the URL. But a solution in Boost.URL would be more sophisticated than that:  
  
- It's independent from the internals of url_view_base. So, it could/should be a free function.  
- I still have to evaluate how useful that is because the ASIO resolver can use the OS to resolve the default port for more services.  
- When there's no scheme or port, we might need to return a `system::result<...>` so we can account for schemes we don't know about.  
- Our [`scheme`](https://www.boost.org/doc/libs/master/doc/antora/url/reference/boost/urls/scheme.html) enum is very limited, and we don't have access to an OS service for that. We could consider an official list of schemes here as in [rfc7595](https://www.rfc-editor.org/rfc/rfc7595).
