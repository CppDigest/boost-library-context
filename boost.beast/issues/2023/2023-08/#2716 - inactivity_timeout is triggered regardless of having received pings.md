# #2716 - inactivity_timeout is triggered regardless of having received pings [Closed]

> Username: xim  
> Created at: 2023-08-10 20:11:29 UTC  
> Updated at: 2025-02-09 14:55:11 UTC  
> Closed at: 2025-02-09 14:55:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2716  

### Version of Beast  
  
347  
  
### Steps necessary to reproduce the problem  
  
I'm using plain, async `boost::beast::websocket` for a server and a client.  
  
I have a server that's sending ping packets at a 15s interval and keeping the connection alive correctly. I have verified that the client receives these and responds with pongs.  
  
Since I have these ping packets going, I've added a timeout in the client to try to detect if the connection goes down:  
  
```cpp  
ws_.set_option(boost::beast::websocket::stream_base::timeout{  
    60s,  
    30s,  
    false  
});  
```  
  
I had assumed that this would be kept alive by the ping packets sent from the server, but it isn't. The client disconnects with the message `The socket was closed due to a timeout`.  
  
Setting the final flag (keep_alive_pings) to `true` makes the client keep the connection alive by doing the same as the server's already doing. Given the server set to send ping frames at 15s intervals, this setting on the client:  
  
```cpp  
ws_.set_option(boost::beast::websocket::stream_base::timeout{  
    60s,  
    80s,  
    true  
});  
```  
  
Means I'll get this behaviour:  
  
```cpp  
00:00 Client connects  
00:15 Server pings as expected  
00:15 Client pongs as required  
00:30 Server pings as expected  
00:30 Client pongs as required  
00:40 Client pings despite having seen activity in the form of a ping  
```  
  
I feel is the desired behaviour would be to count incoming ping frames as activity. If that can't be made the default behaviour, could we try to add it as a config on `boost::beast::websocket::stream_base::timeout`?

---

## Comment 1

> Username: xim  
> Created at: 2023-08-10 21:16:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2716#issuecomment-1673931576  

I made a somewhat-minimal reproduction at https://gist.github.com/xim/06d60c455363fbb764c14f21d7705c40  
  
Compiled and run by running each in a separate terminal:  
  
```  
g++ -g -std=gnu++17 websocket_server_async.cpp && echo running && ./a.out 127.0.0.1 8080 1  
g++ -g -std=gnu++17 websocket_client_async.cpp && ./a.out 127.0.0.1 8080 'test'  
```

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-08-11 00:39:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2716#issuecomment-1674090642  

What are you using as the tcp stream? Are you calling async_read perpetually?

---

## Comment 3

> Username: xim  
> Created at: 2023-08-11 04:47:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2716#issuecomment-1674207167  

As shown in the gist, I made a somewhat-minimal reproduction with the example websocket_client_async.cpp as a base.  
  
It's a `websocket::stream<beast::tcp_stream>` and I re-call `async_read` in the `on_read` handler

---

## Comment 4

> Username: xim  
> Created at: 2023-08-11 07:07:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2716#issuecomment-1674298307  

Full code instead of patch at https://gist.github.com/xim/97874f6d7e27402f26228a0da6448236  
  
Here, the server sends pings ever 1s and the client has a 5s timeout.  
  
Client output:  
```  
ping  
ping  
ping  
ping  
read: The socket was closed due to a timeout  
```  
  
Server output:  
```  
pong  
pong  
pong  
pong  
read: End of file  
```

---

## Comment 5

> Username: xim  
> Created at: 2023-08-11 12:22:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2716#issuecomment-1674674681  

Made a PR. If you want this to be configurable, I can have a stab at that as well.

---

## Comment 6

> Username: ashtum  
> Created at: 2023-08-19 08:19:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2716#issuecomment-1684890779  

I couldn't find any indication in [RFC 6455](https://datatracker.ietf.org/doc/html/rfc6455) that either permits or prohibits the counting of control frames as a form of activity. Considering that this approach could potentially eliminate the need for extra ping-pong messages every n seconds for clients with enabled keep_alive_pings, introducing an additional parameter to the configuration could unnecessarily complicate matters for a feature that might see very limited use.  
  
The only plausible options appear to be either counting pings as activity (assuming the standard allows for it), or maintaining the status quo. Users could opt to utilize the low-level API and manually implement ping-pong if they wish to minimize the transmission of extra ping-pong messages.

---

## Comment 7

> Username: xim  
> Created at: 2023-08-25 06:48:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2716#issuecomment-1692855495  

> I couldn't find any indication in [RFC 6455](https://datatracker.ietf.org/doc/html/rfc6455) that either permits or prohibits the counting of control frames as a form of activity  
  
Agree, the most precise is the phrasing `A Ping frame may serve either as a keepalive or as a means to verify that the remote endpoint is still responsive.`  
  
IMHO it makes sense for control frames to count as activity.  
  
> this approach could potentially eliminate the need for extra ping-pong messages every n seconds for clients with enabled keep_alive_pings  
  
Exactly, good summary there.  
  
> The only plausible options appear to be either counting pings as activity (assuming the standard allows for it), or maintaining the status quo  
  
My vote is on the former =)

---

## Comment 8

> Username: vinniefalco  
> Created at: 2023-08-25 17:39:02 UTC  
> Updated at: 2023-08-25 17:41:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2716#issuecomment-1693717500  

Without thinking about it too much, I believe received pings should reset the timer, as they represent activity.  
  
Regardless of what is changed, the library documentation should be updated to describe either the current or the new behavior of received pings with respect to timeouts if it is not already explained.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2023-08-25 17:42:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2716#issuecomment-1693722984  

> assuming the standard allows for it  
  
The specifications say nothing about what counts as idle activity. Ideally Beast would have great defaults that work for most people, while also allowing for the caller to have perfect control over customizing that behavior. However Beast has reaching close to the end of what can be done without incurring technical debt, so this ideal will likely only be approached in a new websocket library which will replace beast (this work has not started yet).

---

## Comment 10

> Username: xim  
> Created at: 2023-08-29 12:56:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2716#issuecomment-1697395066  

> Without thinking about it too much, I believe received pings should reset the timer, as they represent activity.  
  
Does that mean we should proceed towards merging the PR, @vinniefalco? I can make additional adjustments or try to document more inline if you wish!

---

## Comment 11

> Username: ashtum  
> Created at: 2023-08-29 13:42:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2716#issuecomment-1697473060  

@xim It would be great if you could add the relevant test cases to the PR; This is the appropriate location for including them: https://github.com/boostorg/beast/blob/develop/test/beast/websocket/ping.cpp.

---

## Comment 12

> Username: xim  
> Created at: 2023-08-30 08:26:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2716#issuecomment-1698719943  

I don't see how I can test this without actually waiting for timers to expire. I mean, this test passes, but sleeping in the test like this is probably not OK?  
  
```cpp  
echo_server es{log};  
error_code ec;  
stream<test::stream> ws{ioc_};  
ws.set_option(stream_base::timeout{  
    stream_base::none(),  
    std::chrono::milliseconds(100),  
    false  
});  
ws.next_layer().connect(es.stream());  
ws.handshake("localhost", "/");  
flat_buffer b;  
bool got_timeout = false;  
ws.async_read(b,  
    [&](error_code ec, std::size_t)  
    {  
        std::cerr << ec.message() << std::endl;  
        if(ec != beast::error::timeout)  
            BOOST_THROW_EXCEPTION(  
                system_error{ec});  
        got_timeout = true;  
    });  
BEAST_EXPECT(ws.impl_->idle_counter == 0);  
ioc_.run_for(std::chrono::milliseconds(60));  
ioc_.restart();  
BEAST_EXPECT(ws.impl_->idle_counter == 1);  
ws.ping("", ec);  
if(ec)  
    BOOST_THROW_EXCEPTION(  
        system_error{ec});  
ioc_.run_for(std::chrono::milliseconds(10));  
ioc_.restart();  
BEAST_EXPECT(ws.impl_->idle_counter == 0);  
BEAST_EXPECT(!got_timeout);  
ioc_.run_for(std::chrono::milliseconds(100));  
ioc_.restart();  
BEAST_EXPECT(got_timeout);  
```

---

## Comment 13

> Username: ashtum  
> Created at: 2023-08-30 08:57:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2716#issuecomment-1698770019  

It seems this how is how timeout cases are tested in the entire code base (searching for `stream_base::timeout` or `test::run_for`).  
Please ensure that you allocate a sufficient margin to prevent false failures caused by OS rescheduling; examining other test cases can provide a reference for determining the appropriate margin.  
@klemens-morgenstern is this the right way?
