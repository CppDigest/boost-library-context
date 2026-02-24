# #2276 - More complete SSL websocket example [Closed]

> Username: gitgitwhat  
> Created at: 2021-07-04 04:40:03 UTC  
> Updated at: 2024-02-14 15:46:34 UTC  
> Closed at: 2024-02-14 15:46:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2276  

I'm wondering if there are more complete examples available for SSL websockets besides just sending a single message and shutting down.  For example, how do you keep the connection alive after the on_handshake?  And does a message have to be sent at that time?  I have a pretty typical websocket client use case but just having trouble with some of the boost::beast basics.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-07-04 04:42:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2276#issuecomment-873520731  

Timeouts and keep-alives are not specific to SSL connections. Have you had a look at this? https://www.boost.org/doc/libs/1_76_0/libs/beast/doc/html/beast/using_websocket/timeouts.html

---

## Comment 2

> Username: gitgitwhat  
> Created at: 2021-07-04 15:12:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2276#issuecomment-873609342  

Thanks @vinniefalco I found your CppCon2018 talk and have been looking through that.  It is helpful to understand how the server works but my client isn't a browser so still need to figure out how to extend the existing client examples to do a back and forth.  
  
Unrelated, curious if you have ever done any performance and scaling tests on the various threading models.  I've read about websocket servers written in Golang that can handle 1M+ concurrent connections on relatively modest hardware.  I'd assume something written in C++ could do that just as easily.

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-07-04 20:07:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2276#issuecomment-873653310  

Beast/asio is limited only by the hardware and OS. I can confirm that massively scaling is possible with beast and works well. I did this when working for a cryptocurrency exchange.

---

## Comment 4

> Username: gitgitwhat  
> Created at: 2021-07-04 20:14:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2276#issuecomment-873654399  

Thanks @madmongo1 I assumed as much.  
  
Can you share any write ups, code examples, or gotchas to achieve scale?  It's a interesting problem that I'm just not that familiar with but want to learn.

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-07-04 20:16:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2276#issuecomment-873654603  

Late here now but I’ll write something in it after I’ve competed this months blog (which covers some epic new features about to be released in asio)

---

## Comment 6

> Username: amanbadone  
> Created at: 2022-12-06 18:34:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2276#issuecomment-1339804693  

Hy, This issue seems interesting to me Although I currently don't have sufficient knowledge regarding the technicality  
I would still like to ask if I can give it a try to document it. This process will also let me learn the technical aspect and also about the contribution in this repo. Maybe you can give me instructions on what you want to get completed in the documentation
