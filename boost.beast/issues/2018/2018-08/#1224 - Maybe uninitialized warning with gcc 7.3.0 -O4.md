# #1224 - Maybe uninitialized warning with gcc 7.3.0 -O4 [Closed]

> Username: azuretheseeker  
> Created at: 2018-08-12 00:23:46 UTC  
> Updated at: 2019-04-22 00:10:46 UTC  
> Closed at: 2019-04-22 00:10:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1224  

When compiling with gcc 7.3.0, -O4 results in the following warning issued:  
  
boost/boost_1_67_0/boost/beast/core/impl/buffers_suffix.ipp:57:31: warning: ‘*((void*)& iter +16)’ may be used uninitialized in this function [-Wmaybe-uninitialized]  
         return b_ == other.b_ && it_ == other.it_;  
                ~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~  
In file included from /home/oz/download/boost/boost_1_67_0/boost/beast/core/detail/type_traits.hpp:14:0,  
                 from /home/oz/download/boost/boost_1_67_0/boost/beast/core/type_traits.hpp:15,  
                 from /home/oz/download/boost/boost_1_67_0/boost/beast/core/bind_handler.hpp:14,  
                 from /home/oz/download/boost/boost_1_67_0/boost/beast/core.hpp:15

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-08-12 02:50:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1224#issuecomment-412314740  

I am 100% certain this is a bug in gcc. I've seen it before, and I have not found a work-around. I suggest you open an issue with gcc. I will keep this open, to remind me to add some comments to that file for anyone else who gets the warning.

---

## Comment 2

> Username: djarek  
> Created at: 2019-04-20 20:22:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1224#issuecomment-485164499  

This warning doesn't reproduce with master/develop anymore, does it?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-04-22 00:10:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1224#issuecomment-485292494  

Looks fixed - thanks Damian.
