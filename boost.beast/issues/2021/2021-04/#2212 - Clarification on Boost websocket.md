# #2212 - Clarification on Boost websocket. [Closed]

> Username: edineshkumar  
> Created at: 2021-04-07 17:26:56 UTC  
> Updated at: 2021-04-20 13:14:11 UTC  
> Closed at: 2021-04-20 13:14:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2212  

I am using **Boost** opensource library for creating a websocket in my application. Now my need is, after certain time duration, websocket need to be closed.  
  
**Example:**  After 30 minutes, websocket should gracefully closed at any cost.  
  
Is this possible with Boost library?  
If yes, please help me out to configure the websocket's session timeout and achieve my requirements.  
  
Thanks for advance.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-04-07 17:53:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2212#issuecomment-815107375  

Hi there. You would use an `asio::steady_timer` and initiate an async_wait once the connection was established.  
Once the timer times out (completion handler is executed with no error), you would call `async_close` on the websocket.   
Your `async_read` loop should continue until the completion handler indicates an error.  
At this point you can destroy your connection object.

---

## Comment 2

> Username: edineshkumar  
> Created at: 2021-04-08 10:36:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2212#issuecomment-815652638  

Thanks for your response.,  
  
Actually in my application already open handler, message handler, close handler and error handler were implemented, which is used it for my application.   
  
Now I am trying to add additional way to closing the websocket, (i.e.) called as session timeout.  
  
Any example is more helpful for me.

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-04-08 12:38:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2212#issuecomment-815775799  

Can you tell me the rules for your timeout? Is it an inactivity timeout or an overall session lifetime timeout?  
I’ll write a small example

---

## Comment 4

> Username: edineshkumar  
> Created at: 2021-04-08 13:16:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2212#issuecomment-815816086  

Overall Session lifetime Timeout .  
  
On Thu, Apr 8, 2021, 6:09 PM Richard Hodges ***@***.***>  
wrote:  
  
> Can you tell me the rules for your timeout? Is it an inactivity timeout or  
> an overall session lifetime timeout?  
> I’ll write a small example  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2212#issuecomment-815775799>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AJ77QMUUDBSX52XHBLBLNF3THWPWVANCNFSM42RGWF4Q>  
> .  
>

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-04-08 15:36:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2212#issuecomment-815922906  

ok creating a little project now

---

## Comment 6

> Username: madmongo1  
> Created at: 2021-04-08 22:23:21 UTC  
> Updated at: 2021-04-08 22:24:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2212#issuecomment-816271338  

I have added a 30s session timeout to a demo websocket chat server I wrote some time ago.  
[https://github.com/test-scenarios/boost_beast_websocket_echo/tree/master/pre-cxx20/echo_server](https://github.com/test-scenarios/boost_beast_websocket_echo/tree/master/pre-cxx20/echo_server)  
  
see connection.hpp and connection.cpp  
  
The client will be informed of the remaining session time every 5 seconds.  
  
The server also supports detection of interrupt via ctrl-c on the console.

---

## Comment 7

> Username: edineshkumar  
> Created at: 2021-04-20 13:14:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2212#issuecomment-823263991  

Your example was more useful for my implementation. Very thank you for your responds and quick replay.  
  
Closing the issue.
