# #2806 - http::async_read hides client disconnection errors [Closed]

> Username: ecorm  
> Created at: 2024-01-23 20:30:23 UTC  
> Updated at: 2024-01-26 18:32:41 UTC  
> Closed at: 2024-01-26 18:32:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2806  

`http::async_read` does not report client disconnection errors. When a client disconnects before starting to send an HTTP header, `http::async_read` will emit an `http::error::bad_method` error. Because I can't distinguish a disconnection error from a genuine bad HTTP method error, this prevents me from properly logging what happened.  
  
I would expect `http::async_read` to emit the original `boost::asio::error::broken_pipe` or `boost::asio::error::connection_reset` error instead of absorbing it.  
  
The same problem occurs with `http::async_read_header`.  
  
### Version of Beast  
  
` BOOST_BEAST_VERSION 347`  
  
### Steps necessary to reproduce the problem  
  
1. Launch [asynchronous HTTP server example](https://www.boost.org/doc/libs/1_82_0/libs/beast/example/http/server/async/http_server_async.cpp)  
  
2. Start a Telnet session with the server.  
  
3. Terminate the Telnet session via Ctrl-C so that it disconnects from the server.  
  
4. The server prints `read: bad method` instead of `read: Connection reset by peer`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2024-01-23 20:51:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2806#issuecomment-1906896869  

If the underlying socket returns a non-successful error code, this should be propagated to the caller and not returned as a parsing error. This first needs a failing test case.

---

## Comment 2

> Username: ecorm  
> Created at: 2024-01-23 20:54:15 UTC  
> Updated at: 2024-01-23 21:00:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2806#issuecomment-1906901253  

> This first needs a failing test case.  
  
Was that directed to me, or to the maintainers? I prefer to let your devs/maintainers take care of it.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2024-01-23 21:24:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2806#issuecomment-1906941179  

> Was that directed to me, or to the maintainers?  
  
I wasn't directing it to anyone just stating a fact for whoever works on this. There is no requirement for the reporter of the problem to first write a test case. Of course, if you provide one we will be happy for the contribution :)  
  
Thanks

---

## Comment 4

> Username: ecorm  
> Created at: 2024-01-23 21:33:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2806#issuecomment-1906956096  

I'll wait a while, and if nobody contributes a failing test case, I'll see if I can whip one up to get the ball rolling on this issue.

---

## Comment 5

> Username: ashtum  
> Created at: 2024-01-25 06:42:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2806#issuecomment-1909447032  

@ecorm, it seems that when trying to end the Telnet session using Ctrl-C, it writes `^C` characters to the socket. The parser attempts to parse them, resulting in a `bad method` error.  
  
However, using the following method yields the expected result:  
> To exit the Telnet session, type Ctrl + ] on your keyboard. This changes the command prompt to show as telnet>. Back in the terminal, type in the word 'close' to close the session.

---

## Comment 6

> Username: ecorm  
> Created at: 2024-01-25 23:00:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2806#issuecomment-1911131805  

> @ecorm, it seems that when trying to end the Telnet session using Ctrl-C, it writes ^C characters to the socket. The parser attempts to parse them, resulting in a bad method error.  
  
Ah, that was a blunder on part. Sorry for all the ruckus. Tomorrow, I'll try closing Telnet properly and will confirm that my server receives the expected error code upon disconnection.

---

## Comment 7

> Username: ecorm  
> Created at: 2024-01-26 18:32:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2806#issuecomment-1912508930  

Closing Telnet properly makes `async_read` emit an `boost::boost::http::error::end_of_stream` in my server, which is expected.  
  
I was looking for an easy way to generate `connection_reset` errors for testing purposes, but it seems Telnet will always gracefully close the connection (even when I `kill` it). I guess I'll have to find another method or write a little client app.  
  
Closing as a false positive. Thanks for the assistance!
