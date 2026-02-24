# #3 - parse enumeration [Closed]

> Username: vinniefalco  
> Created at: 2020-01-21 15:36:06 UTC  
> Updated at: 2021-09-14 04:49:31 UTC  
> Closed at: 2021-09-14 04:49:27 UTC  
> Url: https://github.com/boostorg/url/issues/3  

There should be an enumeration or something, to indicate how a URL string should be parsed, e.g.  
```  
enum class kind {  
  url,  
  absolute_uri,  
  relative_ref,  
  origin_form,  
  absolute_form,  
  authority_form  
};  
```  
  
See https://tools.ietf.org/html/rfc7230#section-5.3

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-09-14 04:49:27 UTC  
> Url: https://github.com/boostorg/url/issues/3#issuecomment-918798823  

This is done, and without the enum. Instead, we just have a family of functions like `parse_uri`, `parse_relative_ref`, and so on. And you can build your own parsing functions since the library gives you the primitives to put together.
