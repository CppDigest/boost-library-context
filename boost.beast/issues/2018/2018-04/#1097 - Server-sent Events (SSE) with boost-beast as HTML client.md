# #1097 - Server-sent Events (SSE) with boost/beast as HTML client [Closed]

> Username: smipi1  
> Created at: 2018-04-15 20:01:32 UTC  
> Updated at: 2020-09-24 15:25:12 UTC  
> Closed at: 2018-05-30 11:51:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1097  

Do you have examples on how to use boost/beast as an HTML client to receive HTML5 Server-sent Events (SSE)?  
  
P.S. @vinniefalco, I could not find any information on this, so I am logging an issue (at least for posterity).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-04-16 00:03:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1097#issuecomment-381448814  

I have no idea, it sounds like it is beyond the scope of Beast and has to do with the specifics of HTML5 (which is not covered by the library).

---

## Comment 2

> Username: smipi1  
> Created at: 2018-04-16 10:53:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1097#issuecomment-381560014  

I think it should be doable with an async_read_some with delimiter.  
  
In essence it is an http request that never completes. You get from the event provider target with a Content-Type: text/event-stream. The response has the headers, followed by each event preceded with the text string 'data:'. Events are separated with '\n\n' and are flushed via the socket on a per-event basis. The best explanation (be-it a bit dated) is here: https://www.html5rocks.com/en/tutorials/eventsource/basics/  
  
Do you forsee any problems using boost/beast this way on the client side?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-04-16 12:03:19 UTC  
> Updated at: 2018-04-16 15:44:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1097#issuecomment-381575761  

>Do you forsee any problems using boost/beast this way on the client side?  
  
It should be possible, read the HTTP header first and then read the body yourself.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-04-30 01:01:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1097#issuecomment-385296618  

Has this issue been resolved?

---

## Comment 5

> Username: smipi1  
> Created at: 2018-04-30 10:28:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1097#issuecomment-385361660  

You can close it. I will ask the developers to add a writeup to this ticket so others can find the information. It might be interesting to add an example server and client at some point as well.

---

## Comment 6

> Username: stale[bot]  
> Created at: 2018-05-30 10:46:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1097#issuecomment-393116216  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: bcs-cmorell  
> Created at: 2019-04-29 16:01:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1097#issuecomment-487639108  

> You can close it. I will ask the developers to add a writeup to this ticket so others can find the information. It might be interesting to add an example server and client at some point as well.  
  
Hello, Any update on this type of events and how to handle them(Server/Client Sample). I am currently trying to read data from an open stream and found this thread.

---

## Comment 8

> Username: ghost  
> Created at: 2020-09-24 15:25:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1097#issuecomment-698416320  

Finally you can use Beast WebSocket instead of SSE.
