# #2416 - Assert in close.hpp line 244 version 1.75 [Closed]

> Username: gopalak  
> Created at: 2022-04-26 04:30:05 UTC  
> Updated at: 2024-01-08 13:49:56 UTC  
> Closed at: 2024-01-08 13:49:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2416  

### Discussed in https://github.com/boostorg/beast/discussions/2415  
  
<div type='discussions-op-text'>  
  
<sup>Originally posted by **gopalak** April 25, 2022</sup>  
Hi I am seeing an assert in close.hpp which is kind of strange as I have serialized and made sure only one of async read, wr and close is called at any given time.  
  
Unable to attach stack as its happening in my production code - What I see in the stack is I see assert in --  
242         teardown:  
243             // Teardown  
244             BOOST_ASSERT(impl.wr_block.is_locked(this));  
245             using beast::websocket::async_teardown;  
---  
Happens fron here. Ido have read and write handlers installed as per doc and dispatched  
  
<img width="1198" alt="Screen Shot 2022-04-25 at 10 05 46 AM" src="https://user-images.githubusercontent.com/9953722/165138420-a06f5ab0-9fa7-4a26-921e-56ac62585de2.png">  
------------  
  
</div>

---

## Comment 1

> Username: gopalak  
> Created at: 2022-04-26 04:30:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1109318176  

@vinniefalco here moved it as an issue - will repost some of my finding here

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-04-26 04:31:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1109318410  

Glad to see that issues work :)

---

## Comment 3

> Username: gopalak  
> Created at: 2022-04-26 04:31:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1109318763  

I have 4 threads but think I figured seeing the code in close.hpp:244 - where it asserts that a write callback in actually installed via the impl.wr_block -> My code path has a logic to do some had shake before I actually install both read and write handlers and where this assert is hit, I only had read callback installed but no async write - issue is the window when the teardown was happening and this assert hit is not like easily reproducible. But based on reading the code and assert line that seems to be the correct fix.  
  
There is no documentation which says the async close needs a pre rfequisite of write and read handlers needs to be installed for SSK streams @vinniefalco

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-04-26 04:33:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1109319381  

You can't mix sync and async calls

---

## Comment 5

> Username: gopalak  
> Created at: 2022-04-26 04:33:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1109319437  

<img width="1086" alt="Screen Shot 2022-04-25 at 3 15 47 PM" src="https://user-images.githubusercontent.com/9953722/165221725-fb9c43c8-e4d2-46a7-8a90-20ec45d2378c.png">

---

## Comment 6

> Username: gopalak  
> Created at: 2022-04-26 04:34:30 UTC  
> Updated at: 2022-04-26 04:35:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1109320118  

@vinniefalco I am not mixing sync and async calls -> All I said was I am installing calls to async_read and async_write if one is not already present in my logic if I have to call async_close during my handshake flow  
  
So what I am doing semantically is   
  
if (no_async_read_installed)  
    install_async_read();  
  
if (no_async_write_installed)  
      install async_write_hdlr()l;  
  
call async_close();

---

## Comment 7

> Username: gopalak  
> Created at: 2022-04-26 04:37:44 UTC  
> Updated at: 2022-04-26 04:40:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1109321707  

> You can't mix sync and async calls  
  
SO just to clarify I am not mixing and matching sync / async calls - all are async calls here. Pls see above comment ... - and when I say install its a dispatch of async_read and async_write is all I am doing before calling async_close - as one stack trace I saw actually had like 4 bytes of data in the buffers read and due to close was trying to write the close packet and that is where it hit the assert - see my screen shot above on the the actual beast src code where it hit the assert

---

## Comment 8

> Username: gopalak  
> Created at: 2022-04-26 04:42:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1109330936  

Since there is no doc on this I want to make sure I am understanding the code in the assert line that it indeed is looking that an async_write call is registered which would have the lock - is that what that call checks ?

---

## Comment 9

> Username: vinniefalco  
> Created at: 2022-04-26 04:45:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1109337997  

There is a doc, see:  
  
https://www.boost.org/doc/libs/1_79_0/libs/beast/doc/html/beast/using_websocket/notes.html#beast.using_websocket.notes.thread_safety

---

## Comment 10

> Username: vinniefalco  
> Created at: 2022-04-26 04:46:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1109338563  

If you are getting that assert it means that more than one thread is touching the websocket stream at the same time, which is not allowed.

---

## Comment 11

> Username: gopalak  
> Created at: 2022-04-26 04:47:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1109339277  

I have made all async calls dispatched to the executor of the websocket stream (read, write and close - all async)

---

## Comment 12

> Username: gopalak  
> Created at: 2022-04-26 04:48:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1109339905  

> There is a doc, see:  
>   
> https://www.boost.org/doc/libs/1_79_0/libs/beast/doc/html/beast/using_websocket/notes.html#beast.using_websocket.notes.thread_safety  
  
Yes I have read it and I have safe guards that only one of async (read, wr or close) is called in my code flow as well

---

## Comment 13

> Username: gopalak  
> Created at: 2022-04-26 04:58:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1109344551  

Is there a way I can find the thread which locked the soft futex in gdb ?

---

## Comment 14

> Username: gopalak  
> Created at: 2022-04-26 16:25:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1109999665  

@vinniefalco Changing to 1 thread for the io context makes no difference. I can see alll completion handler in that thread

---

## Comment 15

> Username: vinniefalco  
> Created at: 2022-04-26 17:41:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1110076353  

You still get the assert? Are you calling `async_close` from a foreign thread?

---

## Comment 16

> Username: gopalak  
> Created at: 2022-04-26 17:51:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1110085334  

> You still get the assert? Are you calling `async_close` from a foreign thread?  
  
Yes i am still seeing the assert and no I am not calling it from a foreign thread. Its the thread where io_ctx is run and I have set it to 1 thread now

---

## Comment 17

> Username: gopalak  
> Created at: 2022-04-26 18:02:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1110094963  

I am unable to share code but this is the sequence ->  
Complete WS handshake with peer  
Do some op like send one request and wait for reply  
and when waiting for reply client closes connection using async_close  
Think the way the close frame comes when we  are waiting for read may be causing some thing (this is my gut feeling) as from my logs I can clearly see I am waiting for a reply when this assert is hit and nothing else happens in between apart from the async close and I can assure u that I dispatch async_close to the strand

---

## Comment 18

> Username: vinniefalco  
> Created at: 2022-06-14 17:34:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1155499197  

Has this been resolved?

---

## Comment 19

> Username: gopalak  
> Created at: 2022-09-05 16:32:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1237285094  

No this is still happening @vinniefalco

---

## Comment 20

> Username: gopalak  
> Created at: 2022-09-05 21:17:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1237462465  

So one recent scenario I saw was like this - @vinniefalco @madmongo1   
1. Connection is started - I have aa timer for connect   
2. SSL handshake done  
3. WS handshake starts -  
4. The app layer has this holder obj (lets call it AppSession which holds a shared pointer of the WS conn) has a cleanup started and as part of it - calls async_close on the WS Session which s actually working on WS handshake   
5. This call hits an ABORT on the above call or some times one level down asserting id != T::id -  
So clearly the handshake starting and async_close seems to be hitting some race condition.  
  
There is only 1 thread for the io_ctx  
all async calls are dispatched to the strand and guards are in place to make sure there is only 1 of each async calls active at a given instant.  
Those asserts never fire meaning I have no pending and active async calls in tandem  
  
This once again happens mostly under load. I think the above scenario is kind of hard to recreate. I tried in my code where I set the appObj state to bad and started the shutdown and when there is not much load it seems to work fine. In Prod we did see a lot of load on the client side as well as the server side and this scenario is hit multiple times.  
  
I think there is a race condition some where and the checking of state as most asserts are in the same location when either the SSL is being torndown or the WS session

---

## Comment 21

> Username: gopalak  
> Created at: 2022-09-06 18:13:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1238494235  

@vinniefalco @madmongo1 can u folks pls help for the above ?

---

## Comment 22

> Username: madmongo1  
> Created at: 2022-09-06 19:06:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1238543203  

Yep. Build me a test program to replicate the issue.  
Without that I don’t have a chance.

---

## Comment 23

> Username: gopalak  
> Created at: 2022-09-06 19:17:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1238555550  

@madmongo1 I am unable to like I said its happening under load. I can send u stack trace to ur email. Which email can I send to also can copy @vinniefalco as well

---

## Comment 24

> Username: madmongo1  
> Created at: 2022-09-06 19:18:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1238556506  

Can you build a test that creates a lot of load?  
Perhaps fork 1000 clients to hammer the service?

---

## Comment 25

> Username: gopalak  
> Created at: 2022-09-06 19:20:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1238558363  

No - We cant its not a simple service to build else I would have. There are complex states involved. Cant give much innards here unfortunately else trust me I would have done that before I bother you folks here

---

## Comment 26

> Username: madmongo1  
> Created at: 2022-09-06 19:23:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1238560821  

So this is how these things normally go down:  
Client reports a mysterious crash in beast.  
I ask him to build me a simpler test program to replicate the issue.  
The simpler test program doesn’t not crash.  
The client adds in more and more complexity until the program crashes.  
It turns out that the complexity was hiding some UB and beast is actually fine.

---

## Comment 27

> Username: madmongo1  
> Created at: 2022-09-06 19:23:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1238561136  

Prove me wrong.

---

## Comment 28

> Username: gopalak  
> Created at: 2022-09-06 19:24:40 UTC  
> Updated at: 2022-09-06 19:29:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1238562132  

So here is the thing. The assert is telling wr lock is not taken during tear down and I have one thread and all my async_close are dispatched. How can with 1 thread there could be multiple locks taken ? And if my code had an issue in such a dispatch which has happened before I didnt need this kind of load to see it. It would happen much faster with very less load and i have fixed them in my code.

---

## Comment 29

> Username: gopalak  
> Created at: 2022-09-06 19:25:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1238563038  

This is almost similar to the last assert you fixed in the beast code. With 1 thread for io_ctx and all async alls being dispatched we should never hit this scenario isnt it

---

## Comment 30

> Username: gopalak  
> Created at: 2022-09-06 19:27:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1238564248  

> Prove me wrong.  
  
I am not trying to prove any thing wrong here just to be clear. I am trying to assess and find a solution. I have scoured my code and it has so many asserts to catch any unexpected and wrong async calls made and none ever get hit

---

## Comment 31

> Username: gopalak  
> Created at: 2022-09-06 19:35:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1238571585  

let me know if u want me to email the stack trace I have if that may help ? @vinniefalco @madmongo1

---

## Comment 32

> Username: madmongo1  
> Created at: 2022-09-06 20:09:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1238601455  

Stack traces are not that useful in async code. The error will have occurred on a previous tick of the io context.  
Using moved-from objects, not preserving lifetimes and races are common causes.  
I am not challenging you when I ask you to prove me wrong.   
The way to find your bug is to prove me wrong.   
If you dont want to write a test program, remove some complexity and puit your system under load again. Keep removing complexity until it no longer crashes. Then add it in, one step at a time.  
The last edit you made before the crash will contain the wrong code.

---

## Comment 33

> Username: gopalak  
> Created at: 2022-09-06 20:24:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1238614312  

> Stack traces are not that useful in async code. The error will have occurred on a previous tick of the io context. Using moved-from objects, not preserving lifetimes and races are common causes. I am not challenging you when I ask you to prove me wrong. The way to find your bug is to prove me wrong. If you dont want to write a test program, remove some complexity and puit your system under load again. Keep removing complexity until it no longer crashes. Then add it in, one step at a time. The last edit you made before the crash will contain the wrong code.  
  
Ideally I would be doing that. Its way too complex to tear down the client we have to such levels. In case I write an ibuilt driver to simulate load what is the best way to catch this issue like u said in the previous tick of io_context ? Is there a BOOST macro which I can enable which preferable doesn't add much timing skew but at the same time help in tracking this

---

## Comment 34

> Username: madmongo1  
> Created at: 2022-09-06 21:29:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1238669186  

https://www.boost.org/doc/libs/1_80_0/doc/html/boost_asio/overview/core/handler_tracking.html

---

## Comment 35

> Username: klemens-morgenstern  
> Created at: 2022-09-24 04:55:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1256859890  

Is this issues resolved?

---

## Comment 36

> Username: gopalak  
> Created at: 2022-09-26 14:42:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1258146527  

This still happens. Yeah I also changed code to use tcp::socket instead of stream. Its kind of hard to reproduce. As part of another issue we are working on a not so small version to see if we can simulate the load and find the scenarios. At most I will try to send a boost handler trace once I get to finish this.

---

## Comment 37

> Username: cdbishop  
> Created at: 2023-01-16 15:35:12 UTC  
> Updated at: 2023-01-17 10:51:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1384222718  

Hi, I have hit this same assert, and like the above issue, believe it is not a threading issue as I have a single thread for my context and have ensured that the calls are dispatched/posted.  
  
The issue happens for me if I try to invoke `async_close` while an `async_handshake` is in progress. I believe what is happening is that both of those methods internally perform some read/write calls and they are conflicting with each other?  
  
I have managed to reproduce the same assert with a modification to the async-ssl websocket client example - see this [gist](https://gist.github.com/cdbishop/f590b0e96c31edaf06369dedd427cd89).   
  
The modification I made was to expose a public "Close" method that invokes async_close and then attempt to close the socket straight after an async_handshake (this simulates  a scenario in my application where a connection is started, then instantly cancelled, occasionally causing the same assert)  
  
In the above reproduction, there is only main thread and I can still hit the assert, so I believe it is not a threading issue.  
  
Is the change I made in the above example valid (i.e calling async_close straight after async_handshake) or should I be guarding against that somehow? Or is it a bug in beast?  
  
Appreciate any insight/suggestions on how this scenario should be handled.  
  
**EDIT**: Some clarification:   
  
I first added a close method, to act as an explicit method to close an active websocket connection, it just calls `async_close`, so really this method was not necessary to reproduce the option. I have verified that I can reproduce the issue by adding a call to `async_close` directly after the call to `async_handshake` the following change inside `on_ssl_handshake`  
  
Below is the only change I needed to make - which I made to the end of the `on_ssl_handshake` method in the async-ssl websocket client example:  
  
```c++  
// Perform the websocket handshake  
ws_.async_handshake(host_, "/",  
  beast::bind_front_handler(  
    &session::on_handshake,  
    shared_from_this()));  
  
// My Change: Invoke close straight after handshake request  
ws_.async_close(websocket::close_code::normal,  
  beast::bind_front_handler(  
  &session::on_close,  
  shared_from_this()));  
```

---

## Comment 38

> Username: gopalak  
> Created at: 2023-01-16 15:42:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1384230446  

Can you post this in the github thread as I dont see this over there  
  
Cheers  
Gops  
  
On Mon, Jan 16, 2023 at 7:35 AM cdbishop ***@***.***> wrote:  
  
> Hi, I have hit this same assert, and like the above issue, believe it is  
> not a threading issue I have all a single thread for my context and have  
> ensured that the calls are dispatched/posted.  
>  
> The issue happens for me if I try to invoke async_close while an  
> async_handshake is in progress. I believe what is happening is that both  
> of those methods internally perform some read/write calls and they are  
> conflicting with each other?  
>  
> I have managed to reproduce the same assert with a modification to the  
> async-ssl websocket client example - see this gist  
> <https://gist.github.com/cdbishop/f590b0e96c31edaf06369dedd427cd89>.  
>  
> The modification I made was to expose a public "Close" method that invokes  
> async_close and then attempt to close the socket straight after an  
> async_handshake (this simulates a scenario in my application where a  
> connection is started, then instantly cancelled, occasionally causing the  
> same assert)  
>  
> In the above reproduction, there is only main thread and I can still hit  
> the assert, so I believe it is not a threading issue.  
>  
> Is the change I made in the above example valid (i.e calling async_close  
> straight after async_handshake) or should I be guarding against that  
> somehow? Or is it a bug in beast?  
>  
> Appreciate any insight/suggestions on how this scenario should be handled  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2416#issuecomment-1384222718>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ACL6DOX6DHSVE6VS2OPZQSLWSVTDZANCNFSM5UKQ5IQA>  
> .  
> You are receiving this because you authored the thread.Message ID:  
> ***@***.***>  
>  
  
  
--   
Computers are like air conditioners - they stop working properly if you  
open Windows

---

## Comment 39

> Username: cdbishop  
> Created at: 2023-01-16 15:52:56 UTC  
> Updated at: 2023-01-17 11:51:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1384242207  

ah, my bad, I dont think I did a shared link [this](https://gist.github.com/cdbishop/f590b0e96c31edaf06369dedd427cd89) might work, but just in case, its below. My modification to simulate the behaviour is to request an `async_close` (via a `Close` method I added) - at the end of the `on_ssl_handshake` method, just after the `async_handshake` call.  
  
```c++  
//  
// Copyright (c) 2016-2019 Vinnie Falco (vinnie dot falco at gmail dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
// Official repository: https://github.com/boostorg/beast  
//  
  
//------------------------------------------------------------------------------  
//  
// Example: WebSocket SSL client, asynchronous  
//  
//------------------------------------------------------------------------------  
  
#include "../common/root_certificates.hpp"  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/ssl.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/beast/websocket/ssl.hpp>  
#include <boost/asio/strand.hpp>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace websocket = beast::websocket; // from <boost/beast/websocket.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
namespace ssl = boost::asio::ssl;       // from <boost/asio/ssl.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void  
fail(beast::error_code ec, char const* what)  
{  
    std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Sends a WebSocket message and prints the response  
class session : public std::enable_shared_from_this<session>  
{  
    tcp::resolver resolver_;  
    websocket::stream<  
        beast::ssl_stream<beast::tcp_stream>> ws_;  
    beast::flat_buffer buffer_;  
    std::string host_;  
    std::string text_;  
  
public:  
    // Resolver and socket require an io_context  
    explicit  
    session(net::io_context& ioc, ssl::context& ctx)  
        : resolver_(net::make_strand(ioc))  
        , ws_(net::make_strand(ioc), ctx)  
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
  
        // Look up the domain name  
        resolver_.async_resolve(  
            host,  
            port,  
            beast::bind_front_handler(  
                &session::on_resolve,  
                shared_from_this()));  
    }  
  
    void  
    on_resolve(  
        beast::error_code ec,  
        tcp::resolver::results_type results)  
    {  
        if(ec)  
            return fail(ec, "resolve");  
  
        // Set a timeout on the operation  
        beast::get_lowest_layer(ws_).expires_after(std::chrono::seconds(30));  
  
        // Make the connection on the IP address we get from a lookup  
        beast::get_lowest_layer(ws_).async_connect(  
            results,  
            beast::bind_front_handler(  
                &session::on_connect,  
                shared_from_this()));  
    }  
  
    void  
    on_connect(beast::error_code ec, tcp::resolver::results_type::endpoint_type ep)  
    {  
        if(ec)  
            return fail(ec, "connect");  
  
        // Set a timeout on the operation  
        beast::get_lowest_layer(ws_).expires_after(std::chrono::seconds(30));  
  
        // Set SNI Hostname (many hosts need this to handshake successfully)  
        if(! SSL_set_tlsext_host_name(  
                ws_.next_layer().native_handle(),  
                host_.c_str()))  
        {  
            ec = beast::error_code(static_cast<int>(::ERR_get_error()),  
                net::error::get_ssl_category());  
            return fail(ec, "connect");  
        }  
  
        // Update the host_ string. This will provide the value of the  
        // Host HTTP header during the WebSocket handshake.  
        // See https://tools.ietf.org/html/rfc7230#section-5.4  
        host_ += ':' + std::to_string(ep.port());  
          
        // Perform the SSL handshake  
        ws_.next_layer().async_handshake(  
            ssl::stream_base::client,  
            beast::bind_front_handler(  
                &session::on_ssl_handshake,  
                shared_from_this()));  
    }  
  
    void  
    on_ssl_handshake(beast::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "ssl_handshake");  
  
        // Turn off the timeout on the tcp_stream, because  
        // the websocket stream has its own timeout system.  
        beast::get_lowest_layer(ws_).expires_never();  
  
        // Set suggested timeout settings for the websocket  
        ws_.set_option(  
            websocket::stream_base::timeout::suggested(  
                beast::role_type::client));  
  
        // Set a decorator to change the User-Agent of the handshake  
        ws_.set_option(websocket::stream_base::decorator(  
            [](websocket::request_type& req)  
            {  
                req.set(http::field::user_agent,  
                    std::string(BOOST_BEAST_VERSION_STRING) +  
                        " websocket-client-async-ssl");  
            }));  
  
        // Perform the websocket handshake  
        ws_.async_handshake(host_, "/",  
            beast::bind_front_handler(  
                &session::on_handshake,  
                shared_from_this()));  
    }  
  
    void  
    on_handshake(beast::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "handshake");  
  
        // Send the message  
        ws_.async_write(  
            net::buffer(text_),  
            beast::bind_front_handler(  
                &session::on_write,  
                shared_from_this()));  
    }  
  
    void  
    on_write(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, "write");  
  
        // Read a message into our buffer  
        ws_.async_read(  
            buffer_,  
            beast::bind_front_handler(  
                &session::on_read,  
                shared_from_this()));  
  
        // My Change: Invoke close straight after handshake request  
        ws_.async_close(websocket::close_code::normal,  
            beast::bind_front_handler(  
                &session::on_close,  
                shared_from_this()));  
    }  
  
    void  
    on_read(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, "read");  
    }  
  
    void  
    on_close(beast::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "close");  
  
        // If we get here then the connection is closed gracefully  
  
        // The make_printable() function helps print a ConstBufferSequence  
        std::cout << beast::make_printable(buffer_.data()) << std::endl;  
    }  
};  
  
//------------------------------------------------------------------------------  
  
int main(int argc, char** argv)  
{  
    // Check command line arguments.  
    if(argc != 4)  
    {  
        std::cerr <<  
            "Usage: websocket-client-async-ssl <host> <port> <text>\n" <<  
            "Example:\n" <<  
            "    websocket-client-async-ssl echo.websocket.org 443 \"Hello, world!\"\n";  
        return EXIT_FAILURE;  
    }  
    auto const host = argv[1];  
    auto const port = argv[2];  
    auto const text = argv[3];  
  
    // The io_context is required for all I/O  
    net::io_context ioc;  
  
    // The SSL context is required, and holds certificates  
    ssl::context ctx{ssl::context::tlsv12_client};  
  
    // This holds the root certificate used for verification  
    load_root_certificates(ctx);  
  
    // Launch the asynchronous operation  
    std::make_shared<session>(ioc, ctx)->run(host, port, text);  
  
    // Run the I/O service. The call will return when  
    // the socket is closed.  
    ioc.run();  
  
    return EXIT_SUCCESS;  
}  
```  
  
Thanks for taking the time to look it over  
  
EDIT: slightly simplified the above repo example by removing the Close function/method and instead called async_close directly after async_handshake - this results in the same assert mentioned by the OP.

---

## Comment 40

> Username: gopalak  
> Created at: 2023-01-16 16:46:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1384312132  

From my understanding of boost / beast code - at any time one of each of  
async_read, async_write and async_close can exist and the app code should  
guarantee this. If this is the case, these async calls would run in strands  
(if i MT app) and work fine. I am not sure when you say you made the close  
API public you mean the test code class ?  
  
In my case, the async close was triggered by a timer which I have which  
when it sees a delay in handshake reply will try and close the WEb Socket  
connection.  
  
But can u post this in the thread -  
https://github.com/boostorg/beast/issues/2416#issuecomment-1384222718  
  
Cheers  
Gops  
  
On Mon, Jan 16, 2023 at 7:35 AM cdbishop ***@***.***> wrote:  
  
> Hi, I have hit this same assert, and like the above issue, believe it is  
> not a threading issue I have all a single thread for my context and have  
> ensured that the calls are dispatched/posted.  
>  
> The issue happens for me if I try to invoke async_close while an  
> async_handshake is in progress. I believe what is happening is that both  
> of those methods internally perform some read/write calls and they are  
> conflicting with each other?  
>  
> I have managed to reproduce the same assert with a modification to the  
> async-ssl websocket client example - see this gist  
> <https://gist.github.com/cdbishop/f590b0e96c31edaf06369dedd427cd89>.  
>  
> The modification I made was to expose a public "Close" method that invokes  
> async_close and then attempt to close the socket straight after an  
> async_handshake (this simulates a scenario in my application where a  
> connection is started, then instantly cancelled, occasionally causing the  
> same assert)  
>  
> In the above reproduction, there is only main thread and I can still hit  
> the assert, so I believe it is not a threading issue.  
>  
> Is the change I made in the above example valid (i.e calling async_close  
> straight after async_handshake) or should I be guarding against that  
> somehow? Or is it a bug in beast?  
>  
> Appreciate any insight/suggestions on how this scenario should be handled  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2416#issuecomment-1384222718>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ACL6DOX6DHSVE6VS2OPZQSLWSVTDZANCNFSM5UKQ5IQA>  
> .  
> You are receiving this because you authored the thread.Message ID:  
> ***@***.***>  
>  
  
  
--   
Computers are like air conditioners - they stop working properly if you  
open Windows

---

## Comment 41

> Username: gopalak  
> Created at: 2023-01-17 15:53:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1385637474  

@madmongo1 see repo case from @cdbishop (thanks)

---

## Comment 42

> Username: gopalak  
> Created at: 2023-01-18 16:26:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1387348589  

@madmongo1 @vinniefalco above is a reproducible test case from @cdbishop Please post your thoughts and if the above helps to see if beast code needs a fix or not ?

---

## Comment 43

> Username: gopalak  
> Created at: 2023-01-24 17:57:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1402358540  

@klemens-morgenstern can you move this from LIMBO tag - it was awaiting some kind of reproducible steps which is attached above now. Appreciate if this can be looked into @vinniefalco @madmongo1 - Cheers

---

## Comment 44

> Username: johannesparty  
> Created at: 2023-03-03 19:07:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1453990253  

Any update?  
  
This happens consistently when this happens:  
  
async_connect ... on_connect  
async_handshake ... (call async_close) ... on_handshake (no error)  
    ... crash "BOOST_ASSERT(impl.wr_block.is_locked(this))"  
  
I'm confident all these calls are on a single strand, plus I'm testing with only a single thread.  
  
So it looks to me like maybe async_handshake is doing something wrong unless there is a lower-level problem?

---

## Comment 45

> Username: gopalak  
> Created at: 2023-03-03 19:15:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1454004369  

> Any update?  
>   
> This happens consistently when this happens:  
>   
> async_connect ... on_connect async_handshake ... (call async_close) ... on_handshake (no error) ... crash "BOOST_ASSERT(impl.wr_block.is_locked(this))"  
>   
> I'm confident all these calls are on a single strand, plus I'm testing with only a single thread.  
>   
> So it looks to me like maybe async_handshake is doing something wrong unless there is a lower-level problem?  
  
Yes they all are on a single strand and also I have ONLE ONE thread for the io_ctx to work with as well.

---

## Comment 46

> Username: gopalak  
> Created at: 2023-08-15 16:10:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1679219869  

@vinniefalco @madmongo1 @klemens-morgenstern So this is what I recent found - still able to see it only under load. Will try to work on a smaller reproducible program but in the mean time - may be the docs are a bit mis leading -  
1. the async_close as part of its e=sequence -> calls async_read -> Issue is there may be another thread which can start an async read as well. The doc says any one of these following async calls can co exists which are  
  
async_read, async_connect, async_read and async_close (viz.) one has to check if there is an existing or pending call of each type before starting another one of the same type. async_close seems to be the only one which sends the close frame and calls async_read internally - this is where the confusion is as the doc (at least to me) seems confusing in the sense that, before calling async_read one needs to check for pending async_read and async_close on the web socket else the ASSERT will be hit.  
  
Thoughts, comments ? pr where am I mis reading the docs ?

---

## Comment 47

> Username: ashtum  
> Created at: 2024-01-04 08:52:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2416#issuecomment-1876724132  

@johannesparty @gopalak The assertion is triggered because you can't initiate any other async operation while async_handshake is not complete, as stated in [the documentation](https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_handshake/overload1.html#beast.ref.boost__beast__websocket__stream.async_handshake.overload1.description):  
  
> The algorithm, known as a composed asynchronous operation, is implemented in terms of calls to the next layer's async_read_some and async_write_some functions. No other operation may be performed on the stream until this operation completes.
