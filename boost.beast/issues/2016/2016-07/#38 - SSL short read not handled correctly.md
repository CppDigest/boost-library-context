# #38 - SSL short read not handled correctly [Closed]

> Username: tpecholt  
> Created at: 2016-07-06 17:05:54 UTC  
> Updated at: 2019-02-23 20:45:30 UTC  
> Closed at: 2019-02-23 20:45:30 UTC  
> Url: https://github.com/boostorg/beast/issues/38  

I am playing with http client and SSL. And I am having problem with getting the response from the server after calling beast::http::async_read.   
  
When I examined the code in read.ipp: parse_op::operator() I found the there is nothing to handle "ssl short read errors". I am not SSL expert but from my previous attempts with ASIO I remember ASIO often finished read operations on SSL socket by receiving this kind of error. I think it could be interpreted as error::eof.   
  
When I made following changes to read.ipp I can get correct http response in my handler. Without this change SSL short read error is propagated to my handler but response doesn't contain parsed response although it was received successfully. cpp-netlib library also contains special code to handle it.

---

## Comment 1

> Username: tpecholt  
> Created at: 2016-07-06 17:13:02 UTC  
> Url: https://github.com/boostorg/beast/issues/38#issuecomment-230840626  

patch   
[read.ipp.zip](https://github.com/vinniefalco/Beast/files/350454/read.ipp.zip)

---

## Comment 2

> Username: vinniefalco  
> Created at: 2016-07-06 17:39:23 UTC  
> Url: https://github.com/boostorg/beast/issues/38#issuecomment-230848143  

Looking into it...

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-07-06 19:32:25 UTC  
> Url: https://github.com/boostorg/beast/issues/38#issuecomment-230881726  

The problem with this patch is that it requires `<boost/asio/ssl/error.hpp>` which requires `<openssl/conf.h>`. That means that users of Beast will need to have OpenSSL installed and configured even if they are not using SSL sockets. I'm trying to think of a workaround...

---

## Comment 4

> Username: JoelKatz  
> Created at: 2016-07-06 20:37:38 UTC  
> Updated at: 2016-07-06 20:38:43 UTC  
> Url: https://github.com/boostorg/beast/issues/38#issuecomment-230898627  

An SSL short read error occurs when the TCP connection closes unexpectedly, that is, without the exchange of SSL shutdown notifications. There is an important distinction between the proper shutdown of the SSL layer and the unexpected shutdown of the TCP connection without proper SSL close notification. Pretending you had a normal close when you actually had an unexpected one can create vulnerabilities (consider an HTTP 0.9 connection where the end of a resource is marked by the normal close of the connection). Unexpected shutdown of the TCP connection without an SSL close notification is an error condition, not an expected condition.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2016-07-06 20:39:20 UTC  
> Url: https://github.com/boostorg/beast/issues/38#issuecomment-230899097  

@tpecholt I have researched the issue and I believe that the Beast behavior is correct. A SSL "short read" means that the SSL `close_notify` message was never received (see http://stackoverflow.com/questions/25587403/boost-asio-ssl-async-shutdown-always-finishes-with-an-error scenario **PartyA invokes shutdown() after PartyB closes connection without negotiating shutdown**).  
  
Your patch "eats" this error message and prevents the caller from knowing that the connection was not closed gracefully. In fact, to prevent vulnerabilities the application must always check the error code returned from SSL operations.  
  
I believe what is happening here, is that the remote end of your program's connection is not gracefully closing the connection via SSL. In other words, it is not properly invoking the SSL shutdown handshake mechanism. Or it is closing the socket before the shutdown completes (note, we are talking about the SSL shutdown here and not the TCP/IP shutdown).  
  
When the remote end does not shut down correctly, correct application behavior is to discard the partial HTTP message not to make a best effort to finish it because the contents of the buffers may be undefined. Its not possible to distinguish the SSL short read resulting from improper shutdown, versus a short read resulting from the connection being severed.  
  
I suggest that you investigate the software on the other end of the connection to be certain it is correctly performing the SSL shutdown handshake.

---

## Comment 6

> Username: JoelKatz  
> Created at: 2016-07-06 20:49:27 UTC  
> Updated at: 2016-07-06 20:50:10 UTC  
> Url: https://github.com/boostorg/beast/issues/38#issuecomment-230901869  

I don't think this is a good idea, but if there is a consensus that this is a problem, the least awful solution I can think of is this: On any TCP error, if the implementation can prove that the only thing the other side could possibly have done at that point is close the connection or start a new request, consider the connection to have closed normally.  
  
This seems safe to me and doesn't require decoding the short read error. But it seems like a partial workaround for a broken peer, and since it's only partial, it doesn't avoid the need to fix the peer anyway. So there doesn't seem to be much point.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2016-07-08 00:35:42 UTC  
> Url: https://github.com/boostorg/beast/issues/38#issuecomment-231246683  

See  
https://github.com/cpp-netlib/cpp-netlib/issues/670

---

## Comment 8

> Username: crashtestdummy32  
> Created at: 2017-10-11 18:38:34 UTC  
> Url: https://github.com/boostorg/beast/issues/38#issuecomment-335907179  

Sorry, I feel like I keep asking dumb questions:  
  
### system info:  
using beast version 115, boost 1.65.1  
  
### code used:  
using the example code [here](http://www.boost.org/doc/libs/develop/libs/beast/example/http/client/async-ssl/http_client_async_ssl.cpp)  
  
i changed the lines  
`   // The SSL context is required, and holds certificates  
    ssl::context ctx{ssl::context::sslv23_client};  
    // This holds the root certificate used for verification  
    load_root_certificates(ctx);`  
  
to:  
  
` ssl::context ctx{ssl::context::tlsv11_client};`  
  
so i'm just not validating the cert for tests, and using tls 1.1.  
  
## problem:  
i get a short read from google.com:443/ (oddly not from yahoo.com:443/). I'm also getting a short read from our kestrel server running on dotnet core 2.0, which sends a connection-close in the header.  
the short read occurs during shutdown, obviously.  
  
should i try calling shutdown again?  
should i just wait some amount of time for shutdown to complete or give up if that time expires?  
i can try looking into what google/kestrel are doing and contact them?

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-10-11 18:44:00 UTC  
> Updated at: 2017-10-11 18:46:23 UTC  
> Url: https://github.com/boostorg/beast/issues/38#issuecomment-335908865  

Google is famous for abruptly closing TLS connections without going through the handshake, it "optimizes" their products at "large scales." As long as you know your code is right, I would not worry about it. Chrome does this to my HTTP servers as well, and I know my code is right, because when I use my HTTP client against my HTTP server the shutdown handshake is graceful.

---

## Comment 10

> Username: crashtestdummy32  
> Created at: 2017-10-11 19:01:55 UTC  
> Url: https://github.com/boostorg/beast/issues/38#issuecomment-335914134  

>I know my code is right, because when I use my HTTP client against my HTTP server the shutdown handshake is graceful.   
  
that's why i pointed out that your code always worked with yahoo, and never with google, figured there was some relevance.  
  
good to know about the optimizations being a reason for that. i'd be interested in reading their engineering blog discussing the reasoning.  
  
i'm still trying to decide if i need to do anything with a short read error, because right now it's handled nowhere. i guess i'll have to read the tls/ssl spec and see if i can generate a list of when it can happen and for what reasons so i can determine where to handle it or where to ignore it.  
  
thanks for always being so quick with replies!

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-10-11 20:51:45 UTC  
> Url: https://github.com/boostorg/beast/issues/38#issuecomment-335944438  

if the short read happens at the end of a session I guess you could just ignore it

---

## Comment 12

> Username: ghost  
> Created at: 2018-08-07 14:10:26 UTC  
> Url: https://github.com/boostorg/beast/issues/38#issuecomment-411069930  

What is the suggested workaround if the "short read" does *not* happen during shutdown, but rather *while reading* the response of a GET request?  
  
I ran into this with the [http_client_async_ssl.cpp example from Boost 1.67](https://www.boost.org/doc/libs/1_67_0/libs/beast/example/http/client/async-ssl/http_client_async_ssl.cpp), adding only a connection "close" header field in the request and running it with "www.google.com 443 /" as command line arguments.  
  
The current Beast implementation (Boost 1.67) seems to always return an empty response body and header in this case (along with the short read error_code), even if the response data was received (as indicated by bytes_transferred and validated by debugging into read.ipp).  
  
Shouldn't the read operation rather return all data it has received and let the application decide how to react to the error_code and what to do with the data (keep or discard)?  
Or should it be treated like the EOF case in read.ipp (see the "caller sees EOF on next read" comment in the file)?  
  
With the current behaviour, I don't really see how to implement a generic GET request that also works with servers that "optimize" SSL shutdown (incl. popular ones from Google)?  
Special-casing such known URLs or retrying different request combinations (like removing the connection close header in the failure case) is not a feasible option.  
  
Thanks!

---

## Comment 13

> Username: vinniefalco  
> Created at: 2018-08-07 14:13:51 UTC  
> Url: https://github.com/boostorg/beast/issues/38#issuecomment-411071099  

That does sound like a corner case which could use investigation. I will look into it, thanks!

---

## Comment 14

> Username: ghost  
> Created at: 2018-08-14 15:33:56 UTC  
> Url: https://github.com/boostorg/beast/issues/38#issuecomment-412914816  

@vinniefalco do you have plans to fix this for the next boost release?  
Do you have any suggestion to work around this issue for the time being?  
  
It may be a corner case that SSL shortcuts lead to an error in this case; but I would argue that this exposes a more general issue that beast may discard data it has already received / parsed when it encounters an error. I think it would be more sensible and flexible to pass the data to the caller along with the error code and let the caller decide what to do.

---

## Comment 15

> Username: stale[bot]  
> Created at: 2018-09-13 16:21:19 UTC  
> Url: https://github.com/boostorg/beast/issues/38#issuecomment-421066564  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 16

> Username: sheldonth  
> Created at: 2018-11-28 21:07:49 UTC  
> Updated at: 2018-11-28 21:10:34 UTC  
> Url: https://github.com/boostorg/beast/issues/38#issuecomment-442604993  

Could you also possibly mention where the "short read" error is located in code to help people handle it while it's out there as an issue? I'm noticing this while hitting a server behind CloudFlare:  
  
`uncaught exception of type boost::system::system_error: short read`  
  
Edit: For others who might need it, the error details are:  
`asio.ssl:335544539` in the `category:id` format.

---

## Comment 17

> Username: vinniefalco  
> Created at: 2018-11-28 21:24:46 UTC  
> Url: https://github.com/boostorg/beast/issues/38#issuecomment-442610001  

Come to think of it, I believe that this might be fixable in Beast. If we get a "short read" and the parser has not yet received any bytes, and there are no bytes in the dynamic buffer, then we can safely convert this to an "end of file" signal. Supporting evidence:  
  
_"...if your communication protocol is self-terminated, then you may simply ignore the lack of close_notify"_  
https://security.stackexchange.com/questions/91435/how-to-handle-a-malicious-ssl-tls-shutdown

---

## Comment 18

> Username: vinniefalco  
> Created at: 2018-11-28 21:38:30 UTC  
> Url: https://github.com/boostorg/beast/issues/38#issuecomment-442614181  

I need a way to detect the "short read" error without including OpenSSL, any ideas?

---

## Comment 19

> Username: vinniefalco  
> Created at: 2019-02-23 13:20:31 UTC  
> Url: https://github.com/boostorg/beast/issues/38#issuecomment-466648219  

This is what I'm thinking:  
```  
bool  
is_ssl_short_read(error_code const& ec)  
{  
    if(ec.value() != 335544539)  
        return false;  
    if(string_view{ec.category().name()} != "asio.ssl")  
        return false;  
    BOOST_ASSERT(ec.message() == "short read");  
    return true;  
}  
```

---

## Comment 20

> Username: vinniefalco  
> Created at: 2019-02-23 14:39:57 UTC  
> Url: https://github.com/boostorg/beast/issues/38#issuecomment-466657301  

Actually scratch that, I have a better way. Only took 3 years to figure this out, patch incoming.
