# #74 - copied_strings [Closed]

> Username: vinniefalco  
> Created at: 2021-11-01 01:31:57 UTC  
> Updated at: 2022-02-25 00:46:39 UTC  
> Closed at: 2022-02-25 00:46:39 UTC  
> Url: https://github.com/boostorg/url/issues/74  

Use copied_strings in every mutating algorithm that needs it

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-02-23 17:14:57 UTC  
> Url: https://github.com/boostorg/url/issues/74#issuecomment-1049014995  

Could you explain this issue a little better?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-02-24 16:25:43 UTC  
> Updated at: 2022-02-24 16:25:55 UTC  
> Url: https://github.com/boostorg/url/issues/74#issuecomment-1050031504  

Yes, when calling a mutating function (for example in `url`) that takes a `string_view` parameter, and the caller passes a string which is from inside the container. This helper class assists with that:  
https://github.com/CPPAlliance/url/blob/674153089551371eb05901d615c1702e258eadfc/include/boost/url/detail/copied_strings.hpp#L19  
  
For example  
```  
url u = parse_uri( "http://example.com?k1=42&k2=69" );  
u.params().emplace_back( "k3", u.params().find( "k2" )->value );  
```
