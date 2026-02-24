# #1252 - Compile-time error std::enable_if<! is_mutable_body_writer<Body>::value, BOOST_ASIO_INITFN_RESULT_TYPE(...)>::type is missing [Closed]

> Username: 0xdead4ead  
> Created at: 2018-09-20 18:28:08 UTC  
> Updated at: 2018-11-20 10:47:59 UTC  
> Closed at: 2018-11-20 10:47:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1252  

BOOST_BEAST_VERSION 181  
  
There are two calls:  
```cpp  
template<  
    class AsyncWriteStream,  
    bool isRequest, class Body, class Fields,  
    class WriteHandler>  
typename std::enable_if<  
    is_mutable_body_writer<Body>::value,  
    BOOST_ASIO_INITFN_RESULT_TYPE(  
        WriteHandler, void(error_code, std::size_t))>::type  
async_write(  
    AsyncWriteStream& stream,  
    message<isRequest, Body, Fields>& msg,  
    WriteHandler&& handler);  
// and  
template<  
    class AsyncWriteStream,  
    bool isRequest, class Body, class Fields,  
    class WriteHandler>  
typename std::enable_if<  
       ! is_mutable_body_writer<Body>::value,  
    BOOST_ASIO_INITFN_RESULT_TYPE(  
        WriteHandler, void(error_code, std::size_t))>::type  
async_write(  
    AsyncWriteStream& stream,  
    message<isRequest, Body, Fields> const& msg,  
    WriteHandler&& handler);  
```  
On a second call, I get compile error, when:   
`Body = boost::beast::http::basic_file_body<boost::beast::file_posix>`  
Is it the way it should be or am I not understanding something?  
  
Here is the link!  
https://github.com/0xdead4ead/beast_http_server/blob/2f6b97737869c1ca056de9a2c3f70cd9a11d12f3/include/base.hpp#L95

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-09-20 19:49:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1252#issuecomment-423310231  

What do you mean "on a second call?"  
  
File bodies cannot be passed in const messages, because the serialization of the file changes the file's current position, which is a "logically non-const" operation. I agree this is confusing, and I will be changing it in a future version once I figure out the best way to make it work better (in the new version, messages will always be passed as const).

---

## Comment 2

> Username: 0xdead4ead  
> Created at: 2018-09-21 08:07:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1252#issuecomment-423450541  

Thanks for the support. I was too lazy to seek an answer to this question!

---

## Comment 3

> Username: stale[bot]  
> Created at: 2018-11-20 08:25:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1252#issuecomment-440184920  

This issue has been open for a while with no activity, has it been resolved?
