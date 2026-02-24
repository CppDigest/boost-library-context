# #199 - Make parse operations independent from boost.asio for use in microcontrollers [Closed]

> Username: felipealmeida  
> Created at: 2016-11-24 14:30:32 UTC  
> Updated at: 2017-06-08 05:30:22 UTC  
> Closed at: 2017-06-08 05:30:22 UTC  
> Url: https://github.com/boostorg/beast/issues/199  

beast/http/impl/parse.ipp and beast/http/stream_concepts.hpp depend on asio types, which makes it impossible to use the parsing and generation of HTTP/WS messages without #include'ing boost.asio, which can't compile on microcontrollers on bare metal or RTOS.

---

## Comment 1

> Username: felipealmeida  
> Created at: 2016-11-24 15:01:31 UTC  
> Updated at: 2016-11-24 15:02:22 UTC  
> Url: https://github.com/boostorg/beast/issues/199#issuecomment-262795258  

I have some patches. But involves patching asio too, to separate misc_errors from other network related errors. And involves using std <system_error> instead of boost.system, because boost.system fails to compile since it relies on POSIX headers and preprocessor symbols

---

## Comment 2

> Username: vinniefalco  
> Created at: 2016-11-24 16:01:39 UTC  
> Url: https://github.com/boostorg/beast/issues/199#issuecomment-262807381  

Sounds like its a much bigger job than just minor changes in `basic_parser_v1.hpp`. I would like to address this, but it would have to happen after the Boost formal review when I am less busy.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-04-28 01:38:11 UTC  
> Url: https://github.com/boostorg/beast/issues/199#issuecomment-297884180  

You have to at least have `<boost/asio/buffer.hpp>` can you see if that compiles?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-06-08 05:30:22 UTC  
> Url: https://github.com/boostorg/beast/issues/199#issuecomment-307002675  

@felipealmeida Were you ever able to find out if boost/asio/buffer.hpp compiles for you? If you do, please re-open this issue!
