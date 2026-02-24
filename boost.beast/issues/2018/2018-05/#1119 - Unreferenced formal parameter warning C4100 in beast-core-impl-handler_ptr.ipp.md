# #1119 - Unreferenced formal parameter warning C4100 in beast/core/impl/handler_ptr.ipp [Closed]

> Username: badfd  
> Created at: 2018-05-07 22:55:51 UTC  
> Updated at: 2018-05-19 02:30:50 UTC  
> Closed at: 2018-05-19 02:30:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1119  

Beast-1.0.0-169  
  
Compiling with MSVC (19.00.24215.1 for x64), I  get an `unreferenced formal parameter` warning in `handler_ptr.ipp`:  
```  
boost/beast/core/impl/handler_ptr.ipp(118): warning C4100: 'h': unreferenced formal parameter  
```  
The code in `handler_ptr.ipp` is:  
```  
107 template<class T, class Handler>  
108 template<class... Args>  
109 void  
110 handler_ptr<T, Handler>::  
111 invoke(Args&&... args)  
112 {  
113     BOOST_ASSERT(t_);  
114     clear();  
115     auto deleter = [](Handler* h)  
116     {  
117         h->~Handler();  
118     }; // **************** warning C4100: 'h' unreferenced formal parameter  
119     std::unique_ptr<  
120         Handler, decltype(deleter)> destroyer{  
121             &handler(), deleter};  
122     handler()(std::forward<Args>(args)...);  
123 }  
```  
where it seems evident that 'h', in fact, is referenced.  
The corresponding code (that invokes the above warning) is:  
```  
    boost::system::error_code ec = boost::asio::error::would_block;  
    std::size_t bytes_transferred = 0;  
    http::async_write(socket, request,  
    [&](const boost::system::error_code lambda_ec, const std::size_t lambda_bytes_transferred)  
    {  
        ec = lambda_ec;  
        bytes_transferred = lambda_bytes_transferred;  
    }); // warning C4100  
```  
No issues on Linux with GCC 4.8.5.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-05-07 23:02:54 UTC  
> Updated at: 2018-05-07 23:05:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1119#issuecomment-387232863  

Please try this possible work-around:  
```  
template<class T, class Handler>  
template<class... Args>  
void  
handler_ptr<T, Handler>::  
invoke(Args&&... args)  
{  
    BOOST_ASSERT(t_);  
    clear();  
    struct deleter  
    {  
        void  
        operator()(Handler* h) const  
        {  
            h->~Handler();  
        }  
    };  
    std::unique_ptr<  
        Handler, deleter> destroyer{  
            &handler(), deleter{}};  
    handler()(std::forward<Args>(args)...);  
}  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-05-10 01:19:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1119#issuecomment-387921617  

Did this help?

---

## Comment 3

> Username: badfd  
> Created at: 2018-05-10 20:42:24 UTC  
> Updated at: 2018-05-10 21:01:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1119#issuecomment-388179327  

No, exactly the same warning on the same line (118). (Sorry for the delay in verifying.)  
  
The following does compile without warning, albeit at the expense of a conditional:  
```  
template<class T, class Handler>  
template<class... Args>  
void  
handler_ptr<T, Handler>::  
invoke(Args&&... args)  
{  
    BOOST_ASSERT(t_);  
    clear();  
    struct deleter  
    {  
        void  
        operator()(Handler* h) const  
        {  
            if (h) h->~Handler();  
        }  
    };  
    std::unique_ptr<  
        Handler, deleter> destroyer{  
            &handler(), deleter{}};  
    handler()(std::forward<Args>(args)...);  
}  
```

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-05-10 21:46:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1119#issuecomment-388196207  

How about this then:  
```  
template<class T, class Handler>  
template<class... Args>  
void  
handler_ptr<T, Handler>::  
invoke(Args&&... args)  
{  
    BOOST_ASSERT(t_);  
    clear();  
    struct deleter  
    {  
        void  
        operator()(Handler* h) const  
        {  
            boost::ignore_unused(h);  
            h->~Handler();  
        }  
    };  
    std::unique_ptr<  
        Handler, deleter> destroyer{  
            &handler(), deleter{}};  
    handler()(std::forward<Args>(args)...);  
}  
```

---

## Comment 5

> Username: badfd  
> Created at: 2018-05-10 21:51:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1119#issuecomment-388197276  

Yep. That worked. :-)

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-05-10 21:58:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1119#issuecomment-388198863  

I guess I should have opened with that :)
