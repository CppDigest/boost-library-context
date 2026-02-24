# #205 - missing parse_origin_form [Closed]

> Username: vinniefalco  
> Created at: 2022-06-17 21:28:18 UTC  
> Updated at: 2022-08-02 04:05:06 UTC  
> Closed at: 2022-08-02 04:05:06 UTC  
> Url: https://github.com/boostorg/url/issues/205  

we need `parse_origin_form` for HTTP's request-target:  
```  
origin-form    = absolute-path [ "?" query ]  
```  
  
See: https://datatracker.ietf.org/doc/html/rfc7230#section-5.3.1

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-07-31 05:49:51 UTC  
> Url: https://github.com/boostorg/url/issues/205#issuecomment-1200354449  

> A relative reference that begins with a single slash character is termed an absolute-path reference.  A relative reference that does not begin with a slash character is termed a relative-path reference.
