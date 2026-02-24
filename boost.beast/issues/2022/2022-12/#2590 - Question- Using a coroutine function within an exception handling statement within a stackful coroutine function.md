# #2590 - Question: Using a coroutine function within an exception handling statement within a stackful coroutine function [Closed]

> Username: lazychase  
> Created at: 2022-12-15 03:07:35 UTC  
> Updated at: 2023-10-28 16:34:53 UTC  
> Closed at: 2023-10-28 16:34:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2590  

- BOOST_BEAST_VERSION 330  
- MSVC 2022 CMake Project  
  
I am using stackful coroutines.  
  
```  
boost::asio::spawn(  
                    acceptor.get_executor(),  
                        std::bind(  
                            &do_session,  
                            boost::beast::tcp_stream(std::move(socket)),  
                            doc_root,  
                            std::placeholders::_1));  
```  
  
  
I put an invalid format (non-json string) into boost::json::parse() and threw an exception.  
  
```  
using namespace boost::json;  
  
	try {  
		value jsonObj = parse(body);  
		myHandler(); // It is not called now because of an exception.  
	}  
	catch (std::exception e) {  
     		send(bad_request(R"({"msg":"bad parameter"})"));  //  --> Crash when calling coroutine function.  
     		send(bad_request(R"({"msg":"bad parameter"})"), true);  //  --> This function is fine.  
	}  
```  
  
  
Inside the original send() a coroutine function is used. When I added the ability to use a synchronous function rather than a coroutine and used it, there was no crash.  
  
```  
template<bool isRequest, class Body, class Fields>  
        void  
            operator()(boost::beast::http::message<isRequest, Body, Fields>&& msg) const  
        {  
            // Determine if we should close the connection after  
            close_ = msg.need_eof();  
  
            // We need the serializer here because the serializer requires  
            // a non-const file_body, and the message oriented version of  
            // http::write only works with const messages.  
            boost::beast::http::serializer<isRequest, Body, Fields> sr{ msg };  
            boost::beast::http::async_write(stream_, sr, yield_[ec_]);  
        }  
  
        template<bool isRequest, class Body, class Fields>  
        void  
            operator()(boost::beast::http::message<isRequest, Body, Fields>&& msg, const bool syncParam) const  
        {  
            close_ = msg.need_eof();  
  
            boost::beast::http::serializer<isRequest, Body, Fields> sr{ msg };  
            // boost::beast::http::async_write(stream_, sr, yield_[ec_]);  
            boost::beast::http::write(stream_, sr, ec_);   // When I called this it was fine.  
        }  
  
```  
  
I have a feeling this may not be the right way to do it.  
  
I tried changing options in '/GL', '/EHsc' or 'EHs', but it didn't make a difference.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-12-15 03:10:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2590#issuecomment-1352500924  

Can you provide the definition of the `send` function?

---

## Comment 2

> Username: lazychase  
> Created at: 2022-12-15 03:14:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2590#issuecomment-1352503356  

> Can you provide the definition of the `send` function?  
  
send() used the code provided in the example.  
  
```  
// This is the C++11 equivalent of a generic lambda.  
// The function object is used to send an HTTP message.  
    struct send_lambda  
    {  
        boost::beast::tcp_stream& stream_;  
        bool& close_;  
        boost::beast::error_code& ec_;  
        boost::asio::yield_context yield_;  
  
        send_lambda(  
            boost::beast::tcp_stream& stream,  
            bool& close,  
            boost::beast::error_code& ec,  
            boost::asio::yield_context yield)  
            : stream_(stream)  
            , close_(close)  
            , ec_(ec)  
            , yield_(yield)  
        {  
        }  
  
        template<bool isRequest, class Body, class Fields>  
        void  
            operator()(boost::beast::http::message<isRequest, Body, Fields>&& msg) const  
        {  
            // Determine if we should close the connection after  
            close_ = msg.need_eof();  
  
            // We need the serializer here because the serializer requires  
            // a non-const file_body, and the message oriented version of  
            // http::write only works with const messages.  
            boost::beast::http::serializer<isRequest, Body, Fields> sr{ msg };  
            boost::beast::http::async_write(stream_, sr, yield_[ec_]);  
        }  
  
        template<bool isRequest, class Body, class Fields>  
        void  
            operator()(boost::beast::http::message<isRequest, Body, Fields>&& msg, const bool syncParam) const  
        {  
            // Determine if we should close the connection after  
            close_ = msg.need_eof();  
  
            // We need the serializer here because the serializer requires  
            // a non-const file_body, and the message oriented version of  
            // http::write only works with const messages.  
            boost::beast::http::serializer<isRequest, Body, Fields> sr{ msg };  
            boost::beast::http::write(stream_, sr, ec_);  
        }  
    };  
```

---

## Comment 3

> Username: lazychase  
> Created at: 2022-12-15 03:17:25 UTC  
> Updated at: 2022-12-15 03:18:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2590#issuecomment-1352505015  

The exception is thrown in parse() as when I didn't write a try~catch statement. The response is properly delivered to the client who sent the request.

---

## Comment 4

> Username: lazychase  
> Created at: 2022-12-15 03:40:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2590#issuecomment-1352515645  

When I tried running the same source on Ubuntu in a docker environment, the problem did not occur.  
  
Below are my options specific to msvc.  
```  
if(MSVC)  
    ADD_DEFINITIONS(/bigobj)  
    ADD_DEFINITIONS(-DCMAKE_TOOLCHAIN_FILE=C:/local/vcpkg/scripts/buildsystems/vcpkg.cmake)  
    SET(CMAKE_CXX_FLAGS                "/EHs ${CMAKE_CXX_FLAGS}")  
endif(MSVC)  
  
```

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2022-12-17 08:36:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2590#issuecomment-1356121563  

It's possible this is an MSVC issue, can you change the boost.context impl used to fibers? That will of course be much slower, but would help us figure out where the issues comes from.

---

## Comment 6

> Username: ashtum  
> Created at: 2023-08-18 13:26:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2590#issuecomment-1683920515  

@lazychase Is this still open?
