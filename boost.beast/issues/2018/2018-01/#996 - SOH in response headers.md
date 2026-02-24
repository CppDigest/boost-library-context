# #996 - SOH in response headers [Closed]

> Username: RewritableJ  
> Created at: 2018-01-24 19:43:35 UTC  
> Updated at: 2018-01-24 21:28:28 UTC  
> Closed at: 2018-01-24 21:28:28 UTC  
> Url: https://github.com/boostorg/beast/issues/996  

Hello.  
  
When I make request to certain server the response contains some cookie headers. Those headers contain SOH character that is treated as invalid by basic_parser_base causing bad_value error code, though the response is fine.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-24 21:12:34 UTC  
> Url: https://github.com/boostorg/beast/issues/996#issuecomment-360274913  

SOH is a control character with the value 0x01? That is an illegal character according to rfc2616 and rfc7230 (the HTTP protocol specifications). If you want to accept such characters as valid HTTP, you need to filter the input before passing it to Beast.
