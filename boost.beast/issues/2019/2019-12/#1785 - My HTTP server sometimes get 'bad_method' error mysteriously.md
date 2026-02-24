# #1785 - My HTTP server sometimes get 'bad_method' error mysteriously. [Closed]

> Username: haquocviet  
> Created at: 2019-12-20 02:49:32 UTC  
> Updated at: 2020-01-23 01:28:15 UTC  
> Closed at: 2020-01-23 01:28:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1785  

Hi Vinnie,  
  
First thank you for your good library and your support to the community.  
  
I have an HTTP server created based on your "http_server_small" example.  
It works seem normal except for a strange symptom I describe below:  
  
When the server started running, the connected client sent requests and   
received responses normally, but after several hours, any attempt by the   
client to connect to the server would end up with an "bad_method" error in  
read_request(). Note that the error occurs as soon as the new connection   
is accepted and right after the first http::async_read invoked, which means  
the client is disconnected before having a chance to send any request.  
  
The difference I made to your example are:  
1. The server keeps any connection alive until it is disconnected by the client.  
2. The disconnected clients are cached for next incoming clients.   
  
When the issue occurred, I have tried debugging the issue but, frankly, it is hard   
to find a trace from a long call stack invoked from low-level asio's polling loop.  
  
Also I have tried to send "bad" methods (methods the server does not handle) to the   
server (when it was working normally) to simulate the issue, but I got no luck.  
  
I hope this issue is caused by my misuse rather than a bug, but I do not know  
where the cause is and in what situation the issue would occurred.  
  
The issue happens when I use beast 277 (boost-1.72) as well as the previous version.  
  
Could you please give me a tip for this issue.  
Thank you.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-12-20 02:53:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1785#issuecomment-567765574  

what happens when you try http-server-async?

---

## Comment 2

> Username: haquocviet  
> Created at: 2019-12-20 03:03:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1785#issuecomment-567767625  

I don't know about that because my server does not base on http-server-async.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-12-20 03:05:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1785#issuecomment-567767935  

Well, it should be possible to copy your logic into the other example and see what happens.

---

## Comment 4

> Username: haquocviet  
> Created at: 2019-12-20 03:08:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1785#issuecomment-567768608  

I will try.   
Do you have any opinion about "in what situation the bad_method would happen"?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-12-20 03:11:59 UTC  
> Updated at: 2019-12-20 03:12:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1785#issuecomment-567769264  

Yes, it is most likely that you are not handling threads correctly. For example by not using a strand. Or you are reaching the limit of that server, I believe it pre-allocates the workers? Or perhaps you have a bug where a worker gets "stuck" and the amount of workers available to service requests falls over time, eventually reaching 0.  
  
http-server-async is easier to use then the http-server-fast.

---

## Comment 6

> Username: haquocviet  
> Created at: 2019-12-20 03:47:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1785#issuecomment-567776137  

It is not like that, there is often only one or two clients connecting to my server, and when the issue occurs the server even still catches the new connection at async_accept(). I do not see any thread problem here.  
The reason I pick the example "http-server-small" for reference because it is much simpler and easier to base than http-server-async.

---

## Comment 7

> Username: haquocviet  
> Created at: 2019-12-23 05:06:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1785#issuecomment-568356593  

I found the reason.   
That is because of leftover of the received buffer of disconnected connection which were cached previously.  
So doing clear _request_ and _buffer_ before passing to _http::async_read()_ solves the issue.  
  
It was quite hard to find down the cause because the issue does not always occur. It would be easier if I was given a relevant hint close to the point which bad_method might happen.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2019-12-23 13:09:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1785#issuecomment-568470687  

> It would be easier if I was given a relevant hint close to the point which bad_method might happen.  
  
Actually I am planning on removing that buffer from the public API, and just building it into the parser so this kind of mistake can't happen at all.

---

## Comment 9

> Username: stale[bot]  
> Created at: 2020-01-22 13:47:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1785#issuecomment-577189069  

This issue has been open for a while with no activity, has it been resolved?
