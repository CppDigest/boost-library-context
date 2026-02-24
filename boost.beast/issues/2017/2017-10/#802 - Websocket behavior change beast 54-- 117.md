# #802 - Websocket behavior change beast 54-> 117 [Closed]

> Username: hoditohod  
> Created at: 2017-10-03 07:49:53 UTC  
> Updated at: 2017-10-05 09:26:53 UTC  
> Closed at: 2017-10-05 09:26:53 UTC  
> Url: https://github.com/boostorg/beast/issues/802  

Hi Vinnie,  
I updated my http/websocket server from beast-54 to beast-117 and one my websocket tests fail for 20% of the executions. The test checks the handling of too-large requests.   
  
The server/production code is asynchronous, uses `aync_read()` to read whole messages and `std::enable_shared_from_this/shared_from_this` pattern (?) to keep the socket alive while async operations are pending. `read_message_max()` is called right after `accept()`. When a too-large message is received the read handler correctly gets a `websocket::error::failed` ec and simply returns (causing the session object and socket destruction). Not behavior change on this side.  
  
The client/test code is synchronous, and uses `websocket::stream::read/write`. It writes a message larger than the above configured and waits for the response in a `read()`.  
- For 80% of the cases I get `websocket::error::closed` with close reason `1009 payload to large`. This is what I'm expecting  
- For 20% I get `asio::error::broken_pipe`. This makes my test fail.  
  
  
Actually I could live with either outcome (abrupt socket close or error code delivered) as purpose of the message limit feature is to drop a potentially malicious connection, but why is it undeterministic? Am I using the lib correctly?  
  
[I was thinking: oh the close message has to be sent after the read handler, and that takes time, maybe the socket is destroyed before that. But I couldn't find any way to wait for it. Any further (one more `async_read()` or `close()`) operation on the server side of the socket results in exceptions.]  
  
  
Env:  
Ubuntu 16.04 64bit gcc 5.4.0

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-10-03 12:16:12 UTC  
> Url: https://github.com/boostorg/beast/issues/802#issuecomment-333823675  

>...the close message has to be sent after the read handler  
  
The read handler is called **after** the close message is sent:  
  
First the read operation detects the frame overflow:  
https://github.com/boostorg/beast/blob/7fe74b1bf544a64ecf8985fde44abf88f9902251/include/boost/beast/websocket/impl/read.ipp#L543  
  
Here the close frame is sent:  
https://github.com/boostorg/beast/blob/7fe74b1bf544a64ecf8985fde44abf88f9902251/include/boost/beast/websocket/impl/read.ipp#L616  
  
Then the connection goes through "teardown" (socket shutdown) and finally the handler is called:  
https://github.com/boostorg/beast/blob/7fe74b1bf544a64ecf8985fde44abf88f9902251/include/boost/beast/websocket/impl/read.ipp#L627

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-10-03 12:30:03 UTC  
> Url: https://github.com/boostorg/beast/issues/802#issuecomment-333826565  

I ran this test function and it always outputs "1009"  
```  
    void  
    testIssue802()  
    {  
        for(std::size_t i = 0; i < 1000; ++i)  
        {  
            echo_server es{log, kind::async};  
            boost::asio::io_service ios;  
            stream<test::stream> ws{ios};  
            ws.next_layer().connect(es.stream());  
            ws.handshake("localhost", "/");  
            // too-big message frame indicates payload of 2^64-1  
            boost::asio::write(ws.next_layer(), sbuf(  
                "\x81\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff"));  
            multi_buffer b;  
            error_code ec;  
            ws.read(b, ec);  
            log << ec.message() << "(" << ws.reason().code << ")" << std::endl;  
        }  
    }  
```  
  
This suggests a problem in your code, likely related to lifetime issues.  
  
See: https://github.com/vinniefalco/beast/blob/37b442a53ab6c75578a0c9a93c59953d1ea518f4/test/beast/websocket/read.cpp#L1011

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-10-03 12:47:50 UTC  
> Url: https://github.com/boostorg/beast/issues/802#issuecomment-333830784  

Are you on cpplang slack? Consider joining: https://cpplang.now.sh/ I am in `#boost`

---

## Comment 4

> Username: hoditohod  
> Created at: 2017-10-03 12:57:16 UTC  
> Url: https://github.com/boostorg/beast/issues/802#issuecomment-333833091  

Thanks for the info, I'll try to debug and isolate the issue. You're probably right with object lifetime...

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-10-03 12:59:20 UTC  
> Url: https://github.com/boostorg/beast/issues/802#issuecomment-333833641  

FYI, this `test::stream` and `echo_server` in the websocket test code is new. It took me over a week to develop and there were *many* bugs related to lifetime and race conditions. My point is that getting this just right for tests is definitely non-trivial.

---

## Comment 6

> Username: hoditohod  
> Created at: 2017-10-04 10:01:53 UTC  
> Url: https://github.com/boostorg/beast/issues/802#issuecomment-334108399  

Hi,  
I placed a sleep in my test that makes the issue deterministic, then I followed what happens with the debugger.  Here's the sequence of events after the handshake is complete (server and client runs on different threads with different io_service instances):  
  
    [test/client] (sync) write a too large message  
    [prod/server] (async) recv message, send close frame, teardown, call read handler with "protocol violation"  
    [prod/server] handler returns w/o new async op init, websocket objects gets destructed (owns the underlying socket)  
    [sleep 100ms]  
    [test/client] (sync) read message -> read_some(buf, lim, ec) -> read_some(mbs, ec): reads close frame with cr.code=1009 ->do_fail(1009, error::closed, ec) tries to send a close frame, but boost::asio::write returns with ec=32 broken_pipe  
  
So as I understand the the websocket::stream::read correctly receives the close frame, and it wants to **send** another which fails due to socket already closed. Unfortunately, I don't know much (anything) about the websocket protocol, so I don't know how things should happen, but writing to the closed socket doesn't sound good (though the client is not yet aware that it's closed).

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-10-04 13:53:16 UTC  
> Url: https://github.com/boostorg/beast/issues/802#issuecomment-334162393  

>I placed a sleep in my test that makes the issue deterministic  
  
Brilliant!  
  
>So as I understand the the websocket::stream::read correctly receives the close frame, and it wants to send another which fails due to socket already closed.  
  
Ahh!! Yes...unfortunately, this sounds like the correct behavior. I'll explain rfc6455 and maybe we can figure out what to do, if anything. For a normal websocket closure, one peer sends a close frame. Then it continues reading frames until it receives a close frame. On the other end, the peer receives a close frame, then it immediately sends a close frame and shuts down the socket. That's the normal closure.  
  
However, there is also the abnormal closure to handle the case of a protocol violation, which is the scenario that you are encountering. Because the server received a frame which violated the protocol, it sends the close frame but then immediately performs the "Close the WebSocket Connection" algorithm instead of responding with a close frame first. The client of course has no way to know that the server doesn't plan on reading any more frames. Thus, you get the "broken pipe" error.  
  
Here is the documentation for handling Close  
https://tools.ietf.org/html/rfc6455#section-5.5.1  
  
Note the language:  
*"If an endpoint receives a Close frame and did not previously send a Close frame, the endpoint MUST send a Close frame in response."*  
  
I'm not exactly sure how to handle this, but one solution is to modify `stream::check_ok` in `<boost/beast/websocket/stream.hpp>`:  
```  
    bool  
    check_ok(error_code& ec)  
    {  
        if(ec)  
        {  
            if(rd_close_)  
                status_ = status::closed; // happens when the other end closes the socket  
            else if(status_ != status::closed)  
                status_ = status::failed;  
            return false;  
        }  
        return true;  
    }  
```  
  
You could try this and see how it goes. This could have other consequences, we'd have to think it through.

---

## Comment 8

> Username: hoditohod  
> Created at: 2017-10-05 09:16:27 UTC  
> Url: https://github.com/boostorg/beast/issues/802#issuecomment-334408204  

Hi Vinnie,  
Thanks for the detailed reply! The most important thing to state: I'm completely fine with the current behavior. I opened the Issue because randomly failing tests are mostly the sign of race conditions, object lifetime issues, or other hard to trace down bugs (at my side). Especially in mutithreaded async environments. I wanted to fully understand what happens.  
  
As you wrote Beast is correct and works as specified. I don't think there's need for tricky hacks in the code to handle such an exceptional case. Message size limiting in our app is a security measure to get rid of malicious peers as soon as possible, and it works as expected. From testing point of view it's also acceptable to have 2 outcomes depending the sequencing of events, given both outcomes are correct.  
  
Long story short, I'm ok with the current behavior, and updated my tests. I close the Issue if it's ok for you.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-10-05 09:21:43 UTC  
> Url: https://github.com/boostorg/beast/issues/802#issuecomment-334409471  

Okay. We can leave it alone for now and monitor it. If the problem comes up again we can take a second look, thanks for the report!
