# #1804 - Q: draining http stream with asio instead of http::async_read [Closed]

> Username: WojciechMigda  
> Created at: 2020-01-16 21:50:17 UTC  
> Updated at: 2020-02-23 00:40:55 UTC  
> Closed at: 2020-02-23 00:40:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1804  

Hello,  
  
I am exploring desing options with the client system I am building and because it is not possible to have multiple http::async_writes with beast on the same http stream, at this moment I am queuing my writes.  
However, it might turn out that I will not need to explicitly process inbound responses in http stream with beast (which also includes parsing http response header), and instead I might just get away with pulling in data from http stream and even discarding it in case of 200 OK.  
  
I tried boost::asio::async_read on http stream and either it reads only some of the inbound data or it fails (depending on invocation). I my naivety I expected this to just read whatever data the server sent in the response.  
I also tried asio::async_read_until but I don't seem to make it work at all.  
One detail: I was trying to do the above with flat_static_buffer.  
I was hoping maybe you have some advice on what is the best approach if one just needs to drain inbound http stream to collect unparsed response data to either discard it or to apply some limited inspection?  
Thank you.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-01-16 22:22:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1804#issuecomment-575374503  

Hi @WojciechMigda, thanks for gettting in touch.  
  
Are you able to be a little more specific about what "limited inspection" means?  
  
What I understand it to mean from what you've written is:  
  
1. send a request  
2. receive the first line of the response header  
3. if it's 200 OK, drain the response *  
4. if it's not 200 OK, read the HTTP headers but discard any body  
  
* The problem here is that if the server has indicated Connection: Keep-Alive, then "draining the response" implies reading the header in order to know the content-length and/or whether the response is chunked. Are you able to elaborate further?  
  
Regards,  
  
Richard

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-01-16 22:43:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1804#issuecomment-575381601  

One of the use-cases for HTTP parser refactor should be to simply read and discard the message body.

---

## Comment 3

> Username: WojciechMigda  
> Created at: 2020-01-16 23:09:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1804#issuecomment-575389953  

@madmongo1   
The responses are pretty much well structured json (in many cases the body is just `{}`). The reason I am considering using asio to async_read from http stream is that I am also hoping to avoid the limitation imposed by the assertion in `pending_guard` in `stream_base.hpp`.  
Right now I async_write with asio (my requests are templated and I can operate on raw text) and then http::async_read, holding back any async_writes until my on_read handler completes work.  
  
Instead, In my on_write handler I would like to schedule asio async_read possibly entire response payload (am I too naive thinking that whatever the server sent over the wire will be already pending to be read from the socket on the client side once my on_read handler is invoked?) without having to worry that whatever I am reading belongs to the response to the most recent async_write or not. It might require full parsing or not, but the result would not necesarily be significant most of the time.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-01-16 23:11:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1804#issuecomment-575390434  

Sounds like a case of premature optimization

---

## Comment 5

> Username: stale[bot]  
> Created at: 2020-02-15 23:44:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1804#issuecomment-586652231  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: stale[bot]  
> Created at: 2020-02-23 00:40:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1804#issuecomment-590013466  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
