# #1005 - Warning C4355: 'this' used in base member initializer list [Closed]

> Username: SimonEbner  
> Created at: 2018-02-03 16:20:50 UTC  
> Updated at: 2018-02-24 00:09:45 UTC  
> Closed at: 2018-02-24 00:09:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1005  

### Version of Beast  
144  
  
### All relevant compiler information  
MSVC 15.5.6  
  
I am getting the warning C4355: 'this' used in base member initializer list in file https://github.com/boostorg/beast/blob/d52e8290453fe445c8700b4819fe8e4f45633a02/include/boost/beast/core/detail/static_ostream.hpp#L125  
  
It seems like a legit use to me and msvc being overly cautious. I am curious what is the official policy in these cases, Ignoring / muting in boost / muting outside boost?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-02-03 18:36:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1005#issuecomment-362843319  

Weird because I don't see that warning, and it would bug me if I did since I use MSVC day to day. I prefer to fix them, and to do so without resorting to compiler switches. Sometimes that requires a creative solution.

---

## Comment 2

> Username: SimonEbner  
> Created at: 2018-02-03 20:15:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1005#issuecomment-362850762  

The issue popped up for me since updating to 14.12.25810 toolchain afaik

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-02-21 21:34:56 UTC  
> Updated at: 2018-02-21 21:35:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1005#issuecomment-367480758  

There's nothing I can do about this, since it doesn't happen in the latest toolchain (15.5.5) and there's really no workaround that I can think of, unless you can come up with one? I prefer not to squelch the warning in the library. It is difficult to silence it anyway without affecting the user's translation units.

---

## Comment 4

> Username: SimonEbner  
> Created at: 2018-02-24 00:09:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1005#issuecomment-368175391  

Okay, many thanks for your feedback.
