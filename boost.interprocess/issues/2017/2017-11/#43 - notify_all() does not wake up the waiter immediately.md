# #43 - notify_all() does not wake up the waiter immediately [Closed]

> Username: poukill  
> Created at: 2017-11-14 16:25:18 UTC  
> Updated at: 2018-02-08 09:01:13 UTC  
> Closed at: 2018-02-08 09:01:13 UTC  
> Url: https://github.com/boostorg/interprocess/issues/43  

Hello there,  
  
I'm working on a big project on Windows where two processes A and B exchange data using boost.interprocess. The problem is : when I use `notify_all()`, it takes about **15 msec** to wake up the waiter (I posted my problem yesterday here: https://stackoverflow.com/questions/47269091/boost-interprocess-notify-performance  
  
I end up writing a small exemple that reproduces my problem here: https://github.com/poukill/boost_notify_latency  
  
Today I tried to use **Windows Events** instead of the _wait()/notify_all()_ function and I got 0.04ms of latency every time, which is very good. Somehow the implementation of the `notify_all()` seems slow on Windows (trapped into context switch ???)  
  
Could you help me on that issue ?  
Thanks a lot,  
Gwen

---

## Comment 1

> Username: igaztanaga  
> Created at: 2017-12-08 11:57:25 UTC  
> Url: https://github.com/boostorg/interprocess/issues/43#issuecomment-350246612  

Window simplementations of mutex and condition variables for shared memory are not efficient at all. It's a defficiency of the library that I haven't found time to solve.  
  
There was an experimental implementation of those primitives using Windows-kernel based primitives and some hackery. Try to disable in boost/interprocess/detail/workaround.hpp in the  
  
_#if defined(_WIN32) || defined(__WIN32__) || defined(WIN32)_  
  
section, the following define:  
  
_BOOST_INTERPROCESS_FORCE_GENERIC_EMULATION_  
  
and see if your situation improves. This also changes the binary interface of message queues so you wont' be able to communicate two message queues, one using the default implementation and the other one the experimental one.

---

## Comment 2

> Username: poukill  
> Created at: 2018-01-24 11:42:43 UTC  
> Updated at: 2018-02-07 16:55:06 UTC  
> Url: https://github.com/boostorg/interprocess/issues/43#issuecomment-360105440  

Thanks for your message.  
Under Linux indeed there is no latency at all: ~250 microsec. Nice ! ^^  
Why not using CreateEvent/WaitForSingleObject under Windows and pthread_condition under Linux ?

---

## Comment 3

> Username: igaztanaga  
> Created at: 2018-01-27 17:01:49 UTC  
> Url: https://github.com/boostorg/interprocess/issues/43#issuecomment-360998256  

Because Windows events can't be placed in shared memory, which is what Boost.Interprocess synchronization primitives are about.

---

## Comment 4

> Username: poukill  
> Created at: 2018-02-07 17:28:14 UTC  
> Updated at: 2018-02-07 20:01:12 UTC  
> Url: https://github.com/boostorg/interprocess/issues/43#issuecomment-363845586  

For information, I used a `#pragma pack` for my struct in shared memory. This lead to 100% CPU when waiting for notify on Linux.... This was explained by this thread : https://stackoverflow.com/questions/22166474/pthread-cond-wait-doesnt-make-cpu-sleep  
  
Maybe this issue is worth mentionning in the Boost.Interprocess documentation ?  
Best,

---

## Comment 5

> Username: poukill  
> Created at: 2018-02-08 09:01:13 UTC  
> Url: https://github.com/boostorg/interprocess/issues/43#issuecomment-364045596  

By the way, I have disabled `BOOST_INTERPROCESS_FORCE_GENERIC_EMULATION` and my situation improved a lot. From 31 ms I go down to 1 ms which is way better !  
Thanks again !
