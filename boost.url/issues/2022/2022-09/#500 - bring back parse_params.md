# #500 - bring back parse_params [Closed]

> Username: vinniefalco  
> Created at: 2022-09-04 13:31:39 UTC  
> Updated at: 2022-09-11 23:35:51 UTC  
> Closed at: 2022-09-11 23:35:51 UTC  
> Url: https://github.com/boostorg/url/issues/500  

application/x-www-form-urlencoded uses query params but there is no leading "?" so currently there is no way to parse it. Furthermore the treatment of tilde ('~') is different:  
  
> The octet corresponding to the tilde ("~") is permitted in query strings by RFC3986 but required to be percent-encoded in HTML forms to "%7E".  
  
The complication is that the const params containers currently can only be constructed from a `url_view_base`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-11 23:35:51 UTC  
> Url: https://github.com/boostorg/url/issues/500#issuecomment-1243068403  

done
