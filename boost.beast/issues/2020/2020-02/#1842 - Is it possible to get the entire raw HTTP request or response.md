# #1842 - Is it possible to get the entire raw HTTP request or response? [Closed]

> Username: ghost  
> Created at: 2020-02-08 03:48:20 UTC  
> Updated at: 2020-02-09 04:42:37 UTC  
> Closed at: 2020-02-09 04:42:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1842  

Hi,  
  
I am looking at the Beast library and I would like to know if it's possible to get the entire raw HTTP request or response.  
  
I see that `message.hpp` provides functions to access the individual fields and the body. Is the raw buffer stored too? Or do we need to reconstruct it programmatically?  
  
Browsing the code, I found `boost::beast::http::request_serializer` but I wasn't able to write the code to convert a request or response to a C-style buffer.  
  
Any help would be greatly appreciated.  
Thank you  
C

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-02-08 03:51:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1842#issuecomment-583699438  

Use https://www.boost.org/doc/libs/1_72_0/libs/beast/doc/html/beast/ref/boost__beast__http__operator_lt__lt_/overload5.html

---

## Comment 2

> Username: ghost  
> Created at: 2020-02-09 04:42:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1842#issuecomment-583804269  

Thank you. It worked!
