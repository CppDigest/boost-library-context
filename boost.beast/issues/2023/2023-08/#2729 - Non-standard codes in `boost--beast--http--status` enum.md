# #2729 - Non-standard codes in `boost::beast::http::status` enum [Closed]

> Username: ecorm  
> Created at: 2023-08-21 22:30:26 UTC  
> Updated at: 2024-02-06 14:39:50 UTC  
> Closed at: 2024-02-06 14:39:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2729  

Version of Beast: boost-1.83.0  
  
The following [status codes](https://github.com/boostorg/beast/blob/35d533299c129d622af774f96b820e8f18df068f/include/boost/beast/http/status.hpp#L21) are non-standard:  
- `connection_closed_without_response  = 444,`  
- `client_closed_request               = 499,`  
- `network_connect_timeout_error       = 599`  
  
I did some digging to find out what these non-standard status code are for:  
  
---  
  
Status code 444 can be used the in [NGINX return directive](https://nginx.org/en/docs/http/ngx_http_rewrite_module.html#return) to:  
> close a connection without sending a response header.  
  
---  
  
From what I can tell by this [Stackoverflow Q&A](https://stackoverflow.com/q/12973304/245265), status code 499 is used in the NGINX log to indicate that the client closed the connection before the server could respond.  
  
---  
  
The origin of status code 599 is [unknown according to this GH issue](https://github.com/rmaake1/httpstatuses/issues/22).  
  
---  
  
What is the rationale for these non-standard status codes appearing in the `boost::beast::http::status` enum? If they serve a purpose, it should be documented at least as comments in the source code.  
  
If they were accidentally copy-pasted from somewhere, you may want to consider deprecating them as non-standard.

---

## Comment 1

> Username: ecorm  
> Created at: 2023-08-21 22:32:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2729#issuecomment-1687140085  

I've also noticed that [status code 103](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/103) is missing, but I'm assuming that it's because it's experimental.

---

## Comment 2

> Username: ashtum  
> Created at: 2024-01-18 12:36:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2729#issuecomment-1898400808  

Yes, `444` and `499` are specific to Nginx, and `599` is a non-standard code.  
It might be a good idea to remove them and keep only the standard ones, as we didn't list all of the Nginx codes either.  
@vinniefalco any comment on this?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2024-01-20 21:45:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2729#issuecomment-1902272286  

There's some RFC or webpage where I copied this list from. We should try to find that page and see what their rationale is for including those items.

---

## Comment 4

> Username: ecorm  
> Created at: 2024-01-20 21:55:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2729#issuecomment-1902274098  

> There's some RFC or webpage where I copied this list from. We should try to find that page and see what their rationale is for including those items.  
  
Is it this one? https://developer.mozilla.org/en-US/docs/Web/HTTP/Status  
  
Just adding a source-code comment of where they originated from would suffice, IMHO. I've just realized that removing the non-standard ones may break some apps that ended up using them.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2024-01-24 12:53:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2729#issuecomment-1908067013  

Ah yes. My thinking was that since there are only a couple of non standard codes and since new codes are not added frequently (or at all) there is no harm in listing them.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2024-01-24 12:55:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2729#issuecomment-1908069390  

They are also copied here  
https://github.com/cppalliance/http_proto/blob/3de809f7cf8b6e92a9a6f85ee2ffe6f58f9db875/include/boost/http_proto/status.hpp#L105  
  
@cmazakas

---

## Comment 7

> Username: ashtum  
> Created at: 2024-01-30 07:32:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2729#issuecomment-1916233710  

> > There's some RFC or webpage where I copied this list from. We should try to find that page and see what their rationale is for including those items.  
>   
> Is it this one? https://developer.mozilla.org/en-US/docs/Web/HTTP/Status  
  
But `444`, `499` and `599` are not listed there either. I guess we can remove them and keep only the standard ones. If anyone is using these, they can use the equivalent integer numbers.

---

## Comment 8

> Username: ecorm  
> Created at: 2024-01-31 01:14:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2729#issuecomment-1918186401  

Here is the IANA registry of HTTP status codes: https://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml  
  
The "core" HTTP status codes are in RFC9110, section 15.  
  
I guess you need to decide to what level you want to enumerate known status codes in Beast.

---

## Comment 9

> Username: ecorm  
> Created at: 2024-01-31 01:38:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2729#issuecomment-1918206647  

If you want the `http::status` enum to match the IANA registry, then the following entries are missing:  
  
- 103 Early Hints (https://www.iana.org/go/rfc8297)  
- 425 Too Early (https://www.iana.org/go/rfc8470)  
  
The following enumerators are **not** in the IANA registry:  
  
- `connection_closed_without_response  = 444`  
- `client_closed_request               = 499`  
- `network_connect_timeout_error       = 599`
