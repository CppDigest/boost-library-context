# #1536 - Beast and std::bind vs boost::bind [Closed]

> Username: shuras109  
> Created at: 2019-03-21 11:37:34 UTC  
> Updated at: 2019-10-15 20:22:35 UTC  
> Closed at: 2019-03-22 10:54:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1536  

Hi there!  
 I am quite curious should I use std::bind() or boost::bind() with Boost/Beast code.  
Examples from pure Boost currently using boost::bind(). But in some cases (like async_accept() from boost::asio::ip::tcp::acceptor) I can't use boost::bind() on pure boost.  
At the same time Beast examples using std::bind().   
Is there any considerations/recommendations regarding this matter?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-03-21 12:32:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1536#issuecomment-475210923  

You can use either one, as long as you don't try to mix the placeholders. If you use `boost::bind`, you have to use boost placeholders. If you use `std::bind`, you have to use std placeholders.  
  
However, Beast 1.70 introduces a new function `bind_front_handler` which is more lightweight, and doesn't use placeholders at all, so it is recommended to use that instead of bind. All the examples have been updated.

---

## Comment 2

> Username: mabrarov  
> Created at: 2019-03-21 13:05:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1536#issuecomment-475220644  

What about [`boost::beast::bind_handler`](https://www.boost.org/doc/libs/1_69_0/libs/beast/doc/html/beast/ref/boost__beast__bind_handler.html):  
  
> The passed handler and arguments are forwarded into the returned handler, whose associated allocator and associated executor will will be the same as those of the original handler.   
  
?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-03-21 13:16:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1536#issuecomment-475224094  

`bind_handler` suffers from the same problem as `boost::bind` and `std::bind`. It supports placeholders, and is heavy to compile. I try not to use it anymore, because `bind_front_handler` is faster and lighter on builds.

---

## Comment 4

> Username: shuras109  
> Created at: 2019-03-22 10:54:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1536#issuecomment-475575860  

Thank you Vinnie!  
Especially for insight into bind alternatives.

---

## Comment 5

> Username: ecorm  
> Created at: 2019-10-15 20:19:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1536#issuecomment-542387093  

@vinniefalco How does `bind_front_handler` compare to a lambda, in terms of being lightweight?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-10-15 20:22:09 UTC  
> Updated at: 2019-10-15 20:22:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1536#issuecomment-542388064  

No idea. If I had to guess, I would say the lambda is slightly more lightweight.
