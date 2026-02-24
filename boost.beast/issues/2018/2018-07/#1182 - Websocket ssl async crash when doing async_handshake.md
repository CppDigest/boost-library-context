# #1182 - Websocket ssl async crash when doing async_handshake [Closed]

> Username: leogi222  
> Created at: 2018-07-09 23:29:00 UTC  
> Updated at: 2018-09-20 16:50:39 UTC  
> Closed at: 2018-09-20 16:50:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1182  

I am using the async ssl code to connect. The synchronous code at:  
  
https://www.boost.org/doc/libs/develop/libs/beast/example/websocket/client/sync-ssl/websocket_client_sync_ssl.cpp  
  
works fine  and each time I get a welcome message back from the server.  
  
However the asynchronous code at   
https://www.boost.org/doc/libs/master/libs/beast/example/websocket/client/async-ssl/websocket_client_async_ssl.cpp  
  
always cores, and the gdb doesn’t seem to produce much easy to understand trace.   
  
I am a bit running out of ideas. What I would like to accomplish is connect to the server and get data on the callback, I assume it’s the   
  
void  
        on_read(  
            boost::system::error_code ec,  
            std::size_t bytes_transferred)  
        {  
  
…/…  
  
}  
  
  
Function right?  
  
Based on what I’ve read it seems I need to restart the io_context and keep on calling the read function to be ready for the next data to be received?  
  
Maybe I could just synchronously connect but then how would the callback get registered to be called each time new data arrives?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-07-09 23:44:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1182#issuecomment-403653842  

> However the asynchronous code at https://www.boost.org/doc/libs/master/libs/beast/example/websocket/client/async-ssl/websocket_client_async_ssl.cpp always cores,  
  
Which version of the example source code? Which version of Boost? Have you made modifications? What command-line arguments are you passing?

---

## Comment 2

> Username: leogi222  
> Created at: 2018-07-10 14:47:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1182#issuecomment-403849474  

boost_1_67_0  
  
I am using the example located at the link I sent you:  
  
//  
// Copyright (c) 2016-2017 Vinnie Falco (vinnie dot falco at gmail dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
// Official repository: https://github.com/boostorg/beast  
//  
  
  
The modifications are as follows:  
  
Instead of passing arguments to a standalone program, I created a function from within our environment that calls the code:  
  
  
void connect_to_bitmex(void){  
  
        auto const host = "www.bitmex.com";  
        auto const port = "443";  
        auto const text = "help";  
  
        // The io_context is required for all I/O  
        boost::asio::io_context ioc;  
  
        // The SSL context is required, and holds certificates  
        ssl::context ctx{ssl::context::sslv23_client};  
  
        // This holds the root certificate used for verification  
        //load_root_certificates(ctx);  
  
        // Launch the asynchronous operation  
        std::make_shared<session>(ioc, ctx)->run(host, port, text);  
  
        // Run the I/O service. The call will return when  
        // the socket is closed.  
        ioc.run();  
  
    }  
  
Only difference is I do not load cerificates, because my understanding is we are acting as a client and all we want to do is access the server public data.  
  
So this code works fine:  
  
(SYNCHRONEOUS CALLS)  
  
void connect_to_bitmex(void){  
        std::string const host = "www.bitmex.com";  
        auto const port = "443";  
        auto const text = "help";  
  
       // The io_context is required for all I/O  
       boost::asio::io_context ioc;  
  
       // The SSL context is required, and holds certificates  
       ssl::context ctx{ssl::context::sslv23_client};  
  
       // This holds the root certificate used for verification  
       //load_root_certificates(ctx);  
  
       // These objects perform our I/O  
       tcp::resolver resolver{ioc};  
       websocket::stream<ssl::stream<tcp::socket>> ws{ioc, ctx};  
  
       // Look up the domain name  
       auto const results = resolver.resolve(host, port);  
  
       // Make the connection on the IP address we get from a lookup  
       boost::asio::connect(ws.next_layer().next_layer(), results.begin(), results.end());  
  
       // Perform the SSL handshake  
       ws.next_layer().handshake(ssl::stream_base::client);  
  
       // Perform the websocket handshake  
       ws.handshake(host, "/realtime?subscribe=instrument.orderBook:XBTUSD");  
  
       // Send the message  
       ws.write(boost::asio::buffer(std::string(text)));  
  
       // This buffer will hold the incoming message  
       boost::beast::multi_buffer b;  
  
       // Read a message into our buffer  
       ws.read(b);  
  
SUCCESS:  
  
       //<<...........  {"info":"Welcome to the BitMEX Realtime API.","version":"2018-06-29T18:05:14.000Z","timestamp":"2018-07-09T18:37:52.798Z","docs":"https://www.bitmex.com/app/wsAPI","limit":{"remaining":39}}  
  
       // Close the WebSocket connection  
       ws.close(websocket::close_code::normal);  
  
       // If we get here then the connection is closed gracefully  
  
       // The buffers() function helps print a ConstBufferSequence  
       std::cout << boost::beast::buffers(b.data()) << std::endl;  
    }  
  
While the first code snippet breaks at the following location:  
  
void  
    fail(boost::system::error_code ec, char const* what)  
    {  
        std::cerr << what << ": " << ec.message() << "\n";  
    }  
  
    // Sends a WebSocket message and prints the response  
    class session : public std::enable_shared_from_this<session>  
    {  
        tcp::resolver resolver_;  
        websocket::stream<ssl::stream<tcp::socket>> ws_;  
        boost::beast::multi_buffer buffer_;  
        std::string host_;  
        std::string text_;  
  
    public:  
        // Resolver and socket require an io_context  
        explicit  
        session(boost::asio::io_context& ioc, ssl::context& ctx)  
            : resolver_(ioc)  
            , ws_(ioc, ctx)  
        {  
        }  
  
        // Start the asynchronous operation  
        void  
        run(  
            char const* host,  
            char const* port,  
            char const* text)  
        {  
            // Save these for later  
            host_ = host;  
            text_ = text;  
  
            err_cont("%s called host: %s port %s text %s",__func__,host,port,text);  
  
            // Look up the domain name  
            resolver_.async_resolve(  
                host,  
                port,  
                std::bind(  
                    &session::on_resolve,  
                    shared_from_this(),  
                    std::placeholders::_1,  
                    std::placeholders::_2));  
        }  
  
        void  
        on_resolve(  
            boost::system::error_code ec,  
            tcp::resolver::results_type results)  
        {  
            err_cont("%s called results %s",__func__,ec.message().c_str());  
            if(ec)  
                return ;//fail(ec, "resolve");  
  
            // Make the connection on the IP address we get from a lookup  
            boost::asio::async_connect(  
                ws_.next_layer().next_layer(),  
                results.begin(),  
                results.end(),  
                std::bind(  
                    &session::on_connect,  
                    shared_from_this(),  
                    std::placeholders::_1));  
        }  
  
        void  
        on_connect(boost::system::error_code ec)  
        {  
            err_cont("%s called results %s",__func__,ec.message().c_str());  
            if(ec)  
                return ;//fail(ec, "connect");  
  
            // Perform the SSL handshake  
            ws_.next_layer().async_handshake(  
                ssl::stream_base::client,  
                std::bind(  
                    &session::on_ssl_handshake,  
                    shared_from_this(),  
                    std::placeholders::_1));  
        }  
  
  
        void  
        on_ssl_handshake(boost::system::error_code ec)  
        {  
            err_cont("%s called results %s",__func__,ec.message().c_str());  
            if(ec)  
                return ;//fail(ec, "ssl_handshake");  
  
            // Perform the websocket handshake  
ws_.async_handshake(host_, "/realtime",  <---------------------------- cores here  
                std::bind(  
                    &session::on_handshake,  
                    shared_from_this(),  
                    std::placeholders::_1));  
        }  
  
        void  
        on_handshake(boost::system::error_code ec)  
        {  
            err_cont("%s called results %s",__func__,ec.message().c_str());  
            if(ec)  
                return;// fail(ec, "handshake");  
  
            // Send the message  
            ws_.async_write(  
                boost::asio::buffer(text_),  
                std::bind(  
                    &session::on_write,  
                    shared_from_this(),  
                    std::placeholders::_1,  
                    std::placeholders::_2));  
        }  
  
        void  
        on_write(  
            boost::system::error_code ec,  
            std::size_t bytes_transferred)  
        {  
            boost::ignore_unused(bytes_transferred);  
  
            err_cont("%s called results %s",__func__,ec.message().c_str());  
            if(ec)  
                return;// fail(ec, "write");  
  
            // Read a message into our buffer  
            ws_.async_read(  
                buffer_,  
                std::bind(  
                    &session::on_read,  
                    shared_from_this(),  
                    std::placeholders::_1,  
                    std::placeholders::_2));  
        }  
  
        void  
        on_read(  
            boost::system::error_code ec,  
            std::size_t bytes_transferred)  
        {  
            boost::ignore_unused(bytes_transferred);  
  
            err_cont("%s called results %s",__func__,ec.message().c_str());  
            if(ec)  
                return ;//fail(ec, "read");  
  
            // Close the WebSocket connection  
           // ws_.async_close(websocket::close_code::normal,  
            //    std::bind(  
             //       &session::on_close,  
             //       shared_from_this(),  
             //       std::placeholders::_1));  
        }  
  
        void  
        on_close(boost::system::error_code ec)  
        {  
            err_cont("%s called results %s",__func__,ec.message().c_str());  
            if(ec)  
                return ;//fail(ec, "close");  
  
            // If we get here then the connection is closed gracefully  
  
            // The buffers() function helps print a ConstBufferSequence  
            std::cout << boost::beast::buffers(buffer_.data()) << std::endl;  
        }  
    };  
  
  
Any help would be greatly appreciated.  
  
Thanks!  
  
  
  
From: Vinnie Falco [mailto:notifications@github.com]  
Sent: Monday, July 9, 2018 6:45 PM  
To: boostorg/beast  
Cc: Lionel Girardin; Author  
Subject: Re: [boostorg/beast] Websocket ssl async crash when doing async_handshake (#1182)  
  
  
However the asynchronous code at https://www.boost.org/doc/libs/master/libs/beast/example/websocket/client/async-ssl/websocket_client_async_ssl.cpp always cores,  
  
Which version of the example source code? Which version of Boost? Have you made modifications? What command-line arguments are you passing?  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/beast/issues/1182#issuecomment-403653842>, or mute the thread<https://github.com/notifications/unsubscribe-auth/AnIKD5t613JXCAXlDval7007Dc5CWbR5ks5uE-rggaJpZM4VIiVo>.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-07-10 14:49:58 UTC  
> Updated at: 2018-07-10 14:50:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1182#issuecomment-403850562  

How many threads are calling `io_context::run`?  
Why aren't you using a `strand` in your completion tokens?  
  
Also, if you are using Boost 1.67 then you should use the examples from that same version. The tip of the master branch has the Boost 1.68 Beta version of the examples. You can get 1.67 files from here:  
https://github.com/boostorg/beast/tree/boost-1.67.0

---

## Comment 4

> Username: leogi222  
> Created at: 2018-07-10 15:07:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1182#issuecomment-403856952  

It seems that only 1 thread is calling io_context::run  
  
From: Vinnie Falco [mailto:notifications@github.com]  
Sent: Tuesday, July 10, 2018 9:50 AM  
To: boostorg/beast  
Cc: Lionel Girardin; Author  
Subject: Re: [boostorg/beast] Websocket ssl async crash when doing async_handshake (#1182)  
  
  
Why aren't you using a strand in your completion tokens? How many threads are calling io_context::run?  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/beast/issues/1182#issuecomment-403850562>, or mute the thread<https://github.com/notifications/unsubscribe-auth/AnIKD-zXiPjW4ksED22d1_rO0ytYN4TGks5uFL8bgaJpZM4VIiVo>.

---

## Comment 5

> Username: leogi222  
> Created at: 2018-07-10 22:18:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1182#issuecomment-403984033  

Hi and thank you for looking into this.  
  
There is actually no specific problem with the handshake, but rather my understanding of how to use and adapt the example.  
  
The logging I  was basing my analysis on to identify the core is actually a delayed call, which I had forgotten, so the last lines written to the log are not necessary indicative of the exact reason of the crash.  
  
What I see is that if my on_read function is called and doesn’t call the async_close function, it will core, unless a return statement is added! I am not sure why.  
  
Also, I am wondering how I will get the live data coming in. Provided I do not call the async_close function, will the on_write  function keep getting called? I would expect the library would keep on feeding my app with data and call the on_write  callback as long as the socket is open, just like a regular socket would behave.  
  
Please let me know how the architecture is supposed to work.  
  
Thank you  
  
From: Vinnie Falco [mailto:notifications@github.com]  
Sent: Monday, July 9, 2018 6:45 PM  
To: boostorg/beast  
Cc: Lionel Girardin; Author  
Subject: Re: [boostorg/beast] Websocket ssl async crash when doing async_handshake (#1182)  
  
  
However the asynchronous code at https://www.boost.org/doc/libs/master/libs/beast/example/websocket/client/async-ssl/websocket_client_async_ssl.cpp always cores,  
  
Which version of the example source code? Which version of Boost? Have you made modifications? What command-line arguments are you passing?  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/beast/issues/1182#issuecomment-403653842>, or mute the thread<https://github.com/notifications/unsubscribe-auth/AnIKD5t613JXCAXlDval7007Dc5CWbR5ks5uE-rggaJpZM4VIiVo>.

---

## Comment 6

> Username: leogi222  
> Created at: 2018-07-10 23:18:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1182#issuecomment-403995669  

Just wanted to add that obviously the crash was not related to the absence of an unnecessary return statement, but rather due to our late logging. I still need more clarity on how to get the data in the socket though.  
Thanks  
  
From: Lionel Girardin  
Sent: Tuesday, July 10, 2018 5:18 PM  
To: 'boostorg/beast'; boostorg/beast  
Cc: Author  
Subject: RE: [boostorg/beast] Websocket ssl async crash when doing async_handshake (#1182)  
  
Hi and thank you for looking into this.  
  
There is actually no specific problem with the handshake, but rather my understanding of how to use and adapt the example.  
  
The logging I  was basing my analysis on to identify the core is actually a delayed call, which I had forgotten, so the last lines written to the log are not necessary indicative of the exact reason of the crash.  
  
What I see is that if my on_read function is called and doesn’t call the async_close function, it will core, unless a return statement is added! I am not sure why.  
  
Also, I am wondering how I will get the live data coming in. Provided I do not call the async_close function, will the on_write  function keep getting called? I would expect the library would keep on feeding my app with data and call the on_write  callback as long as the socket is open, just like a regular socket would behave.  
  
Please let me know how the architecture is supposed to work.  
  
Thank you  
  
From: Vinnie Falco [mailto:notifications@github.com]  
Sent: Monday, July 9, 2018 6:45 PM  
To: boostorg/beast  
Cc: Lionel Girardin; Author  
Subject: Re: [boostorg/beast] Websocket ssl async crash when doing async_handshake (#1182)  
  
  
However the asynchronous code at https://www.boost.org/doc/libs/master/libs/beast/example/websocket/client/async-ssl/websocket_client_async_ssl.cpp always cores,  
  
Which version of the example source code? Which version of Boost? Have you made modifications? What command-line arguments are you passing?  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/beast/issues/1182#issuecomment-403653842>, or mute the thread<https://github.com/notifications/unsubscribe-auth/AnIKD5t613JXCAXlDval7007Dc5CWbR5ks5uE-rggaJpZM4VIiVo>.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-07-11 00:52:41 UTC  
> Updated at: 2018-07-11 00:53:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1182#issuecomment-404010527  

> how I will get the live data coming in. Provided I do not call the async_close function, will the on_write  function keep getting called? I would expect the library would keep on feeding my app with data and call the on_write  callback as long as the socket is open, just like a regular socket would behave.  
  
Why would you think that? Asio sockets don't work that way, you need to call `async_read` again every time your completion handler is invoked. Beast is no different. For each call to an initiating function, you get one call to your completion handler.   
  
What happens when you run the original version of websocket-client-async-ssl without modification using this command line:  
```  
websocket-client-async-ssl echo.websocket.org 443 "Hello, world!"  
```

---

## Comment 8

> Username: leogi222  
> Created at: 2018-07-11 18:38:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1182#issuecomment-404269829  

"you need to call async_read again every time your completion handler is invoked"  
  
  
In the code provided, I call the connect and get provide a on_connect completion handler, then I do a async_handshake and get a response in the completion handler provided, i.e. on_ssl_handshake  
  
My question is, how do I send a message to the server (a subscription message) and provide a completion handler that will stay up in a listening stage as long as the socket is open?

---

## Comment 9

> Username: leogi222  
> Created at: 2018-07-11 18:48:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1182#issuecomment-404272607  

the original just gets he string back "hello world" in the completion handler as expected. Your code works fine as well to get the first welcome data from bitmex.com  
  
Question is how to I send/receive messages with server?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-07-11 18:50:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1182#issuecomment-404273280  

> Question is how to I send/receive messages with server?  
  
It works the same way as sending and receiving data with Asio TCP/IP sockets. You call async_write and async_read with your completion handler. If you want to do continuous reads, then you must call async_read again when the previous async_read completion handler is invoked.  
  
For writing, Beast only allows 1 active asynchronous write at a time. So if you want to send multiple messages at the same time (for example, from another thread) then you will need to queue them up. An example of this can be seen here:  
  
https://github.com/vinniefalco/CppCon2018/blob/3067248d5488d130fd3caaee2648774e677cea0d/websocket_session.cpp#L111

---

## Comment 11

> Username: leogi222  
> Created at: 2018-07-11 19:00:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1182#issuecomment-404276136  

Thanks!  
  
I am trying that now that I have a better understanding of the logic flow  
  
From: Vinnie Falco [mailto:notifications@github.com]  
Sent: Wednesday, July 11, 2018 1:51 PM  
To: boostorg/beast  
Cc: Lionel Girardin; Author  
Subject: Re: [boostorg/beast] Websocket ssl async crash when doing async_handshake (#1182)  
  
  
Question is how to I send/receive messages with server?  
  
It works the same way as sending and receiving data with Asio TCP/IP sockets. You call async_write and async_read with your completion handler. If you want to do continuous reads, then you must call async_read again when the previous async_read completion handler is invoked.  
  
For writing, Beast only allows 1 active asynchronous write at a time. So if you want to send multiple messages at the same time (for example, from another thread) then you will need to queue them up. An example of this can be seen here:  
  
https://github.com/vinniefalco/CppCon2018/blob/3067248d5488d130fd3caaee2648774e677cea0d/websocket_session.cpp#L111  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/beast/issues/1182#issuecomment-404273280>, or mute the thread<https://github.com/notifications/unsubscribe-auth/AnIKDyG8vkl4TsCzRodaZlgWvN7BY0MLks5uFkkHgaJpZM4VIiVo>.

---

## Comment 12

> Username: leogi222  
> Created at: 2018-07-11 19:13:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1182#issuecomment-404279776  

Well Thanks it seems to be working now.  
I guess I hadn't worked much with asio before so didn't realize I had to keep doing a async_read upon receiving a message

---

## Comment 13

> Username: vinniefalco  
> Created at: 2018-07-11 19:21:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1182#issuecomment-404282001  

If you want to write asynchronous programs with Beast, you should really first learn Asio, since Beast uses all of the same idioms. Glad to hear it worked!

---

## Comment 14

> Username: leogi222  
> Created at: 2018-07-11 19:22:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1182#issuecomment-404282218  

Good resource for asio?  
  
From: Vinnie Falco [mailto:notifications@github.com]  
Sent: Wednesday, July 11, 2018 2:22 PM  
To: boostorg/beast  
Cc: Lionel Girardin; Author  
Subject: Re: [boostorg/beast] Websocket ssl async crash when doing async_handshake (#1182)  
  
  
If you want to write asynchronous programs with Beast, you should really first learn Asio, since Beast uses all of the same idioms. Glad to hear it worked!  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/beast/issues/1182#issuecomment-404282001>, or mute the thread<https://github.com/notifications/unsubscribe-auth/AnIKD3N34r7g9LFm91S2g-8gvwNWEhhtks5uFlBMgaJpZM4VIiVo>.

---

## Comment 15

> Username: vinniefalco  
> Created at: 2018-07-11 19:30:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1182#issuecomment-404284198  

> Good resource for asio?  
  
I don't know of any (but that doesn't mean they don't exist).

---

## Comment 16

> Username: leogi222  
> Created at: 2018-07-11 19:34:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1182#issuecomment-404285368  

I would just suggest to just maybe add a couple of lines in the readme explaining the general workflow which would save some time for new implementations. Your example works great and you were really quick to answer. Thanks again!

---

## Comment 17

> Username: stale[bot]  
> Created at: 2018-08-10 20:28:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1182#issuecomment-412196981  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 18

> Username: leogi222  
> Created at: 2018-08-14 14:55:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1182#issuecomment-412900927  

Hi  
  
I had one more question.  
  
What is the minimum version of boost that supports the Beast lib?  
  
From: Vinnie Falco [mailto:notifications@github.com]  
Sent: Wednesday, July 11, 2018 2:30 PM  
To: boostorg/beast  
Cc: Lionel Girardin; Author  
Subject: Re: [boostorg/beast] Websocket ssl async crash when doing async_handshake (#1182)  
  
  
Good resource for asio?  
  
I don't know of any (but that doesn't mean they don't exist).  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/beast/issues/1182#issuecomment-404284198>, or mute the thread<https://github.com/notifications/unsubscribe-auth/AnIKDyUAUKUW-mr1dsMW0Euns8fYaj8uks5uFlJBgaJpZM4VIiVo>.

---

## Comment 19

> Username: vinniefalco  
> Created at: 2018-08-14 15:31:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1182#issuecomment-412914077  

Minimum of Boost 1.66 but Boost 1.68 is recommended.

---

## Comment 20

> Username: stale[bot]  
> Created at: 2018-09-13 16:21:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1182#issuecomment-421066575  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 21

> Username: stale[bot]  
> Created at: 2018-09-20 16:50:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1182#issuecomment-423254861  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
