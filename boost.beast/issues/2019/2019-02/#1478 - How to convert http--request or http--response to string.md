# #1478 - How to convert http::request or http::response to string? [Closed]

> Username: ziwu14  
> Created at: 2019-02-26 01:08:24 UTC  
> Updated at: 2019-02-26 01:41:11 UTC  
> Closed at: 2019-02-26 01:41:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1478  

NT

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-26 01:10:05 UTC  
> Updated at: 2019-02-26 01:25:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1478#issuecomment-467250637  

use `operator<<` to `std::stringstream`

---

## Comment 2

> Username: ziwu14  
> Created at: 2019-02-26 01:24:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1478#issuecomment-467253849  

thx for such a quick reply, it works

---

## Comment 3

> Username: ziwu14  
> Created at: 2019-02-26 01:30:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1478#issuecomment-467254975  

wait, it compiles successfully but print out empty string.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-02-26 01:31:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1478#issuecomment-467255532  

Did you do `std::cout << ss.str()` where `ss` is the stringstream?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-02-26 01:32:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1478#issuecomment-467255694  

Printing messages works, as this example demonstrates:  
https://github.com/boostorg/beast/blob/develop/example/http/client/sync/http_client_sync.cpp#L83

---

## Comment 6

> Username: ziwu14  
> Created at: 2019-02-26 01:41:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1478#issuecomment-467257482  

because I am writing a proxy server being a daemon, so I have to use syslog() to do logging in /var/log, which gets me to convert the request and response to string first.  
  
and the problem is fixed, just like what you said.
