# #1300 - Need to find where BOOST_BEAST_HANDLER_INIT is #defined [Closed]

> Username: DragonOsman  
> Created at: 2018-11-13 19:50:22 UTC  
> Updated at: 2018-11-13 20:00:31 UTC  
> Closed at: 2018-11-13 20:00:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1300  

While trying to compile [this code](https://github.com/DragonOsman/currency_converter/blob/master/currency_converter_async.cpp), I had this error:   
```  
1>c:\boost_1_68_0\boost\beast\http\impl\read.ipp(837,1): error C2338:  CompletionHandler signature requirements not met  
```  
I need to know where the `#define` for `BOOST_BEAST_HANDLER_INIT` is, since the error indicating this line of code: `BOOST_BEAST_HANDLER_INIT(` in this function in `\boost\beast\http\impl\read.ipp`:  
```cpp  
template<  
    class AsyncReadStream,  
    class DynamicBuffer,  
    bool isRequest, class Body, class Allocator,  
    class ReadHandler>  
BOOST_ASIO_INITFN_RESULT_TYPE(  
    ReadHandler, void(error_code, std::size_t))  
async_read(  
    AsyncReadStream& stream,  
    DynamicBuffer& buffer,  
    message<isRequest, Body, basic_fields<Allocator>>& msg,  
    ReadHandler&& handler)  
{  
    static_assert(is_async_read_stream<AsyncReadStream>::value,  
        "AsyncReadStream requirements not met");  
    static_assert(  
        boost::asio::is_dynamic_buffer<DynamicBuffer>::value,  
        "DynamicBuffer requirements not met");  
    static_assert(is_body<Body>::value,  
        "Body requirements not met");  
    static_assert(is_body_reader<Body>::value,  
        "BodyReader requirements not met");  
    BOOST_BEAST_HANDLER_INIT(  
        ReadHandler, void(error_code, std::size_t));  
    detail::read_msg_op<  
        AsyncReadStream,  
        DynamicBuffer,  
        isRequest, Body, Allocator,  
        BOOST_ASIO_HANDLER_TYPE(  
            ReadHandler, void(error_code, std::size_t))>{  
                std::move(init.completion_handler), stream, buffer, msg}(  
                    {}, 0, false);  
    return init.result.get();  
}  
```  
I'm using the version of Beast included in Boost version 1.68.0, by the way.

---

## Comment 1

> Username: DragonOsman  
> Created at: 2018-11-13 20:00:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1300#issuecomment-438415679  

The reason was I'd added an extra parameter to `session::on_read` in the client code, which I shouldn't have done.  I'll close this now.
