# #2050 - "wrong version number" error when restarting connection manually [Closed]

> Username: UndarkAido  
> Created at: 2020-08-12 03:10:41 UTC  
> Updated at: 2020-09-01 15:21:36 UTC  
> Closed at: 2020-09-01 15:21:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2050  

### Version of Beast  
  
1.71  
  
### Steps necessary to reproduce the problem  
  
Program: https://github.com/DiscordPP/echo-bot  
  
Websocket implementation: https://github.com/DiscordPP/websocket-beast/blob/master/discordpp/websocket-beast.hh  
  
The initial websocket connection is successful and reconnects after lost internet are successful but when Discord requests a reconnect and I try to reboot the websocket connection myself I get a "wrong version number" error thrown by the handshake at line 99. I'm guessing I'm shutting something down wrong but I have no idea what. Does anyone know what I'm doing wrong?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-08-12 03:18:12 UTC  
> Updated at: 2020-08-12 03:51:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-672546247  

Generally speaking you can't "re-use" a Beast websocket stream. You have to destroy the old one and use a new one. You can store it in a `unique_ptr` or `optional` which lets you safely re-construct in place.

---

## Comment 2

> Username: UndarkAido  
> Created at: 2020-08-12 03:21:36 UTC  
> Updated at: 2020-08-12 03:21:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-672547246  

My call of `ws_ = std::make_unique<boost::beast::websocket::stream<ssl::stream<tcp::socket>>>(*aioc, ctx);` before I connect not sufficient?  
  
Edit: Line 71

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-08-12 03:27:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-672549769  

I see. Can you please try also logging the value of `ec.category().name()` at the same time you log `ec.message()`? I need to know if this error is coming from OpenSSL.

---

## Comment 4

> Username: UndarkAido  
> Created at: 2020-08-12 03:28:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-672550064  

It takes a long time for Discord to send the reconnect event but I'll start that brewing!

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-08-12 03:29:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-672550376  

By the way, you cannot mix synchronous and asynchronous calls - I see you are calling `async_read` in one place and `write` in another. This won't work.

---

## Comment 6

> Username: UndarkAido  
> Created at: 2020-08-12 03:33:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-672551335  

Huh, it's been working just fine except for this. I guess I'll need to swap them over next because I need the async read. How to I get the category from a `boost::wrapexcept<boost::system::system_error>`?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2020-08-12 03:34:52 UTC  
> Updated at: 2020-08-12 03:35:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-672551766  

> How to I get the category from a boost::wrapexcept<boost::system::system_error>?  
  
umm.... if `se` has type `boost::system::system_error` then the category name can be obtained with:  
```  
se.code().category().name()  
```  
  
I have never seen `wrapexcept` before. Typically I don't use exceptions in network code. You may find it easier to express your program in terms of the Beast functions and members which use `error_code&`.

---

## Comment 8

> Username: UndarkAido  
> Created at: 2020-08-12 03:39:48 UTC  
> Updated at: 2020-08-12 03:42:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-672553785  

Calling the handshake function to reconnect as I outline above results in this:  
```  
terminate called after throwing an instance of 'boost::wrapexcept<boost::system::system_error>'  
  what():  wrong version number  
Aborted (core dumped)  
```  
So that's where I'm getting the exception from.  
  
Edit: It seems to like this:  
```cpp  
                     try {  
                         // Perform the websocket handshake  
                         ws_->handshake(url,  
                                        "/?v=" + std::to_string(apiVersion) +  
                                            "&encoding=json");  
                     } catch (boost::wrapexcept<boost::system::system_error> e) {  
                         std::cerr << e.what() << ": " << e.code().category().name() << " | " << e.code().message() << "\n";  
                         exit(1);  
                     }  
```  
  
I'll be back with the results when it crashes again.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2020-08-12 03:51:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-672556729  

`asio::ssl::stream` definitely does not allow both synchronous and asynchronous calls concurrently.

---

## Comment 10

> Username: UndarkAido  
> Created at: 2020-08-12 04:27:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-672565390  

Oh they aren't concurrent. The synchronus setup leads into the async read and the async read is cancelled before new setup.

---

## Comment 11

> Username: UndarkAido  
> Created at: 2020-08-12 04:29:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-672565692  

Oh but my write is synchronus. Huh. Well it works, I can't tell you why. Both the read and the write.

---

## Comment 12

> Username: UndarkAido  
> Created at: 2020-08-12 14:24:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-672903271  

The category name is `asio.ssl`

---

## Comment 13

> Username: vinniefalco  
> Created at: 2020-08-12 14:25:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-672903987  

Right, so you are misusing the SSL stream. First I advise that you do not mix synchronous and asynchronous. Second make sure that you are not accessing the stream from more than one thread at a time. And third, make sure that you are using the SSL stream correctly.

---

## Comment 14

> Username: UndarkAido  
> Created at: 2020-08-12 14:28:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-672905677  

Ok, I'll look at swapped out my synchronous stuff for asynchronous soon. The program is single threaded, so that shouldn't be an issue. What would using the SSL stream incorrectly look like?

---

## Comment 15

> Username: vinniefalco  
> Created at: 2020-08-12 14:44:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-672915215  

> What would using the SSL stream incorrectly look like?  
  
Well, now that you know that it is an SSL problem, have you perhaps done an Internet search for "SSL wrong version number?"

---

## Comment 16

> Username: UndarkAido  
> Created at: 2020-08-12 14:47:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-672917225  

Ah, will do. I'd done all of my searches for "Boost wrong version number" and those all led back to #1776. I've implemented the changes from there but I'll do some more general searches 👍

---

## Comment 17

> Username: vinniefalco  
> Created at: 2020-08-12 15:22:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-672940341  

Try this  
https://www.google.com/search?q=ssl+%22wrong+version+number%22&oq=ssl+%22wrong+version+number%22

---

## Comment 18

> Username: UndarkAido  
> Created at: 2020-08-12 16:28:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-672979081  

Yeah, that's what I was planning on doing when I've got a chance. Thank you though!

---

## Comment 19

> Username: madmongo1  
> Created at: 2020-08-20 13:50:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-677676400  

Hi @UndarkAido , how are you getting on with this?

---

## Comment 20

> Username: UndarkAido  
> Created at: 2020-08-20 13:57:07 UTC  
> Updated at: 2020-08-20 14:01:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-677680732  

@madmongo1 adding a delay got rid of this specific error, so my library is reconnecting to Discord the first time but then Discord says the session token is invalid for some reason (I may be doing something wrong but it works when disconnected because of losing internet) after which you have to do another reconnect for a fresh session and when it's doing that reconnect it crashes somewhere deep inside asio SSL with the only stack trace on my code going to the asio run command. I've tried installing certify and I've almost made everything async like @vinniefalco said but it still fails in the same deep place. I can get the stack trace in just a few.  
  
I think it has something to do with me not shutting down the web socket connection properly because it happens before I have a chance to make the new HTTP call to Discord for the gateway address.  
  
Edit: Oh wait with Certify I know just get "Process finished with exit code 1" but it happens in the same place and I can't seem to intercept it with GDB.  
  
Edit 2: Oh I do still have the location that the stack trace took me to up though. It's `boost/asio/ssl/detail/impl/engine.ipp` line 197 which is `int length = ::BIO_write(ext_bio_, data.data(), static_cast<int>(data.size()));` (though Certify could certainly be dying somewhere else now)

---

## Comment 21

> Username: UndarkAido  
> Created at: 2020-08-20 14:10:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-677690249  

@madmongo1 If you don't mind taking a look I've pushed my changes here: https://github.com/DiscordPP/echo-bot/tree/fix-resume  
Typing `~reboot` in chat makes the bot restart the connection and triggers the error.  
The README has setup instructions.  
And if you need help with anything free to mention me or a @Helper in my support server: https://discord.gg/0usP6xmT4sQ4kIDh

---

## Comment 22

> Username: UndarkAido  
> Created at: 2020-08-21 12:38:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-678268786  

Oh, I had the stacktrace saved: https://gist.github.com/UndarkAido/f7e49841006ad84fb21ea46395684610

---

## Comment 23

> Username: madmongo1  
> Created at: 2020-08-21 13:15:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-678284619  

OK, I've done a fair bit of auto-reconnect with websockets before. I'll take a look.

---

## Comment 24

> Username: UndarkAido  
> Created at: 2020-08-28 20:49:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-683140344  

Have you been able to find anything @madmongo1?

---

## Comment 25

> Username: UndarkAido  
> Created at: 2020-09-01 15:21:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2050#issuecomment-684934281  

It seems most of my issues hinged on the outstanding `async_read`. I've made it all async now but it *might* work to mix them if the outstanding read was handled correctly. There could be something lower I'm missing; everything in my lib is async now, though. Thank you both!
