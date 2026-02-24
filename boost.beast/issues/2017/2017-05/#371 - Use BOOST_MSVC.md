# #371 - Use BOOST_MSVC [Closed]

> Username: vinniefalco  
> Created at: 2017-05-21 19:17:21 UTC  
> Updated at: 2017-08-16 00:15:20 UTC  
> Closed at: 2017-08-16 00:15:20 UTC  
> Url: https://github.com/boostorg/beast/issues/371  

Check all use of `_MSC_VER` see if the defects are still present in the minimum required version and if not, remove it.  
  
Otherwise use `BOOST_MSVC`, check the version if possible, use the workaround in the most narrow scope.  
  
>if at all possible, leave some clue as to why that code is there.. ideally a link to a bug report for a workaround

---

## Comment 1

> Username: glenfe  
> Created at: 2017-05-21 19:19:14 UTC  
> Url: https://github.com/boostorg/beast/issues/371#issuecomment-302956903  

Something like `#if BOOST_WORKAROUND(BOOST_MSVC, < 1910)`  would be ideal.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-05-21 19:22:23 UTC  
> Url: https://github.com/boostorg/beast/issues/371#issuecomment-302957095  

`#if BOOST_WORKAROUND(BOOST_MSVC, < version)`  or if all versions up until the current version exhibit that problem, something like: `#if BOOST_WORKAROUND(BOOST_MSVC, BOOST_TESTED_AT(current))`.    Boost.Config is the better detection mechanism generally

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-05-23 03:56:17 UTC  
> Url: https://github.com/boostorg/beast/issues/371#issuecomment-303283913  

What about here  
https://github.com/vinniefalco/Beast/blob/master/extras/beast/unit_test/dstream.hpp#L17

---

## Comment 4

> Username: glenfe  
> Created at: 2017-05-23 04:09:44 UTC  
> Url: https://github.com/boostorg/beast/issues/371#issuecomment-303285314  

Add OutputDebugStringA and OutputDebugStringW to Boost.WinAPI. See https://github.com/boostorg/winapi

---

## Comment 5

> Username: glenfe  
> Created at: 2017-05-23 04:11:33 UTC  
> Url: https://github.com/boostorg/beast/issues/371#issuecomment-303285498  

And leverage BOOST_WINDOWS detection macro to identify Windows (more so than just MSVC).

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-08-16 00:15:20 UTC  
> Url: https://github.com/boostorg/beast/issues/371#issuecomment-322623439  

DONE
