# #135 Fix `BOOST_LIB_NAME` for Python 3 [Merged]

> Username: jakirkham  
> Created at: 2017-06-10 22:23:28 UTC  
> Updated at: 2018-10-19 14:13:58 UTC  
> Merged at: 2017-06-12 13:32:32 UTC  
> Closed at: 2017-06-12 13:32:33 UTC  
> Url: https://github.com/boostorg/python/pull/135  

Fixes https://github.com/boostorg/python/issues/127  
  
This was reusing the Python 2 name on Python 3, which is incorrect since the Python 3 library for Boost.Python now has a `3` in it. Hence this checks against the Python version and defines this correctly.

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2017-06-11 02:57:10 UTC  
> Url: https://github.com/boostorg/python/pull/135#issuecomment-307602842  

As I just explained in https://github.com/boostorg/python/issues/127, I'm not comfortable with the idea that the library name is hardcoded like this into the code. The build system is a better place for this. For that reason I'd favour a making the hardcoded name a default only (i.e. prefixed witth `#ifndef BOOST_LIB_NAME ...`), and then add the above logic to the Jamfiles and wherever else the library name is being defined.

---
