# #19 - fallback to std::call_once if exists [Closed]

> Username: dmorilha  
> Created at: 2015-01-14 11:11:14 UTC  
> Updated at: 2017-07-31 19:55:10 UTC  
> Closed at: 2017-07-31 19:55:10 UTC  
> Url: https://github.com/boostorg/coroutine/issues/19  

Hi, could we fallback to std::call_once[1](http://en.cppreference.com/w/cpp/thread/call_once) in case it exists? That would eliminate the boost_thread requirement when compiling with C++11 (or above) and reduce complexity.

---

## Comment 1

> Username: dmorilha  
> Created at: 2015-01-14 11:47:16 UTC  
> Url: https://github.com/boostorg/coroutine/issues/19#issuecomment-69905297  

I've created this patch: https://gist.github.com/dmorilha/10ab1090d0f374c75902

---

## Comment 2

> Username: olk  
> Created at: 2017-07-31 19:55:10 UTC  
> Url: https://github.com/boostorg/coroutine/issues/19#issuecomment-319177955  

should already be fixed - linking against boost.thread only if std::call_once is not supported
