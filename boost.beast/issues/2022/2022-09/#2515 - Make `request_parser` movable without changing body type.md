# #2515 - Make `request_parser` movable without changing body type [Closed]

> Username: xbreak  
> Created at: 2022-09-07 12:31:16 UTC  
> Updated at: 2022-09-28 08:00:35 UTC  
> Closed at: 2022-09-28 08:00:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2515  

Hi,  
  
**Version**  
boost 1.80/330  
  
**Question/Request**  
  
Is there a technical motivation why `request_parser` is not move-constructible without changing body type? If not, could this be allowed?  
  
**Motivation**  
  
I have a use-case where an initial HTTP session object reads header (`async_read_header`) using  
`request_parser<empty_body>` and then delegates the rest of the handling to another classes (such as reading body if there is any) . Here I was thinking of passing `request_parser` by value, but that is currently not possible.   
  
Since it's allowed to change body types before body parsing has begun, this (seemingly) can be worked around by needlessly changing body type:  
  
```c++  
#include <boost/beast/http.hpp>  
  
namespace http = boost::beast::http;  
  
void allowed() {  
    http::request_parser<http::empty_body> req0;  
    http::request_parser<http::string_body> req1{std::move(req0)};  
    http::request_parser<http::empty_body> req2{std::move(req1)};  
}  
  
void disallowed() {  
    http::request_parser<http::empty_body> req0;  
    http::request_parser<http::empty_body> req1{std::move(req0)}; // error: use of deleted function  
}  
```  
But moving without changing body type yields:  
```  
<source>: In function 'void disallowed()':  
<source>:13:64: error: use of deleted function 'boost::beast::http::parser<isRequest, Body, Fields>::parser(boost::beast::http::parser<isRequest, Body, Fields>&&) [with bool isRequest = true; Body = boost::beast::http::empty_body; Allocator = std::allocator<char>]'  
   13 |     http::request_parser<http::empty_body> req1{std::move(req0)}; // error: use of deleted function  
      |                                                                ^  
In file included from /opt/compiler-explorer/libs/boost_1_80_0/boost/beast/http.hpp:27,  
                 from <source>:1:  
/opt/compiler-explorer/libs/boost_1_80_0/boost/beast/http/parser.hpp:91:5: note: declared here  
   91 |     parser(parser&& other) = delete;  
      |     ^~~~~~  
Compiler returned: 1  
```  
https://godbolt.org/z/zEaqsqevc

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-09-07 14:54:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2515#issuecomment-1239500548  

Hmm... no idea. @vinniefalco ?

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2022-09-24 04:17:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2515#issuecomment-1256851732  

Sounds like it should be allowed to me/

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-09-25 00:38:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2515#issuecomment-1257091293  

you could always put it in a unique_ptr... I am concerned that playing with the move constructor will break people who are using it to transform the body type. If you are 112% certain you can make it movable without breaking anything, then ok.

---

## Comment 4

> Username: xbreak  
> Created at: 2022-09-28 08:00:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2515#issuecomment-1260531950  

Thanks, yes there are various options.  
  
This is not critical thing to us, but I thought the behavior was a bit... odd. In the interest of not keeping low priority things open indefinitely I'll simply close it.
