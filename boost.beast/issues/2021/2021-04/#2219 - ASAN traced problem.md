# #2219 - ASAN traced problem [Closed]

> Username: GorbachevDaniil  
> Created at: 2021-04-21 15:12:41 UTC  
> Updated at: 2021-04-27 11:21:21 UTC  
> Closed at: 2021-04-27 11:21:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2219  

**Version of Beast**  
  
`BOOST_BEAST_VERSION 306`  
  
**Problem summary**  
  
Hello!  
  
I'm facing problem and have some trace to locate it, but I'm not so deep dived in Beast library, so will really appreciate if you can help me.  
  
Trace that I have from ASAN you can found in attached file. Unfortunately, some of the inputs were cut down.  
[log.txt](https://github.com/boostorg/beast/files/6351872/log.txt)  
  
My WssSession class also attached  
[wss_session.txt](https://github.com/boostorg/beast/files/6351957/wss_session.txt)  
  
If you need something else I will be glad to provide it.  
Look forward to your reply!  
  
Best regards,  
Gorbachev Daniil.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-04-21 17:50:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2219#issuecomment-824245168  

> READ of size 1 at 0x6150002d3a0c thread T1  
...  
> freed by thread T291 here:  
  
Have you taken steps to guard concurrent access of the websocket?

---

## Comment 2

> Username: GorbachevDaniil  
> Created at: 2021-04-22 11:54:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2219#issuecomment-824774256  

I'm using Websocket only from one thread. I'm creating and destroying it inside thread, so it cannot be accessed in any other place.

---

## Comment 3

> Username: GorbachevDaniil  
> Created at: 2021-04-26 10:02:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2219#issuecomment-826699201  

Maybe I didn't understand you right. Do I need to guard concurrent access to websocket for io context aswell?

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-04-26 10:34:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2219#issuecomment-826722399  

io objects such as sockets, ssl streams and webssocket streams are not thread safe. All intermediate completion handlers must execute on their own implicit or explicit strand.  
  
The io_context is thread safe.

---

## Comment 5

> Username: GorbachevDaniil  
> Created at: 2021-04-27 11:21:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2219#issuecomment-827530691  

Okay thank you
