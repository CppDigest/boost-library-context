# #6 Only use 'pragma once' when supported. [Closed]

> Username: danieljames  
> Created at: 2014-03-20 23:19:40 UTC  
> Updated at: 2018-04-07 21:07:31 UTC  
> Closed at: 2018-04-07 21:07:30 UTC  
> Url: https://github.com/boostorg/python/pull/6  

The checks were removed in 42e7d7bbb3c2b0994169b2d02b9e6767db90b08a. This is a little better as most compilers support it nowadays.

---

## Review 1 [Changes requested]

> Username: stefanseefeld  
> Created_at: 2016-09-29 12:19:48 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/python/pull/6#pullrequestreview-2125540  

It isn't clear to me that we need these pragmas at all, given that the headers also use the usual header guards. Wouldn't it be simpler to just remove these 'pragma once' lines as redundant ?

---

## Comment 2

> Username: danieljames  
> Created_at: 2016-09-29 18:28:52 UTC  
> Url: https://github.com/boostorg/python/pull/6#issuecomment-250551436  

Sure, if that's what you prefer.

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2018-04-07 21:07:30 UTC  
> Url: https://github.com/boostorg/python/pull/6#issuecomment-379499295  

Close as obsolete.

---
