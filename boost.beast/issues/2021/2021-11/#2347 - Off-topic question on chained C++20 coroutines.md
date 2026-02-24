# #2347 - Off-topic question on chained C++20 coroutines [Closed]

> Username: brjoha  
> Created at: 2021-11-21 19:27:13 UTC  
> Updated at: 2021-11-23 22:41:09 UTC  
> Closed at: 2021-11-23 22:41:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2347  

This is more of an asio question, but I think I'm more likely to get a response here (I hope :).  
  
I was previously using asio stackful coroutines and had a function that took an asio::yield_context argument, passed it through to a native asio async function, and used asio::async_result to create and return the result.  In converting to C++20 stackless coroutines, the function now looks something like this...  
```  
template<typename T>  
boost::asio::awaitable<bool> my_async_op(T&& token) {  
    return boost::asio::async_initiate<T, void(boost::system::error_code, bool)>(  
        [&](auto&& handler) -> void {  
            boost::system::error_code ec;  
            ...  
            co_await boost::asio::some_async_op(boost::asio::redirect_error(boost::asio::use_awaitable, ec));  
            ...  
            handler(ec, true);  
        },  
        token  
    );  
}  
```  
The problem is that nested call to the native asio async function, where gcc complains with ```error: unable to find the promise type for this coroutine```.  
  
I've tried to find an example of a custom awaiter with a chained coroutine, but no luck.  Any guidance would be much appreciated.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-11-21 20:34:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2347#issuecomment-974891495  

There are a couple of misunderstandings demonstrated by the code. Do you have a repo where you can demonstrate what you are trying to achieve?   
maybe write it with boost coroutines and I’ll help you translate it

---

## Comment 2

> Username: brjoha  
> Created at: 2021-11-21 20:40:43 UTC  
> Updated at: 2021-11-21 20:43:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2347#issuecomment-974892304  

The original function using asio stackful coroutines looked like this..  
```  
bool my_async_op(boost::asio::yield_context yield) {  
    boost::system::error_code ec;  
    using tResult = boost::asion::async_result<boost::asio::yield_context, void(boost::system::error_code, bool)>;  
    tResult::completion_handler_type handler(yield);  
    tResult result(handler);  
    ...  
    boost::asio::some_async_op(yield[ec]);  
    ...  
    handler(ec, true);  
    return result.get();  
}  
```  
The function is like a wrapper for the native asio async function, with a little pre/post processing around the call.

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-11-21 21:42:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2347#issuecomment-974900840  

The equivalent function would be:  
```  
co_await boost::asio::some_async_op(boost::asio::use_awaitable);  
// if you get here, implies true. Otherwise, exception.  
```

---

## Comment 4

> Username: brjoha  
> Created at: 2021-11-21 23:34:41 UTC  
> Updated at: 2021-11-22 04:12:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2347#issuecomment-974920291  

Isn't that what I have in the original post, except that redirect_error is used to map any exception?  
  
Or, are you saying I shouldn't be using async_initiate?  If I do that, how does my wrapper function properly return from being awaited on?  That is...  
```  
auto result = co_await my_async_op(boost::asio::redirect_error(boost::asio::use_awaitable, ec));  
```  
  
The fuller picture is like this, with a call chain of three functions...  
  
1. async_entry() co_await's  
2. my_async_op() co_await's  
3. asio::some_async_op()  
  
...kicked off with a co_spawn of async_entry().  
  
With C++20 coroutines being stackless, my understanding is that each call in the chain needs to be awaitable.  So the code in the original post is my attempt to make my_async_op() awaitable while it's waiting on asio::some_async_op().

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-11-22 07:18:13 UTC  
> Updated at: 2021-11-22 07:18:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2347#issuecomment-975196590  

`async_result` is the general purpose mechanism for turning a completion token into a completion handler and then optionally delivering the result of calling that handler to some kind of promise.  
  
Since you are have already settled on coroutines as your promise type, you can write your code in terms of coroutines. There is no need to involve async_result unless you are writing your own async operations that are designed to work with any token type.  
  
Note that if you need to do this, the modern way is to use the `asio::async_initiate<>` template function.

---

## Comment 6

> Username: madmongo1  
> Created at: 2021-11-22 07:24:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2347#issuecomment-975200328  

So a possible implementation of your function might be:  
  
```  
boost::asio::awaitable<bool>   
my_async_op(boost::system::error_code& ec) {  
    using boost::asio::redirect_error;  
    using boost::asio::use_awaitable;  
  
    co_return co_await boost::asio::some_async_op(redirect_error(use_awaitable, ec));  
}  
```  
  
But you may as well not bother with the function in this case. Simply call the async operation directly from your code:  
  
```  
    using boost::asio::experimental::as_tuple;  
      
    auto [ec, yesno] = co_await boost::asio::some_async_op(as_tuple(use_awaitable));  
  
```

---

## Comment 7

> Username: brjoha  
> Created at: 2021-11-22 15:44:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2347#issuecomment-975657132  

I have some pre/post processing that occurs around the call to the native asio function and don't want to replicate that at every call site, so that's why it's wrapped in my own function.  
  
I think I understand what you're saying, but am still struggling with handler semantics.  I'll illustrate exactly how the call chain is invoked to try and clarify my question...  
```  
asio::co_spawn(ctx, async_entry, asio::detached);  
  
asio::awaitable<void> async_entry() {  
   while (some_condition) {  
       boost::system::error_code ec;  
       auto result = co_await my_asyn_op(asio::redirect_error(asio::use_awaitable, ec);  
       // ... other work ...  
   }  
}  
  
asio::awaitable<bool> my_async_op(???) {  
   // ... pre-processing ...  
   boost::system::error_code ec;  
   co_await asio::some_async_op(asio::redirect_error(asio::use_awaitable, ec);  
   // ... post-processing ...  
   co_return true;  
}  
```  
For async_entry() to await my_async_op(), it needs to accept a handler argument does it not?

---

## Comment 8

> Username: madmongo1  
> Created at: 2021-11-22 15:48:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2347#issuecomment-975661196  

> For async_entry() to await my_async_op(), it needs to accept a handler argument does it not?  
  
No, `my_async_op` is already a coroutine because it contains a co_await and/or co_return. So there's no need to tell it again that it's a coroutine by passing another `use_awaitable` token into it.

---

## Comment 9

> Username: brjoha  
> Created at: 2021-11-22 15:50:31 UTC  
> Updated at: 2021-11-22 15:51:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2347#issuecomment-975663070  

But then how does my_async_op() return both ec and result expected by async_entry() ?

---

## Comment 10

> Username: madmongo1  
> Created at: 2021-11-22 15:52:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2347#issuecomment-975664402  

You have a number of options, you can return a tuple or you can pass by reference.  
  
Here's one way:  
  
```  
asio::awaitable< std::tuple<error_code, bool> >   
my_async_op()   
{  
   // ... pre-processing ...  
   boost::system::error_code ec;  
   co_await asio::some_async_op(asio::redirect_error(asio::use_awaitable, ec);  
   // ... post-processing ...  
   co_return std::make_tuple(ec, true);  
}  
```

---

## Comment 11

> Username: brjoha  
> Created at: 2021-11-22 15:56:38 UTC  
> Updated at: 2021-11-22 16:01:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2347#issuecomment-975669038  

It's confusing to me why my_async_op() is invoked differently than asio::some_async_op().  
  
asio::some_async_op() can accept different handler types (i.e., use_waitable, redirect_error, as_tuple), so shouldn't my_async_op() have the same capability?  
  
Edit: Or at lesast it's my preference that my_async_op() have the same invocation capabilities as native asio functions.

---

## Comment 12

> Username: madmongo1  
> Created at: 2021-11-22 16:12:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2347#issuecomment-975683762  

In that case you must implement it as a native asio template function, which invokes composed operation.  
If you can be more specific about that "pre-processing" and "post-processing" involves and what the expected signature of the completion handler should be, I'm happy to write an example for you.  
  
At the moment I am assuming that the completion handler signature is `void(error_code, bool)` ?

---

## Comment 13

> Username: brjoha  
> Created at: 2021-11-22 16:30:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2347#issuecomment-975700428  

Ok, that's what I thought and is where I was trying to get to with the use of async_initiate<> in the original post.  
  
Yes, that's correct that the handler signature is `void(error_code, bool)`.  
  
Can ignore the pre/post processing and assume a success path returning `true` after the co_await on native asio function for now.

---

## Comment 14

> Username: madmongo1  
> Created at: 2021-11-22 16:45:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2347#issuecomment-975718418  

What is a good example of `asio::some_async_op` ?

---

## Comment 15

> Username: brjoha  
> Created at: 2021-11-22 16:53:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2347#issuecomment-975725183  

asio::steady_timer::async_wait() is the actual call, using a timer member variable that may get canceled elsewhere.

---

## Comment 16

> Username: madmongo1  
> Created at: 2021-11-22 17:01:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2347#issuecomment-975731532  

ok no worries.

---

## Comment 17

> Username: madmongo1  
> Created at: 2021-11-22 17:48:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2347#issuecomment-975772260  

https://github.com/beast-issues/2347/blob/3134a4304003f92a54c684381b3b40d68a915fd6/main.cpp#L121  
  
Here you go - a working example to get us started.

---

## Comment 18

> Username: brjoha  
> Created at: 2021-11-23 02:42:55 UTC  
> Updated at: 2021-11-23 03:04:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2347#issuecomment-976112615  

That's more than expected, and much appreciated.  
  
If I'm following, it's not possible to simply `co_await timer->async_wait(...)` on line 102.  
  
I'm surprised this is so much more complicated than the original implementation using asio stackful coroutines.  It almost forces a flat call structure, with all the drawbacks that entails.

---

## Comment 19

> Username: madmongo1  
> Created at: 2021-11-23 07:16:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2347#issuecomment-976216825  

No because that that point you're not in a coroutine.  
  
C++ coroutines are not functions - they are sequences of function calls with attached state. They just happen to look like functions in the source code. This is because unlike boost.context-based coroutines, they are stackless.  
  
The upshot of this is that we essentially have two distinct "languages" that share the same syntax. Coroutines can call functions and therefore switch back into C++, but functions cannot directly "call" coroutines for effect, because all calling a coroutine does is prepare it and return the awaitable. The work starts happening when you co_await the awaitable, and co_await can only appear in a coroutine.

---

## Comment 20

> Username: brjoha  
> Created at: 2021-11-23 19:03:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2347#issuecomment-977026757  

Thx again.  I need to read/review more to get my head around this.
