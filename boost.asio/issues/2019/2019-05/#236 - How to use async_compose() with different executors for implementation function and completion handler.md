# #236 - How to use async_compose() with different executors for implementation function and completion handler [Closed]

> Username: traceon  
> Created at: 2019-05-12 01:11:33 UTC  
> Updated at: 2020-12-30 01:04:07 UTC  
> Closed at: 2020-12-30 01:04:06 UTC  
> Url: https://github.com/boostorg/asio/issues/236  

According to this, the executor associated with the completion handler will be used:  
https://github.com/boostorg/asio/blob/f2e6af2693df0a7603184d73d9f3e34d245fde71/include/boost/asio/impl/compose.hpp#L187-L190  
Is it possible to somehow bind a different executor to the **implementation of the composed asynchronous operation** too, or I can't use `async_compose()` for that and I need to follow `cpp11/operations/composed_6.cpp` example and write my own?

---

## Comment 1

> Username: djarek  
> Created at: 2019-05-12 22:14:33 UTC  
> Url: https://github.com/boostorg/asio/issues/236#issuecomment-491633769  

Why would you want to override the handler's associated executor?

---

## Comment 2

> Username: traceon  
> Created at: 2019-05-12 23:02:51 UTC  
> Url: https://github.com/boostorg/asio/issues/236#issuecomment-491636694  

I don't want to override it for handler. I want to set a custom one for the code that runs before invoking the completion handler, e.g. when the implementation of the operation needs its own strand.

---

## Comment 3

> Username: djarek  
> Created at: 2019-05-13 16:36:52 UTC  
> Url: https://github.com/boostorg/asio/issues/236#issuecomment-491896453  

I don't think`async_compose` is the appropriate facility for this purpose. If you want to run some code on a different executor and invoke the handler on the associated executor, you'll need to manage `post`/`dispatch` appropriately on your own.

---

## Comment 4

> Username: ghost  
> Created at: 2020-12-30 01:04:05 UTC  
> Url: https://github.com/boostorg/asio/issues/236#issuecomment-752292069  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#690](https://github.com/chriskohlhoff/asio/issues/690).
