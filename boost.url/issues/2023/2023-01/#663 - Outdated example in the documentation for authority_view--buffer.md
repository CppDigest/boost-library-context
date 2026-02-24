# #663 - Outdated example in the documentation for authority_view::buffer [Closed]

> Username: ea1111  
> Created at: 2023-01-18 11:03:58 UTC  
> Updated at: 2023-01-20 02:51:03 UTC  
> Closed at: 2023-01-20 02:51:03 UTC  
> Url: https://github.com/boostorg/url/issues/663  

The [documentation for authority_view::buffer](https://www.boost.org/doc/libs/master/libs/url/doc/html/url/ref/boost__urls__authority_view/buffer.html) gives the example  
  
`assert( parse_authority( "www.example.com" ).value().encoded_authority() == "www.example.com" );`  
  
However, I believe this should be  
  
`assert( parse_authority( "www.example.com" ).value().buffer() == "www.example.com" );`  
  
since encoded_authority() was renamed to buffer().

---

## Comment 1

> Username: alandefreitas  
> Created at: 2023-01-18 14:40:55 UTC  
> Url: https://github.com/boostorg/url/issues/663#issuecomment-1387181797  

Thanks. You're right. It should be `buffer()`.  
  
`encoded_authority()` still exists in `url_view` but it doesn't make sense in the `authority_view` that `parse_authority` already returns.
