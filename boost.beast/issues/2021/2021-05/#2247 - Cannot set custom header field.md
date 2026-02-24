# #2247 - Cannot set custom header field [Closed]

> Username: benstadin  
> Created at: 2021-05-26 15:41:28 UTC  
> Updated at: 2021-05-26 16:18:14 UTC  
> Closed at: 2021-05-26 16:18:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2247  

I'm setting a custom header field in a HTTP (async) client via  
   
`req.insert("X-API-KEY", "1234");`  
  
On the server side however the field name will always be "<unknown-field>". I'm iterating through fields and get strings of field names via  
  
`boost::beast::http::to_string(field.name()).data()`  
  
I'm using Boost version 1.76

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-05-26 15:46:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2247#issuecomment-848886591  

From https://www.boost.org/doc/libs/1_76_0/libs/beast/doc/html/beast/ref/boost__beast__http__basic_fields__value_type.html  
  
`field.name()` returns the field enum, which will be `field::unknown` since it is custom.  
  
Get the field name using `field.name_string()` instead (no need for `to_string`).

---

## Comment 2

> Username: benstadin  
> Created at: 2021-05-26 16:18:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2247#issuecomment-848910891  

Thank you for the quick response. That worked.
