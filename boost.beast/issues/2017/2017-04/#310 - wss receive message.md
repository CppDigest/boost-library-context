# #310 - wss receive message [Closed]

> Username: Lifeda92  
> Created at: 2017-04-20 10:39:29 UTC  
> Updated at: 2018-06-30 13:03:28 UTC  
> Closed at: 2017-04-26 22:51:36 UTC  
> Url: https://github.com/boostorg/beast/issues/310  

Hello, why i cant get some info of the server?  
I used example wss code. I change only host and message to the server.  
Code is below.  
Can you help me, please? Thx.  
  
Handshke:  
```  
HTTP/1.1 101 Switching Protocols  
Date: Thu, 20 Apr 2017 10:22:55 GMT  
Connection: upgrade  
Set-Cookie: __cfduid=d629de44f81f99af48817847389ad88961492683775; expires=Fri, 20-Apr-18 10:22:55 GMT; path=/; domain=.dota2.net; HttpOnly  
Upgrade: websocket  
Sec-WebSocket-Accept: uU58GiRjm3C2txKg3JfRD6i28Ag=  
Sec-WebSocket-Version: 13  
Server: cloudflare-nginx  
CF-RAY: 3527629c3ea70f57-FRA  
```  
  
Code:  
```  
//  
// Copyright (c) 2013-2017 Vinnie Falco (vinnie dot falco at gmail dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
  
#include <beast/core/to_string.hpp>  
#include <beast/websocket.hpp>  
#include <beast/websocket/ssl.hpp>  
#include <boost/asio.hpp>  
#include <boost/asio/ssl.hpp>  
#include <iostream>  
#include <string>  
  
int main()  
{  
    using boost::asio::connect;  
    using socket = boost::asio::ip::tcp::socket;  
    using resolver = boost::asio::ip::tcp::resolver;  
    using io_service = boost::asio::io_service;  
    namespace ssl = boost::asio::ssl;  
  
    // Normal boost::asio setup  
    std::string const host = "wsn.dota2.net";  
    io_service ios;  
    resolver r{ios};  
    socket sock{ios};  
    connect(sock, r.resolve(resolver::query{host, "https"}));  
  
    // Perform SSL handshaking  
    using stream_type = ssl::stream<socket&>;  
    ssl::context ctx{ssl::context::sslv23};  
    stream_type stream{sock, ctx};  
    stream.set_verify_mode(ssl::verify_none);  
    stream.handshake(ssl::stream_base::client);  
  
    // Secure WebSocket connect and send message using Beast  
    beast::websocket::stream<stream_type&> ws{stream};  
    ws.handshake(host, "/wsn/");  
    ws.write(boost::asio::buffer("history_go"));  
  
    // Receive Secure WebSocket message, print and close using Beast  
    beast::streambuf sb;  
    beast::websocket::opcode op;  
    ws.read(op, sb);  
    ws.close(beast::websocket::close_code::normal);  
    std::cout << to_string(sb.data()) << "\n";  
	system("PAUSE");  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-04-20 16:31:56 UTC  
> Url: https://github.com/boostorg/beast/issues/310#issuecomment-295803337  

I've been looking over https://market.dota2.net/docs/.  
  
I can't read Russian, but I think you aren't sending the proper command. Don't you need to submit your API key to the dota2 server in order get subscriptions or information? The documentation isn't clear.

---

## Comment 2

> Username: Lifeda92  
> Created at: 2017-04-20 20:22:41 UTC  
> Updated at: 2017-04-20 22:55:52 UTC  
> Url: https://github.com/boostorg/beast/issues/310#issuecomment-295892796  

I tried send wsAuth, got it of the https://market.csgo.com/api/GetWSAuth/?key=[your_secret_key]  
Result was same, no reaction of the server(  
Ok, if i send uncorrect request, server must send me some response.  
Why server not send me something?  
Program simple stopped on line: ws.read(op, sb);  
May be server not get my request?  
sry for my english :)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-04-20 20:27:03 UTC  
> Url: https://github.com/boostorg/beast/issues/310#issuecomment-295895114  

I have no idea why that server is not responding to you.

---

## Comment 4

> Username: Lifeda92  
> Created at: 2017-04-20 22:48:35 UTC  
> Updated at: 2017-04-20 22:51:05 UTC  
> Url: https://github.com/boostorg/beast/issues/310#issuecomment-295951490  

Why program stopped on line ws.read(op, sb);?  
What does it mean?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-04-21 00:55:21 UTC  
> Url: https://github.com/boostorg/beast/issues/310#issuecomment-295993405  

@Lifeda92 `websocket::stream::read()` blocks until a message is received, or until an error occurs:  
http://vinniefalco.github.io/beast/beast/ref/websocket__stream/read/overload1.html

---

## Comment 6

> Username: Lifeda92  
> Created at: 2017-04-21 07:54:46 UTC  
> Updated at: 2017-04-21 08:14:56 UTC  
> Url: https://github.com/boostorg/beast/issues/310#issuecomment-296115032  

I tried websocket-example (without ssl). Server sent me the info when i sent him request history_go or newitems_go. Why i cant get this info when i use ssl? I'm sure, that a server supporting ssl. What could be the problem? May be problem with my openssl? it is 1.0.1  
  
Handshake:  
```  
HTTP/1.1 101 Switching Protocols  
Date: Fri, 21 Apr 2017 07:35:44 GMT  
Connection: upgrade  
Set-Cookie: __cfduid=d4e929ac16cec9cefdd5878725602d6c21492760144; expires=Sat, 21-Apr-18 07:35:44 GMT; path=/; domain=.dota2.net; HttpOnly  
Upgrade: websocket  
Sec-WebSocket-Accept: fprDf9qxiq6uKKUqIVGkN9T+N40=  
Sec-WebSocket-Version: 13  
Server: cloudflare-nginx  
CF-RAY: 352eab1511c9638b-FRA  
```  
  
This Code works:  
```  
//  
// Copyright (c) 2013-2017 Vinnie Falco (vinnie dot falco at gmail dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
  
#include <beast/core/to_string.hpp>  
#include <beast/websocket.hpp>  
#include <boost/asio.hpp>  
#include <iostream>  
#include <string>  
  
int main()  
{  
    // Normal boost::asio setup  
    std::string const host = "wsn.dota2.net";  
    boost::asio::io_service ios;  
    boost::asio::ip::tcp::resolver r{ios};  
    boost::asio::ip::tcp::socket sock{ios};  
    boost::asio::connect(sock,  
        r.resolve(boost::asio::ip::tcp::resolver::query{host, "80"}));  
  
    // WebSocket connect and send message using beast  
    beast::websocket::stream<boost::asio::ip::tcp::socket&> ws{sock};  
    ws.handshake(host, "/wsn/");  
    ws.write(boost::asio::buffer(std::string("history_go")));  
  
    // Receive WebSocket message, print and close using beast  
    beast::streambuf sb;  
    beast::websocket::opcode op;  
    ws.read(op, sb);  
    ws.close(beast::websocket::close_code::normal);  
    std::cout << beast::to_string(sb.data()) << "\n";  
	system("pause");  
}  
```

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-04-21 16:29:25 UTC  
> Url: https://github.com/boostorg/beast/issues/310#issuecomment-296238776  

I have no idea why the server at wsn.dota2.net does not send back messages over SSL connections. Perhaps you should contact the server operator and begin an inquiry?

---

## Comment 8

> Username: Lifeda92  
> Created at: 2017-04-22 00:24:21 UTC  
> Updated at: 2017-04-22 00:24:31 UTC  
> Url: https://github.com/boostorg/beast/issues/310#issuecomment-296333133  

Please, tell me, how can i add field: "Origin:wss://wsn.dota2.net/wsn/" on the header?  
  
need:  
```  
GET /wsn/ HTTP/1.1  
Host: wsn.dota2.net  
Upgrade: websocket  
Sec-WebSocket-Key: dhaNU/6uTkhmsnYsetBQAQ==  
Sec-WebSocket-Version: 13  
User-Agent: Beast/1.0.0-b34  
Connection: upgrade  
Origin: wss://wsn.dota2.net (need add this)  
```

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-04-22 00:28:44 UTC  
> Url: https://github.com/boostorg/beast/issues/310#issuecomment-296333463  

Use `stream::set_option` with a "decorator":  
http://vinniefalco.github.io/beast/beast/ref/websocket__stream/set_option/overload3.html  
  
See:  
https://github.com/vinniefalco/Beast/issues/212

---

## Comment 10

> Username: Lifeda92  
> Created at: 2017-04-22 02:00:31 UTC  
> Updated at: 2017-04-22 02:01:17 UTC  
> Url: https://github.com/boostorg/beast/issues/310#issuecomment-296339545  

The problem was in the data type...  
If change  ws.write(boost::asio::buffer("history_go")); on   ws.write(boost::asio::buffer(std::string("history_go"))); everything works! Server send me info over ssl. Thanks for support.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-04-22 02:07:36 UTC  
> Url: https://github.com/boostorg/beast/issues/310#issuecomment-296340002  

Oh! I should have seen that. I wrote this small utility function to help with such things:  
```  
    template<std::size_t N>  
    boost::asio::const_buffers_1  
    make_buffer(const char (&s)[N])  
    {  
        return boost::asio::const_buffers_1{&s[0], N-1};  
    }  
```

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-04-26 22:51:36 UTC  
> Url: https://github.com/boostorg/beast/issues/310#issuecomment-297563721  

It looks like this has been resolved. If any other issues pop up, or this problem comes up again feel free to open a new issue, thanks!

---

## Comment 13

> Username: litwr2  
> Created at: 2018-06-28 12:15:09 UTC  
> Url: https://github.com/boostorg/beast/issues/310#issuecomment-401014287  

The codes above don't work with boost 1.67 - they produce a lot of errors during compilation. :( It looks like beast had too many changes for a year. Is it possible to get an example with the same functionality for the current beast? Please...

---

## Comment 14

> Username: vinniefalco  
> Created at: 2018-06-28 13:14:14 UTC  
> Url: https://github.com/boostorg/beast/issues/310#issuecomment-401030110  

Yes, all of the Beast examples for Boost version 1.67.0 are here:  
https://github.com/boostorg/beast/tree/boost-1.67.0/example

---

## Comment 15

> Username: litwr2  
> Created at: 2018-06-28 20:04:40 UTC  
> Url: https://github.com/boostorg/beast/issues/310#issuecomment-401156432  

Thank you very much! That **websocket_client_async_ssl.cpp** looks like a piece of art for me.  However I still can't comprehend how it works. :( **websocket_client_sync_ssl.cpp** is much easier.  Could you show me a way how to make the functionality of the next code inserted into **sync** version for **async** version?  
  
        for (int i = 0; i < 10; ++i) {  
            // Read a message into our buffer  
            ws.read(b);  
            // Print out our buffer  
            std::cout << boost::beast::buffers(b.data()) << std::endl;  
            b.consume(b.size());  
       }  
  
So I need just to read a buffer 10 times. Any hints?  A lot of thanks in advance.

---

## Comment 16

> Username: vinniefalco  
> Created at: 2018-06-28 21:32:15 UTC  
> Updated at: 2018-06-28 21:32:39 UTC  
> Url: https://github.com/boostorg/beast/issues/310#issuecomment-401179993  

All I can really suggest is to study asynchronous Boost.Asio, this is not really a beast-specific problem. If you have a question about a particular part of websocket-client-async-ssl, I can try to answer it.

---

## Comment 17

> Username: litwr2  
> Created at: 2018-06-29 11:43:14 UTC  
> Url: https://github.com/boostorg/beast/issues/310#issuecomment-401329997  

Dear Vinnie, excuse me but I am really baffled by a fact that the next code doesn't work.  
  
    boost::beast::multi_buffer b;  
    ws.read(b);  
    std::ostringstream so;  
    so << boost::beast::buffers(b.data()) << std::endl;  
    std::cout << so.str() << "\n";  
  
The last line prints nothing. How is it possible? Indeed, I use `std::string sb = buffers_to_string(b.data())` for the required conversion but why doesn't sstream work?

---

## Comment 18

> Username: vinniefalco  
> Created at: 2018-06-29 12:57:22 UTC  
> Url: https://github.com/boostorg/beast/issues/310#issuecomment-401346310  

I have no idea, have you tried stepping into it with a debugger to find out what is happening?

---

## Comment 19

> Username: litwr2  
> Created at: 2018-06-29 15:56:39 UTC  
> Url: https://github.com/boostorg/beast/issues/310#issuecomment-401397676  

Thank you.  I was just curious about the case.  It is interesting that `std::cout << boost::beast::buffers(b.data()) << std::endl;` works perfectly.  IMHO something is wrong either with stl or boost.

---

## Comment 20

> Username: vinniefalco  
> Created at: 2018-06-29 16:37:08 UTC  
> Url: https://github.com/boostorg/beast/issues/310#issuecomment-401408309  

maybe your buffer contains a null at the beginning?

---

## Comment 21

> Username: litwr2  
> Created at: 2018-06-30 06:28:24 UTC  
> Url: https://github.com/boostorg/beast/issues/310#issuecomment-401521163  

Thank you very much. It is rather something wrong with my application. Sorry for my a bit dumb question. I have begun to use beast and it is still not easy for me to handle it properly.

---

## Comment 22

> Username: vinniefalco  
> Created at: 2018-06-30 13:03:28 UTC  
> Url: https://github.com/boostorg/beast/issues/310#issuecomment-401539942  

No problem, I'm glad to help
