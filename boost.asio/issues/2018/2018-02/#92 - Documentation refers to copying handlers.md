# #92 - Documentation refers to copying handlers [Closed]

> Username: vinniefalco  
> Created at: 2018-02-28 21:06:55 UTC  
> Updated at: 2020-12-30 00:49:38 UTC  
> Closed at: 2020-12-30 00:49:37 UTC  
> Url: https://github.com/boostorg/asio/issues/92  

Documentation for async operations states that copies of the handler will be made as needed but this is not always correct, the handlers are moved when possible.

---

## Comment 1

> Username: reddwarf69  
> Created at: 2018-06-05 11:14:24 UTC  
> Updated at: 2018-06-05 11:18:09 UTC  
> Url: https://github.com/boostorg/asio/issues/92#issuecomment-394671565  

I noticed that from https://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/reference/Handler.html to https://www.boost.org/doc/libs/1_67_0/doc/html/boost_asio/reference/Handler.html it changed from:  
  
`A handler must meet the requirements of CopyConstructible types (C++ Std, 20.1.3).`  
  
to   
  
`A handler must meet the requirements of MoveConstructible types (C++Std [moveconstructible]).  
  
(In https://www.boost.org/doc/libs/1_67_0/doc/html/boost_asio/overview/cpp2011/move_handlers.html it still says `However, handler types are still required to be copy constructible.`, though.)  
  
So I guess `the handlers are moved when possible.` now is `the handlers are always moved`?  
  
Edit: But there are still copyable LegacyCompletionHandler -> https://www.boost.org/doc/libs/1_67_0/doc/html/boost_asio/reference/LegacyCompletionHandler.html

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 00:49:35 UTC  
> Url: https://github.com/boostorg/asio/issues/92#issuecomment-752289178  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#602](https://github.com/chriskohlhoff/asio/issues/602).
