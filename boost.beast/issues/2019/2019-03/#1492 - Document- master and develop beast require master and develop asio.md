# #1492 - Document: master and develop beast require master and develop asio [Closed]

> Username: xinthose  
> Created at: 2019-03-01 22:40:38 UTC  
> Updated at: 2022-09-24 06:04:40 UTC  
> Closed at: 2022-09-24 06:04:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1492  

OS: Ubuntu 18.04 LTS  
Version: latest  
  
I am having compile issues.  Don't you just copy the boost folder to your boost folder?  
  
`git clone https://github.com/boostorg/beast.git`  
`cp -r beast/include/boost/* /usr/include/boost`  
  
Then I get compile errors like this:  
```  
/usr/include/boost/beast/core/detail/stream_base.hpp:15:10: fatal error: boost/core/exchange.hpp: No such file or directory  
 #include <boost/core/exchange.hpp>  
```  
I installed boost like this: `apt install libboost-all-dev`.  
This command: `locate exchange.hpp` yields this: `/usr/include/boost/context/detail/exchange.hpp`  
  
How do I get this file: `boost/core/exchange.hpp`?  Is it outside of the .deb package?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-03-01 22:41:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1492#issuecomment-468836492  

If you want to use the **develop** or **master** branch of Beast (as it seems like you are doing in your git command) then you also need the develop or master branch of Boost.

---

## Comment 2

> Username: xinthose  
> Created at: 2019-03-01 22:51:40 UTC  
> Updated at: 2019-03-01 22:52:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1492#issuecomment-468838692  

OK.  The current version of [`libboost-all-dev`](https://packages.ubuntu.com/bionic/libboost-all-dev) on Bionic (18.04) is 1.65.1.  So, I need to upgrade to at least `1.66.0`?  You do not have any releases before this version?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-03-01 23:41:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1492#issuecomment-468850168  

The first version of Boost in which Beast appears is Boost 1.66.0. However, it is suggested that you use the latest version, which contains bug fixes and security fixes.

---

## Comment 4

> Username: xinthose  
> Created at: 2019-03-03 01:12:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1492#issuecomment-468976336  

OK.  I see.  Thank you sir.  Perhaps a note in Requirements should say as such.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-03-03 01:13:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1492#issuecomment-468976408  

Okay
