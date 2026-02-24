# #261 - top level rfc rules should have value_type = url_view [Closed]

> Username: vinniefalco  
> Created at: 2022-07-28 01:52:47 UTC  
> Updated at: 2022-07-30 02:19:14 UTC  
> Closed at: 2022-07-30 02:19:14 UTC  
> Url: https://github.com/boostorg/url/issues/261  

Top level rules like `uri_reference_rule` and `absolute_uri_rule` should have   
  
```  
using value_type = url_view;  
```  
  
so they can be used in the composition of custom parsers.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-07-28 01:55:24 UTC  
> Url: https://github.com/boostorg/url/issues/261#issuecomment-1197561529  

Also `query_rule` should use  
```  
using value_type = params_encoded_view;  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-07-28 02:00:16 UTC  
> Url: https://github.com/boostorg/url/issues/261#issuecomment-1197564105  

Path rules should return segment views. And we should remove `parsed_path`.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-07-28 02:02:45 UTC  
> Url: https://github.com/boostorg/url/issues/261#issuecomment-1197565593  

ipv4 / ipv6 rules should return `ip_v4address` and `ip_v6address`
