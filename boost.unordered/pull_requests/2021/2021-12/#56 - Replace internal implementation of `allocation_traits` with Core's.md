# #56 Replace internal implementation of `allocation_traits` with Core's [Merged]

> Username: cmazakas  
> Created at: 2021-12-07 16:45:05 UTC  
> Updated at: 2021-12-07 23:36:18 UTC  
> Merged at: 2021-12-07 23:23:02 UTC  
> Closed at: 2021-12-07 23:23:03 UTC  
> Url: https://github.com/boostorg/unordered/pull/56  

Closes #30

---

## Comment 1

> Username: glenfe  
> Created_at: 2021-12-07 18:00:33 UTC  
> Url: https://github.com/boostorg/unordered/pull/56#issuecomment-988150916  

Get rid of rebind_wrap. Why is it needed? There can't be too many places using it.

---

## Comment 2

> Username: glenfe  
> Created_at: 2021-12-07 18:05:35 UTC  
> Url: https://github.com/boostorg/unordered/pull/56#issuecomment-988154330  

Also, why even support BOOST_UNORDERED_USE_ALLOCATOR_TRAITS at all? i.e. Always unconditionally just use <boost/core/allocator_traits.hpp>

---

## Comment 3

> Username: pdimov  
> Created_at: 2021-12-07 18:19:44 UTC  
> Url: https://github.com/boostorg/unordered/pull/56#issuecomment-988163095  

> i.e. Always unconditionally just use <boost/core/allocator_traits.hpp>  
  
That's what it does?  
  
> Get rid of rebind_wrap.  
  
Yeah, in a subsequent PR, let's merge this first.  
  
The Container include should be removed.

---

## Comment 4

> Username: glenfe  
> Created_at: 2021-12-07 18:33:06 UTC  
> Url: https://github.com/boostorg/unordered/pull/56#issuecomment-988171753  

I noticed the `BOOST_UNORDERED_USE_ALLOCATOR_TRAITS ` macro still present, but now I gather from the last comment that its removal will similarly be in a follow-up PR.

---

## Comment 5

> Username: pdimov  
> Created_at: 2021-12-07 18:34:58 UTC  
> Url: https://github.com/boostorg/unordered/pull/56#issuecomment-988172971  

It's checked in the tests so the removal should be a separate diff.

---

## Comment 6

> Username: cmazakas  
> Created_at: 2021-12-07 18:55:35 UTC  
> Url: https://github.com/boostorg/unordered/pull/56#issuecomment-988186566  

@glenfe How would you like the failing test case (the inherited SOCCC one) to be handled? For example, is it acceptable to simply `#ifdef` this test out for C++03 mode?

---

## Comment 7

> Username: glenfe  
> Created_at: 2021-12-07 19:02:54 UTC  
> Url: https://github.com/boostorg/unordered/pull/56#issuecomment-988190911  

It's worth it to have a test that checks SOCCC otherwise I did the work for nothing. :) Have an 03 test with an allocator that does define a SOCCC member that is detected with our current detection emulation.

---

## Comment 8

> Username: pdimov  
> Created_at: 2021-12-07 19:06:31 UTC  
> Url: https://github.com/boostorg/unordered/pull/56#issuecomment-988193204  

There are several such tests, and only one of them is failing. These pass:  
https://github.com/boostorg/unordered/blob/00b504ebc51e1ecda9347106ed0c91dba77c651a/test/unordered/allocator_traits.cpp#L130  
https://github.com/boostorg/unordered/blob/00b504ebc51e1ecda9347106ed0c91dba77c651a/test/unordered/allocator_traits.cpp#L172  
https://github.com/boostorg/unordered/blob/00b504ebc51e1ecda9347106ed0c91dba77c651a/test/unordered/allocator_traits.cpp#L278  
  
This one fails, because the member function is defined in a base class:  
https://github.com/boostorg/unordered/blob/00b504ebc51e1ecda9347106ed0c91dba77c651a/test/unordered/allocator_traits.cpp#L216

---

## Comment 9

> Username: glenfe  
> Created_at: 2021-12-07 19:08:22 UTC  
> Url: https://github.com/boostorg/unordered/pull/56#issuecomment-988194383  

In that case, yeah, only enable that one in when `!defined(BOOST_NO_CXX11_ALLOCATOR)`.

---
