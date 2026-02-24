# #1850 - client address details. [Closed]

> Username: sainishanthv7698  
> Created at: 2020-02-19 04:34:26 UTC  
> Updated at: 2020-04-08 16:33:46 UTC  
> Closed at: 2020-04-08 16:33:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1850  

I want to build a  messaging application using websocket server. I have gone through sample_server( attached below) code which echoes back the message but Instead how do i send to specific existing users. Could you please help me with it. Also how to store the address of client once connection is established so that it could be contacted when preferred by server.  
[sample_server.txt](https://github.com/boostorg/beast/files/4223116/sample_server.txt)

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-02-19 09:20:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1850#issuecomment-588117557  

Hi @sainishanthv7698   
  
Happy to help. I’m out of the office at the moment but will address this today.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-02-19 15:59:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1850#issuecomment-588293809  

websocket-chat-multi shows how to broadcast messages to connected clients:  
https://github.com/boostorg/beast/tree/develop/example/websocket/server/chat-multi

---

## Comment 3

> Username: sainishanthv7698  
> Created at: 2020-02-19 19:00:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1850#issuecomment-588389679  

> WebSocket-chat-multi shows how to broadcast messages to connected clients:  
> https://github.com/boostorg/beast/tree/develop/example/websocket/server/chat-multi  
  
This code posts data all of the connected clients whereas I am trying to send messages to selected few clients and not all.

---

## Comment 4

> Username: sainishanthv7698  
> Created at: 2020-02-19 19:02:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1850#issuecomment-588390738  

Is there something like connection-pointer which can store connection details and can be used to send data to the client at any point of time

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-02-19 19:13:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1850#issuecomment-588396075  

Yes, obviously - that is the `websocket_session`  
https://github.com/boostorg/beast/blob/develop/example/websocket/server/chat-multi/websocket_session.hpp#L27

---

## Comment 6

> Username: sainishanthv7698  
> Created at: 2020-02-20 14:03:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1850#issuecomment-589052728  

pardon me if my doubts appear little trivial,   
how do I run **_chat-multi_** ( i have cloned from Github, now I want to run same code in visual studio) in visual studio 2019?.  
  
What I am trying to do currently is as follows  
on_accept ([https://github.com/boostorg/beast/blob/develop/example/websocket/server/chat-multi/websocket_session.hpp#L42], I should be able to store the connection details to be used in the future.

---

## Comment 7

> Username: madmongo1  
> Created at: 2020-02-20 14:18:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1850#issuecomment-589073490  

Hi @sainishanthv7698,  
  
To build the example you could just add the relevant files to a visual studion project and make the appropriate settings so that the boost headers can be found.  
  
To answer your question about isolating a given websocket session, note that each websocket connection is maintained by a shared_ptr to a websocket_session.  
  
This pointer is created in the listener class during on_accept.  
  
```  
        boost::make_shared<http_session>(  
            std::move(socket),  
            state_)->run();  
```  
  
note that the code here disards this pointer after runing the session. The session captures its own controlling shared_ptr during each sync operation so the session does not die until it has ceased to run.  
  
However, at this point it would be perfectly aceptable to store a weak pointer to the session in a map (for eample) indexed by the remote_endpoint of the underlying tcp connection.  
  
this can be acquired with a call to:  
  
```  
boost::beast::error_code ec;  
auto remote = ws_.next_layer().remote_endpoint(ec);  
// remote.address() is the ip address of the remote host  
// remote.port() is the port on which the remote end connected to you  
// the error code check is important because the call can fail if the connection has already disconnected  
```

---

## Comment 8

> Username: sainishanthv7698  
> Created at: 2020-02-20 14:33:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1850#issuecomment-589080627  

Thank you I am able to run chat-multi code through visual studio 2019. I got the gist of it. I will try and implement it.

---

## Comment 9

> Username: madmongo1  
> Created at: 2020-02-20 14:46:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1850#issuecomment-589086552  

Great news. If you get stuck you can always get in touch on slack.   
  
http://slack.cpp.al/  
channel: #beast

---

## Comment 10

> Username: sainishanthv7698  
> Created at: 2020-02-21 11:05:19 UTC  
> Updated at: 2020-02-21 11:08:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1850#issuecomment-589607648  

`auto remote = ws_.next_layer().remote_endpoint(ec1);`  
  
'remote_endpoint': is not a member of boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor,boost::beast::unlimited_rate_policy>'. I am getting this error when I am trying to store weak pointer using above call

---

## Comment 11

> Username: madmongo1  
> Created at: 2020-02-21 13:29:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1850#issuecomment-589653314  

Hi @sainishanthv7698. I see you’re using the beast basic stream. This adds another stream layer so you’ll need another call to next_layer():  
  
ws.next_layer().next_layer().remote_endpoint();

---

## Comment 12

> Username: madmongo1  
> Created at: 2020-02-21 13:56:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1850#issuecomment-589663112  

Sorry @sainishanthv7698 I have misled you. The basic_stream does not have next_layer, it has the member socket():  
  
```  
    boost::beast::error_code ec;  
  
    boost::asio::io_context ioc;  
  
    auto s = boost::asio::ip::tcp::socket(ioc);  
    auto e = s.remote_endpoint(ec);  
  
    auto bs = boost::beast::basic_stream<boost::asio::ip::tcp>(ioc);  
    e = bs.socket().remote_endpoint(ec);  
  
    auto wbs = boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp>>(ioc);  
    e = wbs.next_layer().socket().remote_endpoint(ec);  
```  
  
https://godbolt.org/z/Kku7PG

---

## Comment 13

> Username: zlojvavan  
> Created at: 2020-02-21 14:22:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1850#issuecomment-589673532  

may be one should use get_lowest_layer()

---

## Comment 14

> Username: sainishanthv7698  
> Created at: 2020-03-02 13:37:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1850#issuecomment-593405775  

Hello,  
  
I have 2 issues   
1) What dataType is `s.remote_endpoint(ec);` sot that I can create custom dataType and Index it.  
2) How do I use this at later stages to send messages?

---

## Comment 15

> Username: vinniefalco  
> Created at: 2020-03-02 14:28:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1850#issuecomment-593429096  

There is a case to be made that examples should not use `auto`.

---

## Comment 16

> Username: madmongo1  
> Created at: 2020-03-02 15:34:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1850#issuecomment-593461667  

> What dataType is s.remote_endpoint(ec); sot that I can create custom dataType and Index it.  
  
For a socket `s` of type `S`, the result of `s.remote_endpoint()` will be of type `S::endpoint_type`  
  
https://www.boost.org/doc/libs/1_72_0/doc/html/boost_asio/reference/basic_stream_socket/remote_endpoint.html  
  
In general, for any given `protocol` (such as `boost::asio::ip::tcp`), the following type relationships exist:  
`protocol::socket` represents a socket of that protocol  
`protocol::endpoint` represents an endpoint (.e.g address/port pair for ip protocols, a path for unix sockets, etc)  
`protocol::socket::endpoint_type` is the same as `protocol::endpoint`

---

## Comment 17

> Username: stale[bot]  
> Created at: 2020-04-01 16:11:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1850#issuecomment-607343918  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 18

> Username: stale[bot]  
> Created at: 2020-04-08 16:33:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1850#issuecomment-611061567  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
