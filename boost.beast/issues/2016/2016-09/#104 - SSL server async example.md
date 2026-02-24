# #104 - SSL server async example [Closed]

> Username: goflatworld  
> Created at: 2016-09-30 12:13:12 UTC  
> Updated at: 2016-10-10 11:08:43 UTC  
> Closed at: 2016-10-10 11:08:43 UTC  
> Url: https://github.com/boostorg/beast/issues/104  

Hi Vinnie,  
  
The Boost ASIO chat Async example is designed very well. To add SSL to it, I simply modify the chat code by adding the SSL handshake part into the code. The rest of the code stay as is. It is a fairly scalable yet simple solution.   
  
Can I do the similar thing with Beast Websocket SSL? e.g. after I add SSL into the chat application. Can I add beast websocket SSL on top of it and the rest of the code stay as is?  If so, can you please show us how to do it?  If not, can you please also come up a Boost Chat Async style example?  
  
Thanks  
  
George

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-09-30 12:15:21 UTC  
> Updated at: 2016-09-30 15:02:13 UTC  
> Url: https://github.com/boostorg/beast/issues/104#issuecomment-250729789  

Yes. Its pretty easy to change the Beast websocket echo server to use SSL, just replace `boost::asio::ip::tcp::socket` with `boost::asio::ssl::stream<boost::asio::ip::tcp::socket>` and add the necessary calls to `boost::asio::ssl::stream::handshake` (synchronous) or `boost::asio::ssl::stream::async_handshake` (asynchronous).  
  
I will try to add an example. But even without such an example it should be very easy for you to convert the server to use SSL if you are familiar with Asio.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2016-09-30 15:02:20 UTC  
> Url: https://github.com/boostorg/beast/issues/104#issuecomment-250767871  

Please let me know if you get that working!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-09-30 21:24:56 UTC  
> Url: https://github.com/boostorg/beast/issues/104#issuecomment-250855941  

I want to do this but I can't figure out how to get bjam to set the include paths for OpenSSL - any suggestions?

---

## Comment 4

> Username: goflatworld  
> Created at: 2016-09-30 21:34:24 UTC  
> Url: https://github.com/boostorg/beast/issues/104#issuecomment-250857755  

Thanks Vinnie for the follow up. I'm trying to modify my existing code to use beast websocket. That might take some time (plus I'm going to have 10 days holiday). I'll update this post once I got it working.  
  
Regarding OpenSSL, I use Ubuntu, so I used apt-get to install the development package. After that , I just used a simple command line to compile my code. "g+++ -l ssl -l crypto" would link OpenSSL.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2016-09-30 22:31:31 UTC  
> Updated at: 2016-09-30 22:31:50 UTC  
> Url: https://github.com/boostorg/beast/issues/104#issuecomment-250867319  

Yeah, `g++ -l ssl -l crypto` works great if you're using g++ on Linux but I need to figure out how to make it work using bjam on all platforms...I've asked the question here:  
http://stackoverflow.com/questions/39800333/how-do-i-get-bjam-to-set-include-paths-for-openssl-headers-and-libraries-on-msvc?noredirect=1#comment66894110_39800333

---

## Comment 6

> Username: goflatworld  
> Created at: 2016-10-01 21:11:42 UTC  
> Updated at: 2016-10-01 21:12:18 UTC  
> Url: https://github.com/boostorg/beast/issues/104#issuecomment-250937899  

My code needs handle normal SSL socket as well as Web SSL socket. It works fine with normal SSL socket. It also works with another Websocket library however that websocket library is not stable so I'm looking into this one. After some reading, my idea is that get beast http to read the first packet, if it is websocket upgrade, use Websocket to handle it, if it is normal socket, handle it normally.  
  
I have modified my code and some relevant code are as below, but it failed to compile, can you please let me know what am I doing wrong?  
  
#   
  
#include <beast/websocket/ssl.hpp>  
#include <beast/websocket.hpp>  
#include <beast/core/placeholders.hpp>  
#include <beast/core/streambuf.hpp>  
#include <beast/http.hpp>  
#include <beast/http/empty_body.hpp>  
  
```  
    typedef boost::asio::ssl::stream<boost::asio::ip::tcp::socket> ssl_socket;  
```  
  
boost::asio::streambuf buff_;  
beast::http::requestbeast::http::empty_body request_;  
  
session::session(boost::asio::io_service& io_service,   
                boost::asio::ssl::context& context, channel& room)  
                : socket_(io_service,context),   
            {...SSL handshake...}  
  
```  
     beast::http::async_read(socket_, buff_, request_,  
                    strand_.wrap(  
                    std::bind(&session::http_handle_read, shared_from_this(),  
                    boost::asio::placeholders::error)));  
```  
  
  bool result=beast::http::is_upgrade(request_);  
  
#   
  
session.cpp: In member function ‘void websocket::applications::chat::session::do_read_header(int)’:  
session.cpp:96:59: error: no matching function for call to ‘async_read(websocket::applications::chat::ssl_socket&, boost::asio::streambuf&, beast::http::requestbeast::http::empty_body&, boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, std::_Bind<std::_Mem_fn<void (websocket::applications::chat::session::*)(const boost::system::error_code&)>(std::shared_ptrwebsocket::applications::chat::session, boost::arg<1> (*)())>, boost::asio::detail::is_continuation_if_running>)’  
                         boost::asio::placeholders::error)));  
                                                           ^  
In file included from /usr/include/beast/http/read.hpp:267:0,  
                 from /usr/include/beast/websocket/impl/accept_op.ipp:14,  
                 from /usr/include/beast/websocket/impl/stream.ipp:13,  
                 from /usr/include/beast/websocket/stream.hpp:1527,  
                 from /usr/include/beast/websocket.hpp:14,  
                 from session.hpp:8,  
                 from session.cpp:1:  
/usr/include/beast/http/impl/read.ipp:446:1: note: candidate: template<class AsyncReadStream, class DynamicBuffer, bool isRequest, class Body, class Headers, class ReadHandler> typename beast::async_completion<ReadHandler, void(boost::system::error_code)>::result_type beast::http::async_read(AsyncReadStream&, DynamicBuffer&, beast::http::message_v1<isRequest, Body, Headers>&, ReadHandler&&)  
 async_read(AsyncReadStream& stream, DynamicBuffer& dynabuf,  
 ^  
/usr/include/beast/http/impl/read.ipp:446:1: note:   template argument deduction/substitution failed:  
session.cpp:96:59: note:   ‘beast::http::requestbeast::http::empty_body {aka beast::http::message<true, beast::http::empty_body, beast::http::basic_headers<std::allocator<char> > >}’ is not derived from ‘beast::http::message_v1<isRequest, Body, Headers>’  
                         boost::asio::placeholders::error)));  
                                                           ^  
session.cpp: In member function ‘void websocket::applications::chat::session::http_handle_read(const boost::system::error_code&)’:  
session.cpp:130:69: error: no matching function for call to ‘is_upgrade(beast::http::requestbeast::http::empty_body&)’  
                         bool result=beast::http::is_upgrade(request_);  
                                                                     ^  
In file included from /usr/include/beast/http/message_v1.hpp:133:0,  
                 from /usr/include/beast/websocket/stream.hpp:13,  
                 from /usr/include/beast/websocket.hpp:14,  
                 from session.hpp:8,  
                 from session.cpp:1:  
/usr/include/beast/http/impl/message_v1.ipp:37:1: note: candidate: template<bool isRequest, class Body, class Headers> bool beast::http::is_upgrade(const beast::http::message_v1<isRequest, Body, Headers>&)  
 is_upgrade(message_v1<isRequest, Body, Headers> const& msg)  
 ^  
/usr/include/beast/http/impl/message_v1.ipp:37:1: note:   template argument deduction/substitution failed:  
session.cpp:130:69: note:   ‘beast::http::requestbeast::http::empty_body {aka beast::http::message<true, beast::http::empty_body, beast::http::basic_headers<std::allocator<char> > >}’ is not derived from ‘const beast::http::message_v1<isRequest, Body, Headers>’  
                         bool result=beast::http::is_upgrade(request_);

---

## Comment 7

> Username: vinniefalco  
> Created at: 2016-10-01 22:56:15 UTC  
> Url: https://github.com/boostorg/beast/issues/104#issuecomment-250942608  

Yes, what you're trying to do should work. Note, that we are doing the exact same functionality in our own server application:  
https://github.com/vinniefalco/rippled/blob/develop/src/ripple/rpc/impl/ServerHandlerImp.cpp#L188  
  
As for your compile errors, can you please give me a link to your GitHub repository? Its hard to read your message because GitHub has removed the necessary punctuation from the error messages.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2016-10-01 22:57:38 UTC  
> Url: https://github.com/boostorg/beast/issues/104#issuecomment-250942668  

I think I might see a problem - you are declaring `beast::http::request` when you should be using `beast::http::request_v1`. The difference is that `request_v1` is used for HTTP/1.0 and HTTP/1.1 messages. See:  
http://vinniefalco.github.io/beast/beast/http/message.html  
  
Try changing your declaration to `request_v1` and `response_v1` instead, and see if the errors go away. Good luck!

---

## Comment 9

> Username: goflatworld  
> Created at: 2016-10-02 03:38:33 UTC  
> Url: https://github.com/boostorg/beast/issues/104#issuecomment-250951724  

Thanks. I have changed it to request_v1. The original compile error is gone, now it came up another error as below for the same function.   The function is async_read as below:  
  
beast::http::async_read(socket_, buff_, request_,  
               strand_.wrap(  
                        std::bind(&session::http_handle_read, shared_from_this(),  
                        boost::asio::placeholders::error)));  
  
The error message is as below  
  
In file included from /usr/include/beast/http/read.hpp:267:0,  
                 from /usr/include/beast/websocket/impl/accept_op.ipp:14,  
                 from /usr/include/beast/websocket/impl/stream.ipp:13,  
                 from /usr/include/beast/websocket/stream.hpp:1527,  
                 from /usr/include/beast/websocket.hpp:14,  
                 from session.hpp:8,  
                 from session.cpp:1:  
/usr/include/beast/http/impl/read.ipp: In instantiation of ‘typename beast::async_completion<ReadHandler, void(boost::system::error_code)>::result_type beast::http::async_read(AsyncReadStream&, DynamicBuffer&, beast::http::message_v1<isRequest, Body, Headers>&, ReadHandler&&) [with AsyncReadStream = boost::asio::ssl::streamboost::asio::basic_stream_socket<boost::asio::ip::tcp >; DynamicBuffer = boost::asio::basic_streambuf<>; bool isRequest = true; Body = beast::http::empty_body; Headers = beast::http::basic_headersstd::allocator<char >; ReadHandler = boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, std::_Bind<std::_Mem_fn<void (websocket::applications::chat::session::*)(const boost::system::error_code&)>(std::shared_ptrwebsocket::applications::chat::session, boost::arg<1> (*)())>, boost::asio::detail::is_continuation_if_running>; typename beast::async_completion<ReadHandler, void(boost::system::error_code)>::result_type = void]’:  
session.cpp:95:59:   required from here  
/usr/include/beast/http/impl/read.ipp:454:5: error: static assertion failed: ReadableBody requirements not met  
     static_assert(is_ReadableBody<Body>::value,

---

## Comment 10

> Username: vinniefalco  
> Created at: 2016-10-02 05:20:44 UTC  
> Updated at: 2016-10-02 05:21:24 UTC  
> Url: https://github.com/boostorg/beast/issues/104#issuecomment-250954431  

![image](https://cloud.githubusercontent.com/assets/1503976/19018867/82163f06-883e-11e6-9427-70130e61def8.png)  
  
Anyway, it looks like you're declaring the request object with an empty body:  
  
```  
beast::http::request<beast::http::empty_body> request_;  
```  
  
`empty_body` does not meet the requirements of "ReadableBody" - its only writable. To read a request, you need to use a body that can store data. Try declaring your request thusly:  
  
```  
beast::http::request<beast::http::string_body> request_;  
```

---

## Comment 11

> Username: goflatworld  
> Created at: 2016-10-02 07:38:40 UTC  
> Url: https://github.com/boostorg/beast/issues/104#issuecomment-250958520  

Thanks Vinnie,  
  
I tried it and it didn't work. I don't have my project on github. I have made a reduced version of my code and attached it. Hope you can help compile it.  
  
This .gz file include 6 files. Three .cpp files, two .hpp files and a compile script. If you unzip them to Ubuntu Linux and run ./compile. It should fail. If you open session.cpp and comment out below code. You should be able to compile it. Can you please help?  
.................  
  
```  
 beast::http::async_read(socket_, buff_, request_,  
                    strand_.wrap(  
                    std::bind(&session::http_handle_read, shared_from_this(),  
                    boost::asio::placeholders::error)));  
```  
  
---  
  
Also let you know that I copied the initial beast code from below link. I copied the last section (understand it is sync code but I thought I should be able to use it for async code).  
  
http://vinniefalco.github.io/beast/beast/websocket/handshaking.html  
  
[test.tar.gz](https://github.com/vinniefalco/Beast/files/504819/test.tar.gz)

---

## Comment 12

> Username: vinniefalco  
> Created at: 2016-10-02 12:03:27 UTC  
> Url: https://github.com/boostorg/beast/issues/104#issuecomment-250967966  

In session.cpp line 50 you are mixing `std::bind` and `boost::asio::placeholders`. You should use `boost::bind` instead of `std::bind`.

---

## Comment 13

> Username: goflatworld  
> Created at: 2016-10-02 18:04:11 UTC  
> Url: https://github.com/boostorg/beast/issues/104#issuecomment-250985314  

Great. That worked! Thanks a lot!  
  
I'll let you know the SSL server testing result once I got time.

---

## Comment 14

> Username: goflatworld  
> Created at: 2016-10-02 21:21:37 UTC  
> Updated at: 2016-10-02 21:23:33 UTC  
> Url: https://github.com/boostorg/beast/issues/104#issuecomment-250996932  

Not sure how to finish the websocket handsahke. Have read the examples but did not really get how it is done... Can you please help to finish/correct the code? My understanding is that once handshake is finished, I can use the websocket client side example code to read/write from the socket.   
  
...  
  
```  
        void session::http_handle_read(const boost::system::error_code& error)  
        {  
            if (!error)  
            {  
                    bool result=beast::http::is_upgrade(request_);  
                    if (result)  
                    {  
                         std::cout<<"HTTP websocket request comes in."<<std::endl;  
                         wssocket_.async_accept(request_,  
                            strand_.wrap(  
                            boost::bind(&session::http_do_read, shared_from_this())));  
                    }   
                    else   //If we didn't finish read, read more.  
                    {  
                         beast::http::async_read(socket_, buff_, request_,  
                            strand_.wrap(  
                            boost::bind(&session::http_handle_read, shared_from_this(),  
                            boost::asio::placeholders::error)));  
                    }  
            }  
   }  
  
        void session::http_do_read()  
        {  
            std::cout<<"Accepted HTTP connection, should write to it"<<std::endl;  
  
            beast::http::response_v1<beast::http::string_body> response_;  
            wssocket_.write(response_);  
  
            wssocket_.read(op_,buff_);  
            wssocket_.set_option(beast::websocket::message_type{op_});  
  
        std::istream data(&buff_);  
            std::string message;  
            std::getline(data, message);  
  
          //code to handle the data.                
          }  
```  
  
...

---

## Comment 15

> Username: vinniefalco  
> Created at: 2016-10-02 21:47:34 UTC  
> Url: https://github.com/boostorg/beast/issues/104#issuecomment-250998448  

There's a complete example WebSocket echo server here:  
https://github.com/vinniefalco/Beast/blob/master/test/websocket/websocket_async_echo_server.hpp#L25  
  
It looks like you're using `beast::http` to process the WebSocket handshake, that is not necessary. Beast WebSocket will handle the handshake for you, just call `beast::websocket::stream::async_accept` after you have established your SSL stream connection. See:  
http://vinniefalco.github.io/beast/beast/ref/websocket__stream/async_accept/overload1.html  
  
Is this what you want to do? Or is there a reason why you want to handle the HTTP request yourself? For example, to do some pre-processing, or to handle regular HTTP requests)?

---

## Comment 16

> Username: goflatworld  
> Created at: 2016-10-02 22:41:27 UTC  
> Url: https://github.com/boostorg/beast/issues/104#issuecomment-251001290  

Thanks.  
  
I have read the async_echo_server code and also followed below link to modify my code.  
  
http://vinniefalco.github.io/beast/beast/websocket/handshaking.html  
  
...  
    //normal read  
    boost::asio::async_read_until(socket_, buff_, "\r\n\r\n",  
                                        boost::bind(&session::http_handle_read,shared_from_this(),  
                                            boost::asio::placeholders::error));  
     //SSL socket accept   
     wssocket_.async_accept(buff_.data(),  
                                strand_.wrap(  
                                boost::bind(&session::http_do_read, shared_from_this(),  
                                boost::asio::placeholders::error)));  
  
```  
 void session::http_do_read(const boost::system::error_code& error)  
 {  
      if (!error)  
      { } else  
                std::cout<<error.message()<<std::endl;  
 }  
```  
  
...  
  
It appears that accept got error, and the error message is "uninitialized". Any idea?

---

## Comment 17

> Username: vinniefalco  
> Created at: 2016-10-02 22:49:44 UTC  
> Url: https://github.com/boostorg/beast/issues/104#issuecomment-251001724  

I don't understand what you're trying to do here, and the formatting makes the code hard to read. Please use the triple backquotes, as explained here:  
http://meta.stackexchange.com/questions/189920/what-is-the-3-x-backquote-markdown-used-for  
  
Its difficult for me to understand the problem with your code, when I cannot see all of it. You should create a repository and provide a link.

---

## Comment 18

> Username: vinniefalco  
> Created at: 2016-10-10 11:08:43 UTC  
> Url: https://github.com/boostorg/beast/issues/104#issuecomment-252588935  

Merged to b15
