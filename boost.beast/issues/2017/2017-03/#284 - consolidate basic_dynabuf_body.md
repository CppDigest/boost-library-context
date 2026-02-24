# #284 - consolidate basic_dynabuf_body [Closed]

> Username: vinniefalco  
> Created at: 2017-03-25 14:10:59 UTC  
> Updated at: 2017-05-08 00:04:00 UTC  
> Closed at: 2017-05-08 00:04:00 UTC  
> Url: https://github.com/boostorg/beast/issues/284  

There's no need to have streambuf_body.hpp with just one declaration in it, we should move the contents of basic_dynabuf_body into streambuf_body.hpp, and rename it to `dynamic_buffer_body`. Or maybe `dynamic_body`.
