# #93 - boost::coroutines2 integration? [Closed]

> Username: bitbugprime  
> Created at: 2018-03-12 00:56:44 UTC  
> Updated at: 2020-12-30 00:49:52 UTC  
> Closed at: 2020-12-30 00:49:51 UTC  
> Url: https://github.com/boostorg/asio/issues/93  

I see there's an existing pull request for this (#55), but it's been languishing in limbo for more almost six months. Is the submitted code considered incomplete or subpar or do you simply have no interest in it?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-03-12 19:10:56 UTC  
> Url: https://github.com/boostorg/asio/issues/93#issuecomment-372429107  

Maybe because it imposes the additional requirement of C++11, which is not currently a requirement of Boost.Asio?

---

## Comment 2

> Username: bitbugprime  
> Created at: 2018-03-13 03:47:27 UTC  
> Url: https://github.com/boostorg/asio/issues/93#issuecomment-372537581  

I'm sure conditional compilation can solve that problem, so laggards won't hold everybody else hostage. They don't get the new feature (coroutine2 integration), but so what?

---

## Comment 3

> Username: AndrewAMD  
> Created at: 2018-06-26 14:21:26 UTC  
> Updated at: 2018-06-26 17:08:42 UTC  
> Url: https://github.com/boostorg/asio/issues/93#issuecomment-400326958  

> Maybe because it imposes the additional requirement of C++11, which is not currently a requirement of Boost.Asio?  
  
But this pull request appears to support both C++11 and pre-C++11, if I'm not mistaken.  
  
Note: This issue title is confusing because #55 is about boost.context, not boost.coroutine2.

---

## Comment 4

> Username: ghost  
> Created at: 2020-12-30 00:49:50 UTC  
> Url: https://github.com/boostorg/asio/issues/93#issuecomment-752289217  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#603](https://github.com/chriskohlhoff/asio/issues/603).
