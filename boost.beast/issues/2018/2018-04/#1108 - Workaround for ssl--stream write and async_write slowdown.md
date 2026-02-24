# #1108 - Workaround for ssl::stream write and async_write slowdown [Closed]

> Username: vinniefalco  
> Created at: 2018-04-26 23:08:48 UTC  
> Updated at: 2018-05-04 00:40:53 UTC  
> Closed at: 2018-05-04 00:40:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1108  

This issue:  
https://stackoverflow.com/questions/50026167/performance-drop-on-port-from-beast-1-0-0-b66-to-boost-1-67-0-beast  
  
Could be resolved with an overload of `write` and `async_write` which flattens the serialized message header to a single dynamically allocated piece of memory instead of passing buffer sequences with length greater than one to the underlying SSL stream.  
  
Alternatively, new functions can be provided with different names. Or both.

---

## Comment 1

> Username: djarek  
> Created at: 2018-04-26 23:16:33 UTC  
> Updated at: 2018-04-26 23:16:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1108#issuecomment-384817070  

What about a special tag type & function that wraps the message and allows calling the flattening overload, e.g.  
`boost::beast::http::async_write(stream, boost::beast::http::flatten(message), token);`

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-04-26 23:23:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1108#issuecomment-384818139  

>What about a special tag type & function that wraps the message and allows calling the flattening overload  
  
There's really no functional difference between that and a function with a different name. The tag and wrapper are harder to document.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-04-27 16:41:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1108#issuecomment-385026438  

Related:  
https://github.com/boostorg/asio/issues/100  
https://stackoverflow.com/questions/38198638/openssl-ssl-write-from-multiple-buffers-ssl-writev  
https://stackoverflow.com/questions/50026167/performance-drop-on-port-from-beast-1-0-0-b66-to-boost-1-67-0-beast  
boostorg/beast#1108
