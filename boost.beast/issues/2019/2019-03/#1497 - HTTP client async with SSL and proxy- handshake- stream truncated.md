# #1497 - HTTP client async with SSL and proxy: handshake: stream truncated [Closed]

> Username: Xeverous  
> Created at: 2019-03-04 12:36:06 UTC  
> Updated at: 2019-03-05 20:57:44 UTC  
> Closed at: 2019-03-05 20:57:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1497  

Hi, I was playing with the [http_client_async_ssl.cpp](https://www.boost.org/doc/libs/1_69_0/libs/beast/example/http/client/async-ssl/http_client_async_ssl.cpp) example and encountered few problems when trying to add proxy support.  
  
Without the proxy, the example works (only need to disable ssl verifying as I have no backend for this) and correctly downloads data.  
  
How to perform requests through a proxy in Beast? I have tried the simple solution as in https://stackoverflow.com/questions/45617552 but now I get `handshake: stream truncated` (for any request). Is this related to #38 ? I saw some similar problems but they were in shutdown, not in handshake.  
  
Is using proxy more complicated? https://stackoverflow.com/questions/48029008 mentions I would need to send connect request first and then proceed with the get request.  
  
Another question: is setting up `req_` (the place of `// Set up an HTTP GET request message`) in the `run()` necessary? Shouldn't this code be moved to `on_handshake()`?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-03-04 13:52:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1497#issuecomment-469259770  

> is setting up req_ ... in the `run()`....  
  
Hmm, it can be moved yes

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-03-04 14:03:50 UTC  
> Updated at: 2019-03-04 14:04:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1497#issuecomment-469263624  

I might have spoken too soon. The request is set up in `run` because only then do we have access to the target, and version variables. We could save them in a data member but that would make the example needlessly more verbose, and we would have to allocate memory for the `char const*`.

---

## Comment 3

> Username: Xeverous  
> Created at: 2019-03-05 14:10:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1497#issuecomment-469693226  

What about using a proxy? I haven't found any example in Beast or ASIO documentation.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-03-05 14:35:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1497#issuecomment-469702558  

Do you have a link to the documentation for the proxy protocol your proxy uses?

---

## Comment 5

> Username: Xeverous  
> Created at: 2019-03-05 19:30:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1497#issuecomment-469825759  

I have no idea about the proxy in the company where I work but ultimately the program is intended to also run elsewhere. I guess I lack some networking knowledge but for whatever tools/programs I was using the approach was that there was some CLI/GUI option to input the addess and port or the program was affected by environment like after command `export HTTP_PROXY=some.address.com:1234`.  
  
So I thought that if Boost ASIO/Beast supports proxy, it will be as simple as providing that address and port number somewhere into the API. Is this more complicated?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-03-05 19:42:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1497#issuecomment-469830076  

> So I thought that if Boost ASIO/Beast supports proxy, it will be as simple as providing that address and port number somewhere into the API. Is this more complicated?  
  
Beast and ASIO do not support "proxy." If you want to interact with a proxy, you have to implement that protocol. A future version of Beast (or another Boost library built with Beast) may have a feature to work with specific proxy protocols like SOCKS but that is over a year away, maybe two.
