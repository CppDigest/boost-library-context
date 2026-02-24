# #101 Fix fibers::asio::round_robin's notify() method. [Closed]

> Username: xaqq  
> Created at: 2016-11-24 14:57:41 UTC  
> Updated at: 2016-11-25 10:41:09 UTC  
> Closed at: 2016-11-24 18:15:46 UTC  
> Url: https://github.com/boostorg/fiber/pull/101  

The notify() method of fibers::asio::round_robin doesn't give  
a chance to pending fibers to run.  
  
See #100 for more detail.

---

## Comment 1

> Username: olk  
> Created_at: 2016-11-24 15:49:55 UTC  
> Url: https://github.com/boostorg/fiber/pull/101#issuecomment-262805147  

Implementation of has already changed, see branch develop

---

## Comment 2

> Username: xaqq  
> Created_at: 2016-11-24 15:52:59 UTC  
> Url: https://github.com/boostorg/fiber/pull/101#issuecomment-262805774  

In develop, notify() simply set the timer to expires, and it seems its not enough.

---

## Comment 3

> Username: olk  
> Created_at: 2016-11-24 16:51:55 UTC  
> Url: https://github.com/boostorg/fiber/pull/101#issuecomment-262816626  

you are right - Nat and I tried to get asio::round_robin working with multiple threads running io_service::run() of one instance of io_service. Unfortunately io_service it didn't work and not all changes were rolled back.  
I'll take you example and add it to the library too - could you tell me your Name?

---

## Comment 4

> Username: olk  
> Created_at: 2016-11-24 16:53:14 UTC  
> Url: https://github.com/boostorg/fiber/pull/101#issuecomment-262816854  

merge must be into branch develop first - please change your pull request or tell me if I should do it (format-patch etc).

---

## Comment 5

> Username: xaqq  
> Created_at: 2016-11-24 17:10:15 UTC  
> Updated_at: 2016-11-24 17:11:02 UTC  
> Url: https://github.com/boostorg/fiber/pull/101#issuecomment-262819573  

I changed the base on github, but it looks like it did something ugly. Feel free to close the PR and cherry-pick the commit or something if that keeps history cleaner.  
  
Bear in mind that while this change seems to positively affect the example from #100 I think I'm still observing suspicious behavior when running my real program through valgrind. (with the use case being 2 io_service on 2 thread).  
  
My name's Arnaud Kapp.

---

## Comment 6

> Username: olk  
> Created_at: 2016-11-24 18:15:46 UTC  
> Url: https://github.com/boostorg/fiber/pull/101#issuecomment-262827935  

Arnaud, regarding to valgrind please apply valgrind=on at cmd-line.  
(see http://www.boost.org/doc/libs/1_62_0/libs/context/doc/html/context/stack/valgrind.html)

---

## Comment 7

> Username: olk  
> Created_at: 2016-11-24 18:25:59 UTC  
> Url: https://github.com/boostorg/fiber/pull/101#issuecomment-262829036  

pushed to branch develop

---

## Comment 8

> Username: xaqq  
> Created_at: 2016-11-25 10:41:09 UTC  
> Url: https://github.com/boostorg/fiber/pull/101#issuecomment-262929126  

Thanks.  
  
WRT valgrind, I already use `valgrind=on`. The issue is not invalid memory access / "client switching stack". Rather it looks like spinning. I can't reproduce w/o valgrind so it's a bit weird.  
I'll report back if I find something.

---
