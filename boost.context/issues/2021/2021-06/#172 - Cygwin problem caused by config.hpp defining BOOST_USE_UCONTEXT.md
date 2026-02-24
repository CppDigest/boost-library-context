# #172 - Cygwin problem caused by config.hpp defining BOOST_USE_UCONTEXT [Open]

> Username: pdimov  
> Created at: 2021-06-03 16:43:43 UTC  
> Updated at: 2022-09-18 14:19:37 UTC  
> Url: https://github.com/boostorg/context/issues/172  

https://github.com/boostorg/context/blob/fb0f3eab4d14ce2de4cf3a5446c8a0f735a9282d/include/boost/context/detail/config.hpp#L33-L35  
  
defines `BOOST_USE_UCONTEXT` on Cygwin. This causes Fiber to fail to build, because it tries to use ucontext, but Context is built with fcontext.  
  
If Context is built with ucontext instead, Fiber builds, but then Coroutine fails, because it needs fcontext:  
  
https://github.com/boostorg/coroutine/blob/4e47f98d44ad0d5d487788ed6eb4d347489e5483/src/detail/coroutine_context.cpp#L68  
  
Should `config.hpp` be defining `BOOST_USE_UCONTEXT` on Cygwin?

---

## Comment 1

> Username: pdimov  
> Created at: 2021-06-03 16:45:29 UTC  
> Url: https://github.com/boostorg/context/issues/172#issuecomment-854021291  

Ah, that's https://github.com/boostorg/context/pull/155. Well it breaks one of Fiber or Coroutine, then.

---

## Comment 2

> Username: olk  
> Created at: 2021-06-03 18:53:41 UTC  
> Url: https://github.com/boostorg/context/issues/172#issuecomment-854100538  

boost.coroutine is deprecated

---

## Comment 3

> Username: pdimov  
> Created at: 2021-06-03 19:00:33 UTC  
> Url: https://github.com/boostorg/context/issues/172#issuecomment-854104454  

ASIO depends on Coroutine, so whether it's "deprecated" or not is of no relevance. It must work.

---

## Comment 4

> Username: olk  
> Created at: 2021-06-03 19:57:42 UTC  
> Url: https://github.com/boostorg/context/issues/172#issuecomment-854138479  

ASIO should switch to boost.context instead of using deprecated  
boost.coroutine.  
  
Peter Dimov ***@***.***> schrieb am Do., 3. Juni 2021, 21:00:  
  
> ASIO depends on Coroutine, so whether it's "deprecated" or not is of no  
> relevance. It must work.  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/context/issues/172#issuecomment-854104454>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAG7IQAFT52VI24AOK2TICTTQ7GN7ANCNFSM46BERHOA>  
> .  
>

---

## Comment 5

> Username: pdimov  
> Created at: 2021-06-03 22:06:37 UTC  
> Url: https://github.com/boostorg/context/issues/172#issuecomment-854217033  

That's not my department. I'm just seeing that Fiber fails to build on Cygwin, and am telling you that it does, and why.

---

## Comment 6

> Username: pdimov  
> Created at: 2021-06-03 22:30:02 UTC  
> Url: https://github.com/boostorg/context/issues/172#issuecomment-854227230  

New Fiber failure after the latest changes:  
```  
[ 21%] Building CXX object libs/fiber/CMakeFiles/boost_fiber.dir/src/algo/algorithm.cpp.o  
In file included from /home/travis/build/boostorg/boost-root/libs/fiber/src/algo/algorithm.cpp:9:0:  
/home/travis/build/boostorg/boost-root/libs/fiber/include/boost/fiber/context.hpp:26:42: fatal error: boost/context/detail/apply.hpp: No such file or directory  
compilation terminated.  
```

---

## Comment 7

> Username: olk  
> Created at: 2021-06-04 04:53:00 UTC  
> Updated at: 2021-06-04 04:53:10 UTC  
> Url: https://github.com/boostorg/context/issues/172#issuecomment-854356832  

> New Fiber failure after the latest changes  
  
fixed

---

## Comment 8

> Username: pdimov  
> Created at: 2021-06-04 23:14:02 UTC  
> Url: https://github.com/boostorg/context/issues/172#issuecomment-855098334  

> fixed  
  
Indeed, thanks.  
  
> ASIO should switch to boost.context instead of using deprecated boost.coroutine.  
  
I've looked at your PR https://github.com/boostorg/asio/pull/55 that does exactly that, but it seems to me that even if accepted, it won't solve the issue here. It would just move the dependency on `fcontext` from Coroutine to Asio, but building Context with `ucontext` will still break `asio::spawn`.

---

## Comment 9

> Username: corngood  
> Created at: 2021-12-22 02:23:43 UTC  
> Url: https://github.com/boostorg/context/issues/172#issuecomment-999233662  

I've been able to get `fcontext` working in cygwin as described here:  
  
https://cygwin.com/pipermail/cygwin-developers/2020-September/011970.html  
  
It also seems the `ucontext` is broken for the same reason that `fcontext` was:  
  
https://cygwin.com/pipermail/cygwin-developers/2021-December/012536.html

---

## Comment 10

> Username: olk  
> Created at: 2022-09-18 14:19:37 UTC  
> Url: https://github.com/boostorg/context/issues/172#issuecomment-1250318712  

> I've been able to get `fcontext` working in cygwin as described here:  
>   
> https://cygwin.com/pipermail/cygwin-developers/2020-September/011970.html  
>   
> It also seems the `ucontext` is broken for the same reason that `fcontext` was:  
>   
> https://cygwin.com/pipermail/cygwin-developers/2021-December/012536.html  
  
This means that some fixes have to be done in the cygwin?!
