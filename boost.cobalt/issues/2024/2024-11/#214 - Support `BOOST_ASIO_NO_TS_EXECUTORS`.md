# #214 - Support `BOOST_ASIO_NO_TS_EXECUTORS` ? [Closed]

> Username: Tradias  
> Created at: 2024-11-26 12:53:42 UTC  
> Updated at: 2024-12-18 09:37:42 UTC  
> Closed at: 2024-12-13 03:04:24 UTC  
> Url: https://github.com/boostorg/cobalt/issues/214  

I honestly don't know what the story with this preprocessor macro in Asio is. Will it become the default, will it be removed, ... ?  
  
Anyways this code:  
  
https://github.com/boostorg/cobalt/blob/e69138f36bee13cb8f1bcb7a9f9c018add86b14d/include/boost/cobalt/detail/task.hpp#L315  
  
makes use of `asio::bad_executor` which is guarded by it. See  
  
https://github.com/boostorg/asio/blob/27b7596c335363ff4c8a83c26772f6737aff9dd4/include/boost/asio/executor.hpp#L20-L37

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-12-10 12:59:29 UTC  
> Url: https://github.com/boostorg/cobalt/issues/214#issuecomment-2531571683  

This all seems to be connected to the weird story around the executors TS. Are you using io_context::executor_type as executor?

---

## Comment 2

> Username: Tradias  
> Created at: 2024-12-10 13:15:19 UTC  
> Url: https://github.com/boostorg/cobalt/issues/214#issuecomment-2531606944  

I am using https://github.com/Tradias/asio-grpc/blob/master/src/agrpc/grpc_executor.hpp whose implementation is very similar to `io_context::executor_type`.  
  
Honestly I have no strong reason to define this macro. I just use it to get rid of some functionality that I shouldn't rely on since the executor TS appears to be superseded by sender/receiver.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2024-12-11 00:11:29 UTC  
> Url: https://github.com/boostorg/cobalt/issues/214#issuecomment-2533289644  

Got it. It seems reasonable to support in cobalt, so I'll just change the exception type when the macro is defined.  
  
I would be very interested in testing a cobalt setup with a third party executor, actually. I only used this for toy examples so far.

---

## Comment 4

> Username: Tradias  
> Created at: 2024-12-16 15:45:18 UTC  
> Url: https://github.com/boostorg/cobalt/issues/214#issuecomment-2545976484  

My little test seems to compile now, thanks for the fix.  
  
Now on to supporting MacOS😛

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2024-12-18 08:52:21 UTC  
> Url: https://github.com/boostorg/cobalt/issues/214#issuecomment-2550723780  

What's the MacOS issue?

---

## Comment 6

> Username: Tradias  
> Created at: 2024-12-18 09:37:40 UTC  
> Url: https://github.com/boostorg/cobalt/issues/214#issuecomment-2550838118  

https://github.com/boostorg/cobalt/issues/179
