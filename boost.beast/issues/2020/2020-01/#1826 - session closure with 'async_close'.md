# #1826 - session closure with 'async_close' [Closed]

> Username: jpramosi  
> Created at: 2020-01-27 16:40:00 UTC  
> Updated at: 2020-02-07 15:31:23 UTC  
> Closed at: 2020-02-07 15:31:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1826  

Hello!  
At first i would like to thank you for the library & the offered examples.  
  
I'm using beast for a while as a rpc solution for my projects, but i encountered a problem with   
'async_close' on server-side with its sessions on Boost 1.70.0.  
During the stress tests a few sessions hang while waiting for 'async_close' to finish, normaly it should timeout by the internal websocket timer.  
  
The strange thing is, the stuck only happened with 'websocket::stream<boost::beast::tcp_stream>' but not with 'websocket::stream<boost::beast::ssl_stream<boost::beast::tcp_stream>>'.  
Seeking for a solution i tried to cancel all operations and let the session run out of tasks  
On both stream types.   
It seemed to work out great. I followed the debugger and the relevant functions such as 'async_shutdown' was called. Stress tests running for about a half week, nothing emerged.  
  
Later i searched about the issue and i have found the problems regarding  'async_close' in 1.70.0. So i updated the project to 1.72.0 and ran the tests again, successfully.  
  
I know it would be better to gracefully close the sessions by using 'async_close' and letting them do the closing handshake, but now i wonder whether it is still valid to cancel all operations and let the sessions run out of tasks with ssl / plain streams or i'm running into security risks concerning ssl?  
  
Thank you

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-01-27 16:53:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1826#issuecomment-578843392  

Hi @reapler,  
  
Closing the websocket gracefully is the best option. One of the drivers for the beast team to create the tcp_stream was precisely because of the difficulty in dealing with timeouts correctly.  
  
Canceling the websocket's underlying io objects will not necesarily lead to a security issue, but it might result in the remote peer being surprised by the sudden peer reset.  
  
To be clear, are you saying that the original program, with graceful closing of websockets, experiences no problems when compiled with boost.beast 1.72 but exhibits problems when compiled with boost.beast 1.70?

---

## Comment 2

> Username: jpramosi  
> Created at: 2020-01-27 17:10:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1826#issuecomment-578851727  

Thanks for the fast answer, the original program with the gracefully closure was only tested on 1.70.0.  
I think i switch back to async_close and test it again with 1.72.0 and report my findings.

---

## Comment 3

> Username: jpramosi  
> Created at: 2020-02-05 09:17:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1826#issuecomment-582313269  

Ok, the stress tests running for a few days, everything seems to be fine with async_close.
