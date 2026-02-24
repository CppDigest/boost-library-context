# #1220 - http::read failed due to requirement [Closed]

> Username: Ludea  
> Created at: 2018-08-09 20:34:01 UTC  
> Updated at: 2018-09-07 16:29:57 UTC  
> Closed at: 2018-09-07 16:29:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1220  

Hello,   
I follow your advanced server with flex and plain session.   
  
But, I have some issue with my code.   
I don't use CRTP.   
  
```  
http::async_read(  
            socket,  
            buffer,  
            req,  
            boost::asio::bind_executor(  
                strand,  
                std::bind(&http::session:on_read  
                ));  
```  
Is from http_session class.   
The http_session constructor is :   
```  
http_session::http_session(boost::asio::ioc, boost::beast::flat_buffer buffer, std::shared_ptr<std::string const> const& doc_root)  
: doc_root(doc_root)  
        , timer(ioc,  
            (std::chrono::steady_clock::time_point::max)())  
        , strand(ioc.get_executor())  
        , buffer(std::move(buffer))  
{  
}  
```  
  
I create the http_session like :  
```  
 std::make_shared<http_session>(  
        std::move(socket.get_executor().context())   
        std::move(buffer),  
        doc_root)->run();  
```  
  
It give me `static_assert failed due to  
      requirement 'is_async_read_stream<int  
      (int, int, int)>::value'  
      "AsyncReadStream requirements not  
      met"  
    static_assert(is_async_read_stream<AsyncReadStream>::value,`  
  
I don't inderstand what is wrong

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-08-10 01:06:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1220#issuecomment-411944211  

Do the unmodified examples compile for you?

---

## Comment 2

> Username: Ludea  
> Created at: 2018-08-10 07:46:01 UTC  
> Updated at: 2018-08-10 07:46:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1220#issuecomment-412004013  

Yes I can compil successfully the example   
  
Here my source https://source.team-doze.com/Doze_server/server  
  
I modified a lot the example  
The error appears in http_session, with http::read function  
I use the same function in detect_session, and it works
