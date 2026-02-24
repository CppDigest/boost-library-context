# #3069 - Feature:New HTTP Protocols Supports [Closed]

> Username: albxy  
> Created at: 2026-01-18 14:02:42 UTC  
> Updated at: 2026-01-25 13:25:55 UTC  
> Closed at: 2026-01-25 13:25:55 UTC  
> Url: https://github.com/boostorg/beast/issues/3069  

I would like to know whether maintainers have an idea that HTTP/2 (further more HTTP/3) would be added to beast.I can try to write the code if possible.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2026-01-19 00:42:06 UTC  
> Updated at: 2026-01-19 00:42:39 UTC  
> Url: https://github.com/boostorg/beast/issues/3069#issuecomment-3765963234  

HTTP/2 and HTTP/3 are not planned for Beast, which is currently in end-of-life maintenance mode. Keep an eye out for Beast2 though! https://github.com/cppalliance/beast2

---

## Comment 2

> Username: albxy  
> Created at: 2026-01-24 11:29:18 UTC  
> Url: https://github.com/boostorg/beast/issues/3069#issuecomment-3794501180  

Uh,after taking a quick look at `Boost.HTTP` & `Boost.Beast2`,I've found `HTTP` says it will not support HTTP/2 or HTTP/3 protocol  
[https://develop.http.cpp.al/http/index.html#_what_this_library_does_not_do]()  
and beast2 is based on `Boost.HTTP.Proto`.  
So it seems like new protocals are still unwelcomed?Or we do have plans adding them into the plans?

---

## Comment 3

> Username: albxy  
> Created at: 2026-01-25 02:19:18 UTC  
> Updated at: 2026-01-25 04:27:18 UTC  
> Url: https://github.com/boostorg/beast/issues/3069#issuecomment-3795842960  

@vinniefalco Sorry forgot to reopen the issue…Please take a look at the comment above.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2026-01-25 10:33:43 UTC  
> Url: https://github.com/boostorg/beast/issues/3069#issuecomment-3796409221  

idk... maybe
