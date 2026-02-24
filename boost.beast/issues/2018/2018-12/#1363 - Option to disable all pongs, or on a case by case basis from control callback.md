# #1363 - Option to disable all pongs, or on a case by case basis from control callback [Closed]

> Username: qduyang  
> Created at: 2018-12-10 12:18:04 UTC  
> Updated at: 2023-12-31 09:37:28 UTC  
> Closed at: 2023-12-31 09:37:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1363  

### Version of Beast  
  
BEAST version 181, boost 1.68  
  
### Steps necessary to reproduce the problem  
  
N/A  
  
### All relevant compiler information  
  
N/A  
  
As for server security reason, we'd like a way to avoid websocket ping flooding from unfriendly clients. we may need to,  
1. Allow to disable automatic ping responding  
2. Allow to ignore ping request selectively  
3. Allow throttling control on the ping requests, and even other messages.

---

## Comment 1

> Username: reddwarf69  
> Created at: 2018-12-10 13:51:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1363#issuecomment-445821753  

If the unfriendly client is just buggy it will most likely expect to receive a pong. Not sending a pong would be basically equivalent to closing the connection.  
If the unfriendly client is malicious you want to close the connection.  
  
So, can't you use websocket::stream::control_callback to look at how often you receive those pings and close the connection if it's more often that what you would like?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-12-10 15:13:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1363#issuecomment-445850397  

> As for server security reason, we'd like a way to avoid websocket ping flooding from unfriendly clients.  
  
Well, I understand what you are trying to do but there are almost an infinite number of ways for malicious peers to abuse the connection. Even if we address this problem, you will still need to write code to detect bad behavior and disconnect the host, possibly adding them to a temporary or permanent IP blacklist.  
  
> Allow to disable automatic ping responding  
> Allow to ignore ping request selectively  
  
How about if the "control callback" can return a `bool` to indicate whether or not the implementation should respond? It seems this would cover your case  
  
> Allow throttling control on the ping requests, and even other messages.  
  
I'm not sure how we could do that. There is no way to prevent the remote host from sending messages. You can, however, choose not to receive them (by not reading). Or you can control the rate that you receive the message data, by using the overloads of read_some which accept a mutable buffer sequence, or by using the overloads of read_some which let you specify a limit on the number of bytes to transact, combined with a timer:  
  
https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/read_some/overload2.html  
https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/read_some/overload4.html  
https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_read_some/overload1.html  
https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_read_some/overload2.html  
  
For control frames (ping, pong) you can do as @RedDwarf69 suggested which is to measure the incoming rate of control frame messages and disconnect if it exceeds a threshold.  
  
Another option is to write your own stream wrapper which implements adjustable bandwidth limits using a timer. Then use the wrapper for the NextLayer when instantiating the websocket stream, like this:  
  
```  
boost::beast::websocket::stream<throttled_stream<boost::asio::ip::tcp::socket>> ws(ios);  
```  
  
`throttled_stream` is your wrapper which implements `async_read_some` and `async_write_some` to meet the requirements of _AsyncReadStream_ and _AsyncWriteStream_, which supports bandwidth control. This is something I will probably add to Beast at some point (in fact, I will open an issue for it).

---

## Comment 3

> Username: qduyang  
> Created at: 2018-12-11 03:02:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1363#issuecomment-446055424  

Thanks @RedDwarf69 and @vinniefalco , it is a good way monitor the rate and disconnect the client if over limit. While for my case, I prefer to delay responding to the client. But yes, a control callback with 'bool' return value can solve my problem and let me handle the responding in user code.  
  
@vinniefalco throttler is usually a mandatory component of a server implementation, It is great we could have a built-in throttling feature. I also have a throttler in my code and simply use it to monitor request rates per-second. Though usually actively close the session is a better choice, I chose to delay responding if limit exceeded.

---

## Comment 4

> Username: qduyang  
> Created at: 2018-12-11 03:17:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1363#issuecomment-446057895  

@vinniefalco, it seems strand.wrap requires a void returned handler, so we can't use strand.wrap on the control callback if the it has a bool return value. In my case, It is fine to just allow disable the auto-pong and let my user code handle if pong or not. Is it possible to add the option to websocket::stream::set_option?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-12-11 04:03:02 UTC  
> Updated at: 2018-12-11 04:03:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1363#issuecomment-446064872  

> we can't use strand.wrap on the control callback   
  
The control callback is not a completion handler. The documentation states:  
  
_"The implementation type-erases the callback which may require a dynamic allocation. To prevent the possibility of a dynamic allocation, use std::ref to wrap the callback. If the read operation which receives the control frame is an asynchronous operation, **the callback will be invoked using the same method as that used to invoke the final handler**."_  
  
https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/control_callback/overload1.html  
  
This means that if you have used a strand to wrap the completion handler submitted in an asynchronous read operation, then the control callback will be called from the strand.  
  
> Is it possible to add the option to websocket::stream::set_option?  
  
Yes I think that is possible, but Beast will still have to read the frame.

---

## Comment 6

> Username: qduyang  
> Created at: 2018-12-11 06:35:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1363#issuecomment-446089003  

Thanks for your detail explanation.

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2022-09-24 06:09:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1363#issuecomment-1256876560  

Is this resolved?
