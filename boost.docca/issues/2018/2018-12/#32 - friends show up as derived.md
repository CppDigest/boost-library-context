# #32 - friends show up as derived [Closed]

> Username: vinniefalco  
> Created at: 2018-12-23 05:38:05 UTC  
> Updated at: 2021-03-04 21:50:55 UTC  
> Closed at: 2021-03-04 21:50:55 UTC  
> Url: https://github.com/boostorg/docca/issues/32  

For example free function `async_connect`, a friend of `timed_stream`, says:  
  
"(Inherited from async_connect)"

---

## Comment 1

> Username: evanlenz  
> Created at: 2021-01-09 07:28:31 UTC  
> Updated at: 2021-01-09 07:28:52 UTC  
> Url: https://github.com/boostorg/docca/issues/32#issuecomment-757110553  

I couldn't find `timed_stream` in the Beast HTML docs (assuming that's where it was originally reported). I wonder if this one is obsolete too, or if it just needs a different repro case.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-01-25 23:37:08 UTC  
> Url: https://github.com/boostorg/docca/issues/32#issuecomment-767182014  

it was renamed to `basic_stream` before the release:  
https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__basic_stream.html

---

## Comment 3

> Username: evanlenz  
> Created at: 2021-03-03 01:11:24 UTC  
> Url: https://github.com/boostorg/docca/issues/32#issuecomment-789347407  

Following up on this. I see async_connect on that basic_stream page, but I don't see it saying "(Inherited from async_connect)". Does that mean this issue is obsolete? Or am I misunderstanding it? Sorry I need the extra handholding!

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-03-04 21:50:55 UTC  
> Url: https://github.com/boostorg/docca/issues/32#issuecomment-790970566  

It looks like I got rid of the free functions, so this issue is no longer relevant - good work ;)
