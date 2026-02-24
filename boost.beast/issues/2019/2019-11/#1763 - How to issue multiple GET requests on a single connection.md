# #1763 - How to issue multiple GET requests on a single connection [Closed]

> Username: Xeverous  
> Created at: 2019-11-09 22:03:47 UTC  
> Updated at: 2019-11-10 00:39:10 UTC  
> Closed at: 2019-11-10 00:39:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1763  

Hey, I have a specific use case and would like to optimize my project towards it (networking takes majority of the time):  
  
- the progam rarely needs to download data but when it needs it's the longest task  
- when the program needs to download, it needs to perform multiple HTTP GET requests against the same host (only target URL differs)  
- AFAIK, if the server is HTTP 1.1 it is possible to issue multiple requests on a single connection avoiding the need for multiple handshakes and name resolutions  
- I have no idea whether/how spawning multiple io_contexts or worker threads affects the overall speed. I issue ~20 requests at most.  
- I want to support HTTPS, but apparently the only thing to make it work is proper `ssl::context` construction  
  
I'm currently getting the same error as in #821  
  
my code: https://github.com/Xeverous/filter_spirit/blob/feature-poe-ninja/src/lib/fs/network/http.cpp

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-11-09 22:04:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1763#issuecomment-552141831  

Well just keep the connection open and issue another read?

---

## Comment 2

> Username: Xeverous  
> Created at: 2019-11-09 22:05:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1763#issuecomment-552141889  

That's exactly what I though to do but apparently I either did something wrong or it's more complicated.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-11-09 22:17:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1763#issuecomment-552142774  

Have you tried the **advanced-server** example? It keeps the connection open and allows multiple requests.

---

## Comment 4

> Username: Xeverous  
> Created at: 2019-11-09 22:36:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1763#issuecomment-552143989  

I'm not issuing requests towards my own server - I'm implementing only a client. The only things that I spotted were:  
  
- other examples use different streams - not sure what's the impact  
- server example has `keep_alive(true)` - I added this to my request but it did not help

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-11-09 22:37:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1763#issuecomment-552144043  

Is the server closing the connection on you? What do the headers in the server's response look like?

---

## Comment 6

> Username: Xeverous  
> Created at: 2019-11-09 22:51:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1763#issuecomment-552144844  

```  
GET /itemdata HTTP/1.1  
Host: api.poe.watch  
User-Agent: Boost.Beast/248  
  
HTTP/1.1 200 OK  
Date: Sat, 09 Nov 2019 22:44:42 GMT  
Server: Apache/2.4.29 (Ubuntu)  
Cache-Control: max-age=60  
Expires: Sat, 09 Nov 2019 22:45:12 GMT  
Access-Control-Allow-Origin: *  
Age: 29  
X-Cache: HIT from api.poe.watch  
Content-Length: 4722679  
Content-Type: application/json  
  
GET /compact?league=Standard HTTP/1.1  
Host: api.poe.watch  
User-Agent: Boost.Beast/248  
  
HTTP/1.1 200 OK  
Date: Sat, 09 Nov 2019 22:44:42 GMT  
Server: Apache/2.4.29 (Ubuntu)  
Cache-Control: max-age=60  
Expires: Sat, 09 Nov 2019 22:45:42 GMT  
Access-Control-Allow-Origin: *  
X-Cache: MISS from api.poe.watch  
Transfer-Encoding: chunked  
Content-Type: application/json  
  
GET /itemdata HTTP/1.1  
Host: api.poe.watch  
User-Agent: Boost.Beast/248  
(now error - protocol is shutdown)  
```  
  
Ok, it looks like I actually do download multiple files correctly but my logic in using requests is flawed - the 3rd request is redundant and it's the same as the 1st.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-11-09 22:51:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1763#issuecomment-552144878  

Seems like things are working :)

---

## Comment 8

> Username: Xeverous  
> Created at: 2019-11-09 23:16:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1763#issuecomment-552146330  

Ok, now downloading works - it was a purely off-by-one logic bug caused by missing `return` under an `if` statement.  
  
I'm getting `"could not shutdown the connection: stream truncated"` when closing the connection. Reading #38, looks like this could be a problem with the server I'm issuing requests to.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-11-09 23:25:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1763#issuecomment-552146811  

Yeah that happens

---

## Comment 10

> Username: Xeverous  
> Created at: 2019-11-09 23:26:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1763#issuecomment-552146853  

Should I just ignore this error? Is there a need to report this to the server owner?

---

## Comment 11

> Username: vinniefalco  
> Created at: 2019-11-09 23:59:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1763#issuecomment-552148593  

You can ignore it, see:  
http://stackoverflow.com/questions/25587403/boost-asio-ssl-async-shutdown-always-finishes-with-an-error

---

## Comment 12

> Username: Xeverous  
> Created at: 2019-11-10 00:39:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1763#issuecomment-552150896  

Ok, everything now works. Thanks for fast responses.
