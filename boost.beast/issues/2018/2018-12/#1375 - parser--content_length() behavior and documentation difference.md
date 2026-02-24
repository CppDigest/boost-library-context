# #1375 - parser::content_length() behavior and documentation difference [Closed]

> Username: iceboy233  
> Created at: 2018-12-18 06:38:07 UTC  
> Updated at: 2019-03-29 22:07:23 UTC  
> Closed at: 2019-03-29 22:07:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1375  

The documentation says `content_length()` "Returns the optional value of Content-Length if known.":  
https://www.boost.org/doc/libs/1_69_0/libs/beast/doc/html/beast/ref/boost__beast__http__basic_parser/content_length.html  
  
But it actually returns the remaining size, if present:  
https://github.com/boostorg/beast/blob/boost-1.69.0/include/boost/beast/http/impl/basic_parser.ipp#L72  
https://github.com/boostorg/beast/blob/boost-1.69.0/include/boost/beast/http/impl/basic_parser.ipp#L624  
  
I would suggest to create two functions, one for the real content length (if can be implemented zero-cost), and one for the remaining size. `content_length()` name is misleading but has existing code so we might not want to change its behavior, consider deprecating.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-18 14:28:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1375#issuecomment-448238817  

hmm..looks like a bug! I think we should simply fix it, label it as an API change, and probably add another function which returns the amount remaining if known.

---

## Comment 2

> Username: iceboy233  
> Created at: 2019-03-29 21:26:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1375#issuecomment-478154867  

Bump - boost 1.70 is close to release
