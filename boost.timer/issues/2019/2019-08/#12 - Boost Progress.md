# #12 - Boost Progress [Closed]

> Username: r-barnes  
> Created at: 2019-08-19 22:36:23 UTC  
> Updated at: 2020-11-20 02:16:27 UTC  
> Closed at: 2020-11-20 02:16:27 UTC  
> Url: https://github.com/boostorg/timer/issues/12  

[boost/progress.hpp](https://www.boost.org/doc/libs/1_71_0/boost/progress.hpp) warns me:  
  
    include/boost/timer.hpp:21:98: note: #pragma message: This header is deprecated.   
    Use the facilities in <boost/timer/timer.hpp> instead.  
    BOOST_HEADER_DEPRECATED( "the facilities in <boost/timer/timer.hpp>" )  
  
But when I look in [boost/timer.hpp](https://www.boost.org/doc/libs/1_71_0/boost/timer/timer.hpp) there's no obvious path to upgrade, since there is no `boost::progress_display` class.  
  
How am I intended to deal with the deprecation of `progress.hpp`, other than rewriting it?

---

## Comment 1

> Username: pdimov  
> Created at: 2019-08-21 16:15:26 UTC  
> Url: https://github.com/boostorg/timer/issues/12#issuecomment-523531291  

Fair point. I added the deprecation message because the old timers have been explicitly marked as deprecated in the documentation for a long while (since Boost 1.53, https://www.boost.org/doc/libs/1_53_0/libs/timer/doc/index.html.)  
  
But it's true that `progress_display` has no "modern" equivalent.  
  
I'm not sure what's the best way to handle this; add `boost::timer::progress_display` with the same contents as the deprecated one?

---

## Comment 2

> Username: glenfe  
> Created at: 2019-08-21 16:52:06 UTC  
> Url: https://github.com/boostorg/timer/issues/12#issuecomment-523548140  

Adding `boost::timer::progress_display` in a new header, <boost/timer/progress_display.hpp>. With the same contents is fine. I don't think we're using it in other Boost libraries, and users are probably already aware that it won't receive updates (though contributions wouldn't be turned away).

---

## Comment 3

> Username: r-barnes  
> Created at: 2019-08-21 18:23:24 UTC  
> Url: https://github.com/boostorg/timer/issues/12#issuecomment-523589964  

This seems reasonable to me.

---

## Comment 4

> Username: r-barnes  
> Created at: 2019-08-23 18:12:22 UTC  
> Url: https://github.com/boostorg/timer/issues/12#issuecomment-524411629  

Should I make a PR?

---

## Comment 5

> Username: pdimov  
> Created at: 2019-08-24 12:59:35 UTC  
> Url: https://github.com/boostorg/timer/issues/12#issuecomment-524548326  

No need, thanks. See https://github.com/boostorg/timer/commit/c221a60102ed618a82116f84407de3f1713aa10f.

---

## Comment 6

> Username: r-barnes  
> Created at: 2019-08-27 17:58:52 UTC  
> Url: https://github.com/boostorg/timer/issues/12#issuecomment-525414997  

Cool, I'll close this then.

---

## Comment 7

> Username: r-barnes  
> Created at: 2019-08-27 18:00:13 UTC  
> Url: https://github.com/boostorg/timer/issues/12#issuecomment-525415522  

Oh - I wonder if you know when that might make it into a Boost release?

---

## Comment 8

> Username: glenfe  
> Created at: 2019-08-27 19:17:34 UTC  
> Url: https://github.com/boostorg/timer/issues/12#issuecomment-525444872  

It should make it into 1.72.
