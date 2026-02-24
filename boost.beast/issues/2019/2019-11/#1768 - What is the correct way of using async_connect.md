# #1768 - What is the correct way of using async_connect [Closed]

> Username: tty2099  
> Created at: 2019-11-18 08:15:53 UTC  
> Updated at: 2019-12-26 09:19:01 UTC  
> Closed at: 2019-12-26 09:19:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1768  

### Version of Beast 266  
  
### Steps necessary to reproduce the problem  
 one server and one client in the same class as member variables, they are follow official websocket async examples. The program run the server part first and then waiting for client of the server to be connected. when the client connected, this program will connect to another server.  
  
I use the same io_context for both part. When I connect to another server first, and then start the server in the same class, then everything sames OK. But I start the server first and then start the client connection to another server, it crashes. The debugger reports that the value of write.pending is true, which value should be false, so assert condition is not satisfied.  
  
### All relevant compiler information  
  
I test my program in Windows and Linux,      on base_stream.hpp:433, crash on the line  , pg1_(impl_->write.pending)  
  
I can't post my source here, because it is private and is not allowed to publish anywhere

---

## Comment 1

> Username: leleftheriades  
> Created at: 2019-11-18 09:31:16 UTC  
> Updated at: 2019-11-18 09:34:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1768#issuecomment-554931982  

you are not allowed a second write on the same socket before the first one finishes. Without code it's hard to guess if you are using the api correctly. If you can create an example that reproduces the issue it would allow for people to help more.

---

## Comment 2

> Username: leleftheriades  
> Created at: 2019-11-18 10:16:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1768#issuecomment-554949604  

server part has one socket for accepting, and one socket per accepted connection correct?

---

## Comment 3

> Username: tty2099  
> Created at: 2019-11-19 08:37:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1768#issuecomment-555393218  

I take a mistake on program logic that triggers unnecessary reconnnection to another server.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-12-19 09:08:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1768#issuecomment-567401862  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2019-12-26 09:19:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1768#issuecomment-569020877  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
