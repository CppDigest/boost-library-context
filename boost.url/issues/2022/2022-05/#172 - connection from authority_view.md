# #172 - connection from authority_view [Closed]

> Username: vinniefalco  
> Created at: 2022-05-17 14:07:47 UTC  
> Updated at: 2022-08-02 02:09:09 UTC  
> Closed at: 2022-08-02 02:09:09 UTC  
> Url: https://github.com/boostorg/url/issues/172  

In principle there should be an easy way to establish a connection (performing name resolution if required) from an authority. However, the URL library should not depend on Asio. Perhaps this could be an example. Or we could devise some trick to make it work or facilitate the operation.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-05-17 15:57:24 UTC  
> Url: https://github.com/boostorg/url/issues/172#issuecomment-1129045034  

The basic asio overload of [`basic_resolver::resolve`](https://www.boost.org/doc/libs/1_79_0/doc/html/boost_asio/reference/ip__basic_resolver/resolve/overload3.html) is:  
  
```cpp  
results_type resolve(  
    string_view host,  
    string_view service);  
```  
  
(The `results_type resolve(const query & q);` overload is deprecated)  
  
So we can't just let it implicitly convert into a `string_view` because we need two `string_view`s. :disappointed: The second idea is a free convenience function that would call `basic_resolver::resolve`, but that would make Boost.URL depend on Asio. These are the only two obvious tricks that come to mind.  
  
We then have a secondary problem, which is the problem of inferring the default port. Most URLs won't have an explicit port, and the URL protocol defines no default port for any scheme. Each scheme defines its default port in its own specification. So the HTTP /HTTPS spec defines 80/443 as a default among its other normalization rules, which should be in http_proto at some point, but this would still leave us without the ports for any other schemes.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-05-17 16:11:01 UTC  
> Url: https://github.com/boostorg/url/issues/172#issuecomment-1129059163  

We can, and should - provide a way to determine the default port for the 7 well known schemes (which include http, https, ws, and wss). And I agree that it isn't exactly straightforward how this issue would be resolved.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-08-02 02:09:09 UTC  
> Url: https://github.com/boostorg/url/issues/172#issuecomment-1201933488  

out of scope
