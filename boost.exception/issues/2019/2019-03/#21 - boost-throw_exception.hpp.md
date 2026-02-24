# #21 - boost/throw_exception.hpp? [Closed]

> Username: DragonOsman  
> Created at: 2019-03-06 20:51:45 UTC  
> Updated at: 2019-03-21 19:11:02 UTC  
> Closed at: 2019-03-21 19:11:02 UTC  
> Url: https://github.com/boostorg/exception/issues/21  

I have this [code](https://github.com/DragonOsman/currency_converter/blob/master/currency_converter.cpp) on GitHub that uses Boost.Beast, and it requires boost/throw_exception.hpp.  What version of Boost.Exception has that?  I'm trying to use the Boost libraries on GitHub rather than the ones that come with Boost 1.69.0 so I can avoid some clashes between the latest version of Boost.Beast on GitHub and the one in Boost 1.69.0.

---

## Comment 1

> Username: zajo  
> Created at: 2019-03-08 20:02:02 UTC  
> Url: https://github.com/boostorg/exception/issues/21#issuecomment-471058206  

`throw_exception.hpp` is special: logically, it is part of Boost Exception, but physically it lives in a separate repo: https://github.com/boostorg/throw_exception. That is all you need if your code does not use Boost Exception; if it does, then you also need this repo (`boost/exception`).
