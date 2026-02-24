# #137 - Is there a version of boost.fiber that will work with the TS version of ASIO? [Closed]

> Username: akoolenbourke  
> Created at: 2017-07-17 03:55:56 UTC  
> Updated at: 2022-09-18 14:39:01 UTC  
> Closed at: 2022-09-18 14:39:01 UTC  
> Url: https://github.com/boostorg/fiber/issues/137  

With the github ASIO master branch having modifications for the networking TS, will (is) there be a branch/fork of fiber will be compatible with that?

---

## Comment 1

> Username: olk  
> Created at: 2017-07-19 05:00:56 UTC  
> Url: https://github.com/boostorg/fiber/issues/137#issuecomment-316273351  

which changes of boost.asio?

---

## Comment 2

> Username: olk  
> Created at: 2017-07-19 05:06:26 UTC  
> Url: https://github.com/boostorg/fiber/issues/137#issuecomment-316274045  

change log for boost-1.65 does not contain asio

---

## Comment 3

> Username: akoolenbourke  
> Created at: 2017-07-20 08:40:41 UTC  
> Url: https://github.com/boostorg/fiber/issues/137#issuecomment-316637238  

Sorry if I wasn't clear, I wasn't asking about Boost.ASIO but ASIO. ASIO master branch is ASIO with changes to reflect the networking TS. I was wondering if there were any plans to make something like Boost.Fibre compatible with that in any way, i.e. how ASIO itself comes in Boost and non-Boost versions.  
  
Boost Fiber looks very interesting but we have decided to go with the TS version of ASIO for our async IO.

---

## Comment 4

> Username: olk  
> Created at: 2017-07-20 17:56:03 UTC  
> Url: https://github.com/boostorg/fiber/issues/137#issuecomment-316781896  

Ok, I could try to provide some fiber integration for asio

---

## Comment 5

> Username: olk  
> Created at: 2017-09-10 12:52:35 UTC  
> Url: https://github.com/boostorg/fiber/issues/137#issuecomment-328340800  

you refer to https://github.com/chriskohlhoff/networking-ts-impl, right?  
  
2017-07-20 10:40 GMT+02:00 akoolenbourke <notifications@github.com>:  
  
> Sorry if I wasn't clear, I wasn't asking about Boost.ASIO but ASIO. ASIO  
> master branch is ASIO with changes to reflect the networking TS. I was  
> wondering if there were any plans to make something like Boost.Fibre  
> compatible with that in any way, i.e. how ASIO itself comes in Boost and  
> non-Boost versions.  
>  
> Boost Fiber looks very interesting but we have decided to go with the TS  
> version of ASIO for our async IO.  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/fiber/issues/137#issuecomment-316637238>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AA30QJCkghyf3YWeaimayP96bITGH2dxks5sPxKKgaJpZM4OZhzm>  
> .  
>

---

## Comment 6

> Username: vinipsmaker2  
> Created at: 2018-02-27 19:26:23 UTC  
> Url: https://github.com/boostorg/fiber/issues/137#issuecomment-368996314  

Boost.Asio 1.66 was released with TS-changes as well: http://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/net_ts.html

---

## Comment 7

> Username: bitbugprime  
> Created at: 2020-09-17 18:12:24 UTC  
> Url: https://github.com/boostorg/fiber/issues/137#issuecomment-694409467  

Since the Networking TS version of ASIO is basically, if not formally, deprecated in Boost 1.74 in favor of standard executors, how would one write a standard executor style fiber executor? The boost fiber io_service integration definitely won't work.

---

## Comment 8

> Username: olk  
> Created at: 2020-09-17 18:32:42 UTC  
> Url: https://github.com/boostorg/fiber/issues/137#issuecomment-694420616  

I'm too busy but you are welcome to provide a fiber executor via pull request.
