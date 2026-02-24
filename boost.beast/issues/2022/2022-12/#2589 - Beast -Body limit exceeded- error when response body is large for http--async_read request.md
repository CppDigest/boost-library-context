# #2589 - Beast "Body limit exceeded" error when response body is large for http::async_read request. [Closed]

> Username: SRGehani  
> Created at: 2022-12-14 15:35:37 UTC  
> Updated at: 2023-10-28 16:34:59 UTC  
> Closed at: 2023-10-28 16:34:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2589  

Beast Version:  
BOOST_BEAST_VERSION 290  
  
Steps necessary to reproduce the problem:  
When HTTP response is greater than 8 MB.  
  
  
I have a "body limit exceeded" error during http::async_read request.  
  
I have seen how to set body_limit using parser as mentioned [here](https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/ref/boost__beast__http__parser/body_limit.html), but could you suggest how can it be implemented with http::async_read.  
  
Class members:  
boost::beast::ssl_stream<boost::beast::tcp_stream> stream_;  
boost::beast::flat_buffer buffer_;  
std::shared_ptr< boost::beast::http::response<boost::beast::http::string_body>> resPtr_;  
  
Read function:  
    http::async_read(stream_, buffer_, *resPtr_,  
        beast::bind_front_handler(  
            &http_session::on_read,  
            shared_from_this()));

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-12-14 16:50:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2589#issuecomment-1351759725  

> could you suggest how can it be implemented with http::async_read.  
  
It cannot.

---

## Comment 2

> Username: GaneshTambat1  
> Created at: 2022-12-14 17:31:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2589#issuecomment-1351825648  

What would be the workaround in such a situation? Especially if moving to another model is not feasible and the rest of the code uses this model.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-12-15 18:31:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2589#issuecomment-1353535303  

Tell the server to send smaller bodies. But really, you need to refactor your code to pass the parser around. If this is an impossible task you have bigger problems.

---

## Comment 4

> Username: ashtum  
> Created at: 2023-08-18 13:23:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2589#issuecomment-1683916529  

@SRGehani Is this still open?
