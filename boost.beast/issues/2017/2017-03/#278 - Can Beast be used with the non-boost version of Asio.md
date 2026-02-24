# #278 - Can Beast be used with the non-boost version of Asio? [Closed]

> Username: crujose  
> Created at: 2017-03-08 18:02:14 UTC  
> Updated at: 2017-03-21 20:17:59 UTC  
> Closed at: 2017-03-21 20:17:58 UTC  
> Url: https://github.com/boostorg/beast/issues/278  

http://think-async.com/Asio/AsioAndBoostAsio  
https://github.com/chriskohlhoff/asio  
  
I see there are some direct includes of boost headers (e.g. https://github.com/vinniefalco/Beast/blob/master/include/beast/core/error.hpp#L11 ) but I was wondering how much of a rabbit hole it would be to carve out the dependency on boost.

---

## Comment 1

> Username: djarek  
> Created at: 2017-03-08 19:06:27 UTC  
> Url: https://github.com/boostorg/beast/issues/278#issuecomment-285136252  

@crujose   
I believe there are some classes that would have to be replaced without boost (optional/tribool, don't remember which one).

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-03-09 12:53:07 UTC  
> Url: https://github.com/boostorg/beast/issues/278#issuecomment-285343364  

Beast uses other parts of boost such as Boost.Intrusive, a smattering of utilities. It is in theory possible. But I don't have the resources to maintain a standalone-Asio fork of Beast. And I am trying to get Beast into boost - if Beast passes a formal review then my workload will probably triple!

---

## Comment 3

> Username: crujose  
> Created at: 2017-03-09 20:12:42 UTC  
> Url: https://github.com/boostorg/beast/issues/278#issuecomment-285467417  

Fair enough!  I was mostly hoping for a header-only option that didn't need additional linking which is almost the case here except for boost-system.  I'm probably being too nit-picky though.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-03-09 23:09:57 UTC  
> Updated at: 2017-03-09 23:10:04 UTC  
> Url: https://github.com/boostorg/beast/issues/278#issuecomment-285514185  

I believe there is a way to eliminate the boost-system link dependency, there's a macro for it, try looking through the Beast issues.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-03-10 12:06:37 UTC  
> Url: https://github.com/boostorg/beast/issues/278#issuecomment-285653061  

I found it! https://github.com/vinniefalco/Beast/issues/231

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-03-21 20:17:58 UTC  
> Url: https://github.com/boostorg/beast/issues/278#issuecomment-288205081  

Since there's nothing for me to do here yet, I'm going to close it - thanks!
