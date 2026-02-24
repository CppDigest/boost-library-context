# #10 override: check for NULL when calling overrides [Closed]

> Username: mathstuf  
> Created at: 2014-09-09 06:18:09 UTC  
> Updated at: 2016-07-15 14:34:00 UTC  
> Closed at: 2015-10-17 04:04:10 UTC  
> Url: https://github.com/boostorg/python/pull/10  

PyEval_CallFunction can return NULL in the event of an error which  
causes method_result to error out. Instead, check for NULL and throw an  
exception since Python has already set it.

---

## Comment 1

> Username: hotgloupi  
> Created_at: 2014-11-04 08:03:04 UTC  
> Url: https://github.com/boostorg/python/pull/10#issuecomment-61605462  

Thanks for that ! I'm pretty sure I've encountered it.  
However I'd love to see a test case that crash before your patch :)

---

## Comment 2

> Username: mathstuf  
> Created_at: 2014-11-16 17:50:55 UTC  
> Url: https://github.com/boostorg/python/pull/10#issuecomment-63229706  

The function is undocumented, but can happen when the function fails to be called (for whatever reason). I think I hit it when I coded up an improper C function, but I think a segfault is worse than an exception.  
  
https://stackoverflow.com/questions/4922365/python-c-api-pyeval-callfunction  
https://docs.python.org/release/3.0.1/c-api/object.html#PyObject_CallObject

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2015-05-27 14:46:30 UTC  
> Url: https://github.com/boostorg/python/pull/10#issuecomment-105939548  

Please add a failing test case (which passes after this patch is applied).  
And please resubmit the pull request against the develop branch.  
Thanks !

---

## Comment 4

> Username: teeks99  
> Created_at: 2015-06-26 18:51:09 UTC  
> Url: https://github.com/boostorg/python/pull/10#issuecomment-115830263  

This should probably be merged into develop first, so that we can try it with the test runners before we put it in master. I'm not sure if that needs to happen in a different pull request.

---

## Comment 5

> Username: stefanseefeld  
> Created_at: 2015-10-17 04:04:10 UTC  
> Url: https://github.com/boostorg/python/pull/10#issuecomment-148884170  

As suggested before, please submit this (with test case) agains the develop branch.

---

## Comment 6

> Username: mathstuf  
> Created_at: 2016-06-01 16:02:55 UTC  
> Url: https://github.com/boostorg/python/pull/10#issuecomment-223041004  

There's no easy way to get this thing to return `NULL`. The only ways I can see are:  
- not passing in a tuple object (not possible to trigger from the API);  
- failing to allocate a tuple object (not easy to trigger);  
- kwargs not being a dictionary (not passed here);  
- internal recursion (not sure how exactly to test this);  
- returning `NULL` from a bound function while not setting an error (probably easiest).  
  
So I've written the code which I think should show a failure…but how the heck do I get the test suite to actually run?

---

## Comment 7

> Username: mathstuf  
> Created_at: 2016-07-15 13:58:57 UTC  
> Url: https://github.com/boostorg/python/pull/10#issuecomment-232958009  

Ping?

---

## Comment 8

> Username: stefanseefeld  
> Created_at: 2016-07-15 14:02:38 UTC  
> Url: https://github.com/boostorg/python/pull/10#issuecomment-232958927  

As per my previous comment: Please submit a PR (including a test case) against the develop branch. Thanks !

---

## Comment 9

> Username: mathstuf  
> Created_at: 2016-07-15 14:27:10 UTC  
> Url: https://github.com/boostorg/python/pull/10#issuecomment-232965749  

No, in response to my question as to how to run the test suite. I can't verify the test actually works on my machine.

---

## Comment 10

> Username: stefanseefeld  
> Created_at: 2016-07-15 14:34:00 UTC  
> Url: https://github.com/boostorg/python/pull/10#issuecomment-232967799  

Oh, I see. Please have a look at my recent work on a SCons-based build system. Use  
  
```  
scons config  
scons  
scons test  
```  
  
to configure, build, and test respectively. Tests are in tests/, and are added to test/SConscript.  
  
Please open a new PR where I can provide specific feedback to get this working. This (current) PR is closed.

---
