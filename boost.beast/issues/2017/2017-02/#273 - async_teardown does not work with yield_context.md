# #273 - async_teardown does not work with yield_context [Closed]

> Username: mika-fischer  
> Created at: 2017-02-27 10:07:33 UTC  
> Updated at: 2017-02-27 13:41:29 UTC  
> Closed at: 2017-02-27 13:39:58 UTC  
> Url: https://github.com/boostorg/beast/issues/273  

```async_teardown(beast::websocket::teardown_tag{}, websocket_.next_layer(), yield);```  
  
gives me:  
  
```static_assert failed "TeardownHandler requirements not met"```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-02-27 11:13:46 UTC  
> Url: https://github.com/boostorg/beast/issues/273#issuecomment-282692813  

Hmm...the unit tests use coroutines so I have a hard time believing there's a bug! The assert you got means that the handler does have the right signature. Try this instead:  
```  
beast::error_code ec;  
async_teardown(  
    beast::websocket::teardown_tag{},  
        websocket_.next_layer(), yield[ec]);  
```  
  
See if that helps!

---

## Comment 2

> Username: mika-fischer  
> Created at: 2017-02-27 13:14:27 UTC  
> Url: https://github.com/boostorg/beast/issues/273#issuecomment-282716597  

Nope, same error. I don't see any tests that actually call `async_teardown`, either...

---

## Comment 3

> Username: mika-fischer  
> Created at: 2017-02-27 13:17:06 UTC  
> Url: https://github.com/boostorg/beast/issues/273#issuecomment-282717149  

I see that the requirements state that the handler itself must be callable. This is not the case for `yield_context`. You must go through `boost::asio::handler_type` and `boost::asio::async_result` to make this work, I think...

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-02-27 13:19:39 UTC  
> Url: https://github.com/boostorg/beast/issues/273#issuecomment-282717684  

hmm... you're right. Come to think of it, `teardown` and `async_teardown` shouldn't need to be called directly. They are only for the implementation of `websocket::stream`. I think the reason I exposed them is for the case where you want to instantiate `websocket::stream` with a `NextLayer` type that is not `boost::asio::ip::tcp::socket` or `boost::asio::ssl::stream`. This way user created next layer types can define a way to properly close the connection as per RFC6455.  
  
You're right that in order for this to work it needs the `async_result` bits. Which is trivial for me to add. But before I do that, why are you calling `async_teardown` directly?

---

## Comment 5

> Username: mika-fischer  
> Created at: 2017-02-27 13:25:11 UTC  
> Url: https://github.com/boostorg/beast/issues/273#issuecomment-282718868  

Ah I see. I assumed that I had to call this after calling `async_close` on the websocket. But if I understand correctly, this is called autmatically by beast, right? In that case, everything's fine and I don't need this to work with `yield_context`.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-02-27 13:26:50 UTC  
> Url: https://github.com/boostorg/beast/issues/273#issuecomment-282719218  

\phew :)  
  
Yes, `beast::websocket::stream` automatically calls `async_teardown`. And it does so without namespace qualification, so that argument-dependent lookup can find the correct overload for the case where **NextLayer** is a user-defined type that has provided implementations of `teardown` and `async_teardown`.  
  
I should probably make these interfaces non-public.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-02-27 13:30:37 UTC  
> Url: https://github.com/boostorg/beast/issues/273#issuecomment-282719991  

Actually... `teardown` and `async_teardown` have to remain public or else people who want to call them in their implementation of the overloads for their user defined types will not be able to do so. For example:  
https://github.com/vinniefalco/Beast/blob/36143be195186970818558c73d07260b76fe58f2/extras/beast/test/fail_stream.hpp#L162

---

## Comment 8

> Username: mika-fischer  
> Created at: 2017-02-27 13:40:29 UTC  
> Url: https://github.com/boostorg/beast/issues/273#issuecomment-282722216  

Maybe just add a note to the documentation, that these functions should not be called directly except when implementing overloads for custom streams?

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-02-27 13:41:29 UTC  
> Url: https://github.com/boostorg/beast/issues/273#issuecomment-282722462  

Well if you're telling me the docs should be better...you'll get no argument from me!
