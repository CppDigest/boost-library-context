# #987 - Please add a pipelining client example [Closed]

> Username: gjasny  
> Created at: 2018-01-16 19:43:32 UTC  
> Updated at: 2018-02-21 21:40:05 UTC  
> Closed at: 2018-02-21 21:40:05 UTC  
> Url: https://github.com/boostorg/beast/issues/987  

Hello,  
  
I wonder if you could provide an example client which performs [pipelining](https://www.w3.org/Protocols/rfc2616/rfc2616-sec8.html#sec8.1.2.2)?  
  
If I modify the async client, remove the `socket_.shutdown` and issue another `http::async_write` in `session::on_write` I get two `read: bad version`.  
  
Thanks,  
Gregor

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-16 20:14:07 UTC  
> Url: https://github.com/boostorg/beast/issues/987#issuecomment-358090247  

Hmm...I am reluctant to provide such an implementation. It would be difficult to test, since many HTTP servers do not handle pipelining correctly (even Firefox and Chrome have it turned off). And it would greatly complicate the examples without a corresponding benefit.  
  
You have to wait for the first call to `async_write` to complete before you issue the second one, otherwise the behavior is undefined.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-01-26 17:03:16 UTC  
> Url: https://github.com/boostorg/beast/issues/987#issuecomment-360843172  

Has this issue been resolved?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-02-21 21:40:05 UTC  
> Url: https://github.com/boostorg/beast/issues/987#issuecomment-367482534  

It looks like there is nothing more to do here so I will go ahead and close the issue. Feel free to open a new one in the future should the need arise!
