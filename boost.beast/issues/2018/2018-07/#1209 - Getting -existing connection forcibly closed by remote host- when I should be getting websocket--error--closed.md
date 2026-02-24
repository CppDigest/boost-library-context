# #1209 - Getting "existing connection forcibly closed by remote host" when I should be getting websocket::error::closed [Closed]

> Username: emag37  
> Created at: 2018-07-26 02:04:56 UTC  
> Updated at: 2018-09-13 13:21:21 UTC  
> Closed at: 2018-09-13 13:21:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1209  

Hello,  
  
I have a simple test setup where I have a simple secure websocket server running in Tornado websockets in Python 2.7, and a C++ websocket client using the version of boost::beast included with the Boost 1.67.0 release. The connection is made over SSL with TLSv1.2. I'm compiling with MSVC 19.14.26433.0 x86_64 (32 bit compiler for 64 bit host)   
  
My C++ client calls async_read and waits for data. When it receives data, it checks for any errors then fires a callback with said data. It then starts another async_read.  
  
From what I understand, when I call close() on the server-side websocket connection, the async_read callback on the C++ side should gracefully intercept and respond to the websocket 'close' frame, and callback with the error code set to websocket::error::closed.   
  
However, my async_read callback always seems to have "An existing connection was forcibly closed by the remote host" set as the error. I've also seen it as "stream truncated" a couple of times.  
  
I registered a control_callback, and am able to see the 'close' frame being received there, so I'm not sure why async_read does not have the correct error message.  
  
Is this a known issue? Am I doing something wrong? Thank you!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-07-26 03:47:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1209#issuecomment-407967619  

> From what I understand, when I call close() on the server-side websocket connection, the async_read callback on the C++ side should gracefully intercept and respond to the websocket 'close' frame, and callback with the error code set to websocket::error::closed.  
  
Yeah you have that all correct. `ECONNRESET` ("An existing connection was forcibly closed by the remote host") means exactly what it says. The remote host (Tornado websockets in this case) reset the connection.  
  
And if we look at the source code[1] for Tornado websockets we find this:  
```  
# Give the client a few seconds to complete a clean shutdown,  
# otherwise just close the connection.  
self._waiting = self.stream.io_loop.add_timeout(  
    self.stream.io_loop.time() + 5, self._abort)  
```  
  
Ho ho! What's this? A call to `self_.abort()`? Not best practices :) Maybe on the Beast client side you are still writing data? Or for some other reason there is a delay? What happens when you don't use SSL?  
  
[1] http://www.tornadoweb.org/en/stable/_modules/tornado/websocket.html

---

## Comment 2

> Username: emag37  
> Created at: 2018-07-26 14:32:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1209#issuecomment-408117788  

Hi Vinnie, I appreciate the quick and detailed response!  
  
So I did some more testing: I compiled simple test apps using the provided example applications:  
  
https://www.boost.org/doc/libs/1_67_0/libs/beast/example/websocket/client/  
  
With sync and async clients and no SSL, I get the `websocket::error::closed` as expected.  
  
With the async SSL client, I get the "An existing connection was forcibly closed by the remote host" error.  
  
I timed the delay between receiving the 'close' control frame and getting the `async_read` callback - it is in the order of microseconds, so Tornado is not calling `self_.abort()` due to the timeout delay. Also, Tornado calls its `on_close` handler, so it is getting the 'close' frame from Beast as expected. I have a funny feeling Tornado is killing the SSL stream too quickly when it gets the 'close' frame, which might cause Beast to give the wrong error.  
  
Anyways, not a big deal - since I can consistently catch the close frame in the `control_callback` I'll set a flag to override the error.  
  
I have attached my Tornado test server, just send it a 'close' string and it will initiate the close from the server-side.  
  
[websocket_server_test.zip](https://github.com/boostorg/beast/files/2232392/websocket_server_test.zip)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-07-26 15:45:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1209#issuecomment-408142181  

> I have a funny feeling Tornado is killing the SSL stream too quickly when it gets the 'close' frame, which might cause Beast to give the wrong error.  
  
I think that is precisely what is happening. Consider trying a few other servers. You might also try a beast SSL websocket server. Google servers in particular love to close the socket without finishing the SSL shutdown handshake.

---

## Comment 4

> Username: emag37  
> Created at: 2018-07-26 17:21:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1209#issuecomment-408171202  

I actually had tried it with a synchronous boost::beast websocket server and I did get the correct error code. Not entirely sure who's court the bug lands in- whether Tornado shouldn't close the socket so quickly, or if boost::beast should preserve the `close` error code even if the SSL stream closes. Anyways, I guess I'll just hook onto  `control_callback` for now. Thanks!

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-07-26 17:25:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1209#issuecomment-408172263  

> preserve the close error code even if the SSL stream close  
  
Definitely not, because this would mask a man in the middle attack on the closing handshake.

---

## Comment 6

> Username: zlojvavan  
> Created at: 2018-08-07 12:41:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1209#issuecomment-411042237  

> I've also seen it as "stream truncated" a couple of times  
  
could you clarify please? what precise error code and message you get? in my app I sometimes can see ec.value()=2 and ec.message() returning "End of file" in void fail(boost::system::error_code ec, char const* what) handler

---

## Comment 7

> Username: stale[bot]  
> Created at: 2018-09-06 13:01:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1209#issuecomment-419084293  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 8

> Username: stale[bot]  
> Created at: 2018-09-13 13:21:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1209#issuecomment-421004509  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
