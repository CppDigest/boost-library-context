# #1543 - Feature request: forward declarations for http request/response types [Closed]

> Username: Eelis  
> Created at: 2019-03-24 22:49:13 UTC  
> Updated at: 2024-10-17 14:04:11 UTC  
> Closed at: 2024-10-17 14:04:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1543  

Suppose a header declares:  
  
    namespace http = boost::beast::http;  
    void handle(http::request<http::string_body> const &);  
  
This doesn't require the types to be complete, and so forward declarations for them would suffice. Unfortunately, there doesn't appear to be a Beast header that contains such forward declarations. So in order to be able to declare `handle` above, one currently needs to:  
  
    #include <boost/beast/http/message.hpp>  
    #include <boost/beast/http/string_body.hpp>  
  
These two headers combined are almost a hundred thousand lines of code.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-03-24 22:51:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1543#issuecomment-476009406  

The correct file for this is `include/boost/beast/http/message_fwd.hpp`

---

## Comment 2

> Username: tcraigtyler  
> Created at: 2021-06-23 18:56:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1543#issuecomment-867081991  

>   
>   
> The correct file for this is `include/boost/beast/http/message_fwd.hpp`  
  
I don't find this file in the repository. Is there a replacement?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-06-23 19:00:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1543#issuecomment-867084220  

No

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2022-09-24 06:02:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1543#issuecomment-1256875532  

is this resolved?

---

## Comment 5

> Username: ashtum  
> Created at: 2024-10-17 14:04:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1543#issuecomment-2419645780  

Addressed in #2940.
